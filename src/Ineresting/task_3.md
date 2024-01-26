# Task 3 - Dynamic Range Update and Query

## C++ Source Code

```c++
#include <bits/stdc++.h>
const int N = 1 << 20;
int n, m, a[N], c[N], tag[N << 2], cnt[N << 2];
void update(int p, int l, int r) {
  if (l == r)
    cnt[p] = !!c[l] * !tag[p];
  else
    cnt[p] = (cnt[p << 1] + cnt[p << 1 | 1]) * !tag[p];
}
void modify(int p, int L, int R, int l, int r, int x) {
  if (L == l && R == r) return tag[p] += x, update(p, L, R);
  int mid = L + R >> 1;
  if (l > mid)
    modify(p << 1 | 1, mid + 1, R, l, r, x);
  else if (r <= mid)
    modify(p << 1, L, mid, l, r, x);
  else
    modify(p << 1, L, mid, l, mid, x),
        modify(p << 1 | 1, mid + 1, R, mid + 1, r, x);
  update(p, L, R);
}
void settle(int x, int w) {
  if (a[x] > a[x - 1]) modify(1, 1, N, a[x - 1] + 1, a[x], w);
}
void init(int x, int w) { settle(x, w), settle(x + 1, w); }
void ins(int x, int w) { c[x] += w, modify(1, 1, N, x, x, 0); }
int main() {
  scanf("%d%d", &n, &m), a[0] = N;
  for (int i = 1; i <= n; i++) scanf("%d", a + i), ins(a[i], 1), settle(i, 1);
  for (int x, y; m--;) {
    scanf("%d%d", &x, &y), init(x, -1), ins(a[x], -1);
    ins(a[x] = y, 1), init(x, 1), printf("%d\n", cnt[1]);
  }
  return 0;
}

```

## GPT-4 Attempt Example 
```rust
const N: usize = 1 << 20;
static mut A: [usize; N] = [0; N];
static mut C: [usize; N] = [0; N];
static mut TAG: [usize; N << 2] = [0; N << 2];
static mut CNT: [usize; N << 2] = [0; N << 2];

fn update(p: usize, l: usize, r: usize) {
    unsafe {
        if l == r {
            CNT[p] = !!C[l] * !TAG[p];
        } else {
            CNT[p] = (CNT[p << 1] + CNT[p << 1 | 1]) * !TAG[p];
        }
    }
}

fn modify(p: usize, l: usize, r: usize, x: usize, y: usize, z: usize) {
    unsafe {
        if l == x && r == y {
            TAG[p] += z;
            update(p, l, r);
            return;
        }
        let mid = l + r >> 1;
        if x > mid {
            modify(p << 1 | 1, mid + 1, r, x, y, z);
        } else if y <= mid {
            modify(p << 1, l, mid, x, y, z);
        } else {
            modify(p << 1, l, mid, x, mid, z);
            modify(p << 1 | 1, mid + 1, r, mid + 1, y, z);
        }
        update(p, l, r);
    }
}

fn settle(x: usize, w: usize) {
    unsafe {
        if A[x] > A[x - 1] { modify(1, 1, N, A[x - 1] + 1, A[x], w); }
    }
}

fn init(x: usize, w: usize) {
    settle(x, w);
    settle(x + 1, w);
}

fn ins(x: usize, w: usize) {
    unsafe {
        C[x] += w;
        modify(1, 1, N, x, x, 0);
    }
}

fn main() {
    let mut s = String::new();
    std::io::stdin().read_line(&mut s).unwrap();
    let mut it = s.trim().split_whitespace();
    let n: usize = it.next().unwrap().parse().unwrap();
    let mut m: usize = it.next().unwrap().parse().unwrap();
    unsafe {
        A[0] = N;
        for i in 1..=n {
            let mut s = String::new();
            std::io::stdin().read_line(&mut s).unwrap();
            let a_i: usize = s.trim().parse().unwrap();
            A[i] = a_i;
            ins(A[i], 1);
            settle(i, 1);
        }
        while m > 0 {
            let mut s = String::new();
            std::io::stdin().read_line(&mut s).unwrap();
            let it = s.trim().split_whitespace();
            let x: usize = it.next().unwrap().parse().unwrap();
            let y: usize = it.next().unwrap().parse().unwrap();
            init(x, -1);
            ins(A[x], -1);
            ins(A[x], y);
            init(x, 1);
            println!("{}", CNT[1]);
            m -= 1;
        }
    }
}

```

