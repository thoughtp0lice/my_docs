# Task 1 Naive Result

## Prompt 

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

## GPT-4-0125-preview Result

Temperature = 0.8

### Attempt 1

**Result Summary**: No compile error, but bit shift overflow

```rust
use std::collections::HashMap;
use std::io::{self, BufRead};

fn change(mask: i32, bit: i32) -> i32 {
    mask ^ (1 << bit)
}

fn main() {
    let stdin = io::stdin();
    let mut lines = stdin.lock().lines();

    // Read n
    let n: usize = lines.next().unwrap().unwrap().parse().unwrap();
    
    let mut h = vec![0; n];
    let mut cnt: HashMap<i32, i64> = HashMap::new();
    let mut ans: i64 = 0;

    for i in 0..n {
        // Read string
        let str = lines.next().unwrap().unwrap();
        
        for c in str.bytes() {
            let k = c as i32;
            h[i] = change(h[i], k);
        }
        
        ans += *cnt.get(&h[i]).unwrap_or(&0);
        
        for k in 0..32 {
            let f = change(h[i], k);
            ans += *cnt.get(&f).unwrap_or(&0);
        }
        
        *cnt.entry(h[i]).or_insert(0) += 1;
    }

    println!("{}", ans);
}
```

### Attempt 2

**Result Summary**: No compile error, but bit shift overflow


```rust
use std::collections::HashMap;
use std::io::{self, BufRead};

fn change(mask: i32, bit: usize) -> i32 {
    mask ^ (1 << bit)
}

fn main() {
    let stdin = io::stdin();
    let mut lines = stdin.lock().lines();

    let n: usize = lines.next().unwrap().unwrap().trim().parse().unwrap();
    let mut h = vec![0; n];
    let mut cnt: HashMap<i32, i64> = HashMap::new();
    let mut ans: i64 = 0;

    for i in 0..n {
        let str = lines.next().unwrap().unwrap();
        for c in str.bytes() {
            let k = c as usize;
            h[i] = change(h[i], k);
        }
        ans += *cnt.get(&h[i]).unwrap_or(&0);
        for k in 0..32 {
            let f = change(h[i], k);
            ans += *cnt.get(&f).unwrap_or(&0);
        }
        *cnt.entry(h[i]).or_insert(0) += 1;
    }

    println!("{}", ans);
}
```

### Attempt 3

**Result Summary**: No compile error, but bit shift overflow


```rust
use std::collections::HashMap;
use std::io::{self, BufRead};

fn change(mask: i32, bit: usize) -> i32 {
    mask ^ (1 << bit)
}

fn main() {
    let stdin = io::stdin();
    let mut iterator = stdin.lock().lines();
    let n: usize = iterator.next().unwrap().unwrap().parse().unwrap();
    
    let mut cnt: HashMap<i32, i64> = HashMap::new();
    let mut h = vec![0; n];
    let mut ans: i64 = 0;
    
    for i in 0..n {
        let str = iterator.next().unwrap().unwrap();
        for c in str.bytes() {
            let k = c as usize;
            h[i] = change(h[i], k);
        }
        ans += cnt.get(&h[i]).unwrap_or(&0);
        for k in 0..32 {
            let f = change(h[i], k);
            ans += cnt.get(&f).unwrap_or(&0);
        }
        *cnt.entry(h[i]).or_insert(0) += 1;
    }
    
    println!("{}", ans);
}
```
