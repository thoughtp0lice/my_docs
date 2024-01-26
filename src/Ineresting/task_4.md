# Task 4 - Combinatorial Counting

## C++ Source Code

```c++
#include <bits/stdc++.h>
using namespace std;
void Read(int &x) {
  char c;
  while (c = getchar(), c != EOF)
    if (c >= '0' && c <= '9') {
      x = c - '0';
      while (c = getchar(), c >= '0' && c <= '9') x = x * 10 + c - '0';
      ungetc(c, stdin);
      return;
    }
}
int n, d, MOD, f[1000 + 10][11], g[1000 + 10], inv[1000 + 10], ans;
int main() {
  Read(n), Read(d), Read(MOD);
  if (n <= 2) {
    puts("1");
    return 0;
  }
  if ((n - 2) % (d - 1)) {
    puts("0");
    return 0;
  }
  int i, j, p, q, w;
  inv[0] = inv[1] = 1;
  for (i = 2; i <= n; i++) inv[i] = 1ll * (MOD - MOD / i) * inv[MOD % i] % MOD;
  for (i = 2; i <= n; i++) inv[i] = 1ll * inv[i] * inv[i - 1] % MOD;
  g[1] = f[1][0] = 1;
  for (i = 1; i <= ((n - 1) >> 1); i++) {
    for (j = n - i + 1; j >= 1; j--)
      for (p = 0; p < d; p++)
        if (f[j][p]) {
          w = 1;
          for (q = 1; p + q <= d && j + q * i <= n; q++) {
            w = 1ll * w * (g[i] + q - 1) % MOD;
            f[j + q * i][p + q] =
                (f[j + q * i][p + q] + 1ll * w * inv[q] % MOD * f[j][p]) % MOD;
          }
        }
    g[i + 1] = f[i + 1][d - 1];
  }
  ans = f[n][d];
  if (!(n & 1))
    ans = (ans + g[n >> 1] + (1ll * g[n >> 1] * (g[n >> 1] - 1) / 2)) % MOD;
  printf("%d\n", ans);
}
```

## GPT-4 Attempt Example

```rust
use std::io::{self, Read};

fn read() -> i32 {
    let mut x = 0;
    let mut c: u8 = 0;
    let stdin = io::stdin();
    let mut handle = stdin.lock();
    while c < b'0' || c > b'9' {
        c = handle.bytes().next().unwrap().unwrap();
    }
    while c >= b'0' && c <= b'9' {
        x = x * 10 + (c - b'0') as i32;
        c = handle.bytes().next().unwrap().unwrap();
    }
    x
}

const N: usize = 1000 + 10;

fn main() {
    let mut n = read() as usize;
    let d = read() as usize;
    let mut mod_ = read() as usize;
    if n <= 2 {
        println!("1");
        return;
    }
    if (n - 2) % (d - 1) != 0 {
        println!("0");
        return;
    }

    let mut f = vec![vec![0; 11]; N];
    let mut g = vec![0; N];
    let mut inv = vec![0; N];
    let mut ans = 0;

    inv[0] = 1;
    inv[1] = 1;
    for i in 2..=n {
        inv[i] = (mod_ - mod_ / i as i32) * inv[mod_ as usize % i] % mod_;
    }
    for i in 2..=n {
        inv[i] = (inv[i] as i64 * inv[i - 1] as i64 % mod_ as i64) as i32;
    }
    g[1] = 1;
    f[1][0] = 1;
    for i in 1..=((n - 1) >> 1) {
        for j in (1..=n - i + 1).rev() {
            for p in 0..d {
                if f[j][p] != 0 {
                    let mut w = 1;
                    for q in 1..=d {
                        if p + q <= d && j + q * i <= n {
                            w = (w as i64 * (g[i] as i64 + q as i64 - 1) % mod_ as i64) as i32;
                            f[j + q * i][p + q] =
                                ((f[j + q * i][p + q] as i64 + w as i64 * inv[q] as i64 % mod_ as i64 * f[j][p] as i64) % mod_ as i64) as i32;
                        }
                    }
                }
            }
        }
        g[i + 1] = f[i + 1][d - 1];
    }
    ans = f[n][d];
    if n & 1 == 0 {
        ans = ((ans as i64 + g[n >> 1] as i64 + ((g[n >> 1] as i64 * (g[n >> 1] as i64 - 1)) / 2)) % mod_ as i64) as i32;
    }
    println!("{}", ans);
}
```

### Note

- Type conversion error
- Borrow and move error

## IO Description

[IO Description]

Input

The single line of the input contains three integers n, d and mod (1 ≤ n ≤ 1000, 2 ≤ d ≤ 10, 108 ≤ mod ≤ 109)

Output

One Integer

[IO Examples]

Input

5 2 433416647

Output

1

Input

10 3 409693891

Output

2

Input

65 4 177545087

Output

910726

## GPT-4 Summary

This C++ program is designed to solve a mathematical problem that possibly involves counting certain structures within a set of constraints. The program employs dynamic programming and modular arithmetic to calculate the result under a given modulus `MOD`. Here's a breakdown of the key components of the program:

1. `Read(int &x)` function:
   This is a custom input function that reads integers from the standard input. It uses `getchar()` to read characters one by one, parsing only the digits to form an integer.

2. Main program logic:
   - The program reads three integers: `n`, `d`, and `MOD`.
   - It immediately checks if `n` is less than or equal to 2, in which case the answer is 1, as there is only one possible structure.
   - It checks if `(n - 2) % (d - 1)` is non-zero. If it is, then the answer is 0 since the conditions cannot be satisfied.

3. Preparation of modular inverses:
   - The program calculates the modular inverses of all numbers up to `n` using the formula for the modular inverse in a prime field: `inv[a] = (MOD - MOD / a) * inv[MOD % a] % MOD`.
   - It also computes the factorial of the inverses in the same loop to use later in combinations.

4. Dynamic programming:
   - The program uses a 2D array `f` for dynamic programming, where `f[i][j]` represents the number of ways to build a certain structure with `i` elements and `j` parts (with specific constraints based on `d`).
   - It initializes `g[1]` and `f[1][0]` to 1, as there is one way to create a structure with one element and zero parts.
   - It then iteratively calculates the values for `g` and `f` using the previous values and the prepared modular inverses to handle combinations under the modulus.

5. Calculation of the answer:
   - After the dynamic programming loop, `g[i + 1]` is set to `f[i + 1][d - 1]`, which represents the number of ways to build the structure with `i + 1` elements and exactly `d - 1` parts.
   - The answer is retrieved from `f[n][d]`, which is the number of ways to build the structure with `n` elements and `d` parts.
   - There's a special case handled for even `n`, where additional structures are counted and added to the answer.

6. Output:
   - The program prints the calculated answer modulo `MOD`.

The program employs advanced techniques such as dynamic programming for combinatorial counting and modular arithmetic for calculations under a modulus. These techniques are typical in competitive programming problems that involve counting objects with certain properties in a combinatorial setting. The exact nature of the structures or objects being counted is not described in the code, but the logic of the program suggests that they are closely related to the values of `n` and `d`.

## Description From Dataset

A tree is a connected graph without cycles.

Two trees, consisting of n vertices each, are called isomorphic if there exists a permutation p: {1, ..., n} → {1, ..., n} such that the edge (u, v) is present in the first tree if and only if the edge (pu, pv) is present in the second tree.

Vertex of the tree is called internal if its degree is greater than or equal to two.

Count the number of different non-isomorphic trees, consisting of n vertices, such that the degree of each internal vertex is exactly d. Print the answer over the given prime modulo mod.