### Note

- i32, usize errors Everywhere
- `mut` varaible errors

## IO Description

[IO Description]

Input

The first line contains two integers n and q (1 ≤ n, q ≤ 5 ⋅ 10^5)

The second line contains n integers a_1, a_2, ..., a_n (1 ≤ a_i ≤ 10^6)

Each of the next q lines contains two integers pos and x (1 ≤ pos ≤ n, 1 ≤ x ≤ 10^6, x ≠ a_{pos})

[IO Examples]

Output

q integers

Example

Input

5 3 
50 40 30 20 10 
1 25 
3 45 
1 48

Output

3 3 4

### GPT-4 Summary

This C++ program implements a data structure to handle a specific type of dynamic range update and query problem. It uses a segment tree with lazy propagation to efficiently manage intervals and answer queries about them. The program seems to be managing a sequence of numbers and performing updates based on certain conditions.

Here's a breakdown of the key components of the program:

1. Global variables:
   - `n`, `m`: Represent the number of elements in the sequence and the number of operations, respectively.
   - `a`: An array to store the elements of the sequence.
   - `c`: A count array to keep track of the occurrences of each value within the sequence.
   - `tag`: An array used as part of the lazy propagation in the segment tree to store updates that need to be applied to children nodes later.
   - `cnt`: An array to store the number of elements within a segment that meet certain criteria (in this case, non-zero and not tagged).

2. `update(int p, int l, int r)`:
   This function updates the count of a segment in the segment tree. If the segment corresponds to a single element (`l == r`), it sets `cnt[p]` based on the presence of the value and the absence of a tag. Otherwise, it calculates the count based on the sum of the counts of its children, factoring in the tag.

3. `modify(int p, int L, int R, int l, int r, int x)`:
   This function applies an update to a range `[l, r]` in the segment tree. If the current segment `[L, R]` matches the update range, it modifies the tag and updates the count. If not, it splits the range and recursively updates the appropriate child segments. After updating the children, it calls `update` to recalculate the count for the current segment.

4. `settle(int x, int w)`:
   This function manages the updates when the value of `a[x]` changes. It modifies the segment tree to reflect the new count for the ranges affected by the change.

5. `init(int x, int w)`:
   This function calls `settle` for the positions `x` and `x + 1` to initialize or update the segment tree after a change in `a[x]`.

6. `ins(int x, int w)`:
   This function updates the count array `c` for a specific value `x` and ensures that the segment tree reflects this update.

7. `main` function:
   - The program reads the initial sequence of numbers and initializes the segment tree with `ins` and `settle` function calls.
   - It processes `m` operations, each consisting of changing the value of a single element in the sequence. After each operation, it updates the necessary parts of the segment tree with `init` and `ins` and prints the current count of the entire sequence `cnt[1]`, which is the root of the segment tree.

Overall, the program maintains a sequence of numbers and efficiently handles updates and queries to provide the count of elements meeting certain criteria in the sequence after each operation. This is a classic example of the usage of segment trees with lazy propagation in competitive programming.

## Description From Dataset

Suppose that we have an array of n distinct numbers a_1, a_2, ..., a_n. Let's build a graph on n vertices as follows: for every pair of vertices i < j let's connect i and j with an edge, if a_i < a_j. Let's define weight of the array to be the number of connected components in this graph. For example, weight of array [1, 4, 2] is 1, weight of array [5, 4, 3] is 3.

You have to perform q queries of the following form — change the value at some position of the array. After each operation, output the weight of the array. Updates are not independent (the change stays for the future).

Input

The first line contains two integers n and q (1 ≤ n, q ≤ 5 ⋅ 10^5) — the size of the array and the number of queries.

The second line contains n integers a_1, a_2, ..., a_n (1 ≤ a_i ≤ 10^6) — the initial array.

Each of the next q lines contains two integers pos and x (1 ≤ pos ≤ n, 1 ≤ x ≤ 10^6, x ≠ a_{pos}). It means that you have to make a_{pos}=x.

It's guaranteed that at every moment of time, all elements of the array are different.

Output

After each query, output the weight of the array.

Example

Input

5 3 50 40 30 20 10 1 25 3 45 1 48

Output

3 3 4

Note

After the first query array looks like [25, 40, 30, 20, 10], the weight is equal to 3.

After the second query array looks like [25, 40, 45, 20, 10], the weight is still equal to 3.

After the third query array looks like [48, 40, 45, 20, 10], the weight is equal to 4.