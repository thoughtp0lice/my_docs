# Task 1 - Palindrome Pairs

## C++ Source Code

```c++
#include <bits/stdc++.h>
using namespace std;
using namespace std;
int change(int mask, int bit) { return mask ^ (1 << bit); }
const int N = 2e5 + 5;
int n, h[N];
map<int, int> cnt;
string str;
long long ans = 0;
int main() {
  ios_base::sync_with_stdio(0);
  cin.tie(0);
  cin >> n;
  for (int i = 0; i < n; ++i) {
    cin >> str;
    for (unsigned char c : str) {
      int k = (int)c;
      h[i] = change(h[i], k);
    }
    ans += cnt[h[i]];
    for (int k = 0; k < 32; ++k) {
      int f = change(h[i], k);
      ans += cnt[f];
    }
    cnt[h[i]]++;
  }
  cout << ans << endl;
  return 0;
}
```

## Example GPT-3.5-turbo Attempt

```rust
use std::collections::HashMap;
use std::io;

fn change(mask: i32, bit: usize) -> i32 {
    mask ^ (1 << bit)
}

const N: usize = 200005;

fn main() {
    let mut cnt: HashMap<i32, i32> = HashMap::new();
    let mut h: [i32; N] = [0; N];
    let mut ans: i64 = 0;

    let mut input = String::new();
    io::stdin().read_line(&mut input).unwrap();
    let n: usize = input.trim().parse().unwrap();

    for _ in 0..n {
        input.clear();
        io::stdin().read_line(&mut input).unwrap();
        let str = input.trim();

        for c in str.chars() {
            let k = c as i32;
            h[i] = change(h[i], k);
        }
        ans += cnt[&h[i]];
        for k in 0..32 {
            let f = change(h[i], k);
            ans += cnt[&f];
        }
        *cnt.entry(h[i]).or_insert(0) += 1;
    }
    println!("{}", ans);
}
```

### Note

- Global Variable Missing
- Bit Shift Overflow

## IO Description

[IO Description]

Input

The following will be given to the program through stdin: a number `n` then `n` strings containing only lower case English charater (from `a` to `z`)

Output

Output one number, representing how many palindrome pairs there are in the array.

[IO Examples]

Input

3 
aa 
bb 
cd

Output

1

Input

6 
aab 
abcac 
dffe 
ed 
aa 
aade

Output

6

## GPT-4 Summary

The C++ program is designed to count the number of palindrome pairs in an array of strings. A palindrome pair is a pair of strings that can be concatenated and permuted to form a palindrome. Let's update our understanding of the program with this new information:

1. `change(int mask, int bit)` function:
     This function is used to toggle the `bit`-th bit in the `mask` representing the frequency parity of characters in a string. If a character appears an even number of times, its corresponding bit remains 0; otherwise, it becomes 1.

2. `N` constant:
     `N` represents the maximum number of strings that can be stored in the array `h`.

3. Variables:
     - `n`: The number of strings to be read.
     - `h`: An array where `h[i]` stores the bit mask representing the parity of character frequencies in the `i`-th string.
     - `cnt`: A map that counts the occurrences of each bit mask.
     - `str`: A string variable to store the input strings.
     - `ans`: A long long integer that will hold the total number of palindrome pairs.

4. The `main()` function processes the input and counts palindrome pairs:
     - It reads the number of strings `n`.
     - For each string `str`:
         - It computes a bit mask `h[i]` representing the parity of character frequencies. Each bit in the mask corresponds to a character in the alphabet.
         - It increments `ans` by the count of the current mask in `cnt`, since a palindrome can be formed if all characters have even frequency or exactly one character has an odd frequency.
         - It also checks for the possibility of forming a palindrome by changing the frequency parity of each character (i.e., flipping each bit in the mask). This accounts for the case where a palindrome can be formed if exactly one character has an odd frequency.
         - Finally, it increments the count of the current mask in `cnt`.
     - After processing all strings, the program outputs the total number of palindrome pairs found.

The key to solving this problem is to realize that a palindrome has an even number of occurrences for each character, except for at most one character which can appear an odd number of times. The bit mask is a clever way to keep track of this parity. If two strings can form a palindrome pair, their concatenated bit mask will have all bits set to 0 (even number of occurrences), or exactly one bit set to 1 (one character with an odd number of occurrences). The program counts all such cases to find the total number of palindrome pairs.

## Task Description From Code Contest

After learning a lot about space exploration, a little girl named Ana wants to change the subject.

Ana is a girl who loves palindromes (string that can be read the same backwards as forward). She has learned how to check for a given string whether it's a palindrome or not, but soon she grew tired of this problem, so she came up with a more interesting one and she needs your help to solve it:

You are given an array of strings which consist of only small letters of the alphabet. Your task is to find how many palindrome pairs are there in the array. A palindrome pair is a pair of strings such that the following condition holds: at least one permutation of the concatenation of the two strings is a palindrome. In other words, if you have two strings, let's say "aab" and "abcac", and you concatenate them into "aababcac", we have to check if there exists a permutation of this new string such that it is a palindrome (in this case there exists the permutation "aabccbaa").

Two pairs are considered different if the strings are located on different indices. The pair of strings with indices (i,j) is considered the same as the pair (j,i).

Input

The first line contains a positive integer N (1 ≤ N ≤ 100 000), representing the length of the input array.

Eacg of the next N lines contains a string (consisting of lowercase English letters from 'a' to 'z') — an element of the input array.

The total number of characters in the input array will be less than 1 000 000.

Output

Output one number, representing how many palindrome pairs there are in the array.

Examples

Input

3 aa bb cd

Output

1

Input

6 aab abcac dffe ed aa aade

Output

6