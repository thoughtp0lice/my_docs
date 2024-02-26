# Runtime Error Analysis

## Task 13

```c++
#include <bits/stdc++.h>
using namespace std;
const int INF = numeric_limits<int>::max();
int main() {
  ios_base::sync_with_stdio(false);
  cin.tie(NULL);
  int n;
  cin >> n;
  vector<int> val(n);
  vector<pair<int, int> > g(n);
  for (int i = 0, v, l, r; i < n; i++) {
    cin >> v >> l >> r;
    val[i] = v;
    g[i] = {l - 1, r - 1};
  }
  set<int> works;
  function<void(int, int, int)> dfs = [&](int u, int l, int r) {
    if (u == -2) return;
    if (l > val[u] and r < val[u]) works.insert(val[u]);
    dfs(g[u].first, min(l, val[u]), r);
    dfs(g[u].second, l, max(val[u], r));
  };
  vector<bool> is_root(n, true);
  for (auto &x : g) {
    if (x.first != -2) is_root[x.first] = false;
    if (x.second != -2) is_root[x.second] = false;
  }
  for (int i = 0; i < n; i++) {
    if (is_root[i]) {
      dfs(i, INF, -INF);
      break;
    }
  }
  int ans = 0;
  for (auto &x : val) ans += (!works.count(x));
  cout << ans << endl;
}

```

### Attemp 1

Did not follow src

#### COT Translation

```rust
use std::collections::HashSet;
use std::io::{self, BufRead};

const INF: i32 = i32::MAX;

fn dfs(u: i32, l: i32, r: i32, g: &[(i32, i32)], val: &[i32], works: &mut HashSet<i32>) {
    if u == -2 {
        return;
    }
    if l < val[u as usize] && r > val[u as usize] {
        works.insert(val[u as usize]);
    }
    dfs(g[u as usize].0, l.min(val[u as usize]), r, g, val, works);
    dfs(g[u as usize].1, l, r.max(val[u as usize]), g, val, works);
}

fn main() {
    let stdin = io::stdin();
    let mut lines = stdin.lock().lines();
    let n: usize = lines.next().unwrap().unwrap().trim().parse().unwrap();

    let mut val = Vec::with_capacity(n);
    let mut g = Vec::with_capacity(n);
    for _ in 0..n {
        let line = lines.next().unwrap().unwrap();
        let nums: Vec<i32> = line
            .split_whitespace()
            .map(|num| num.parse().unwrap())
            .collect();
        val.push(nums[0]);
        g.push((nums[1] - 1, nums[2] - 1));
    }

    let mut works = HashSet::new();
    let mut is_root = vec![true; n];
    for &x in &g {
        if x.0 != -2 {
            is_root[x.0 as usize] = false;
        }
        if x.1 != -2 {
            is_root[x.1 as usize] = false;
        }
    }

    for i in 0..n {
        if is_root[i] {
            dfs(i as i32, INF, -INF, &g, &val, &mut works);
            break;
        }
    }
    let ans = n - works.len();
    println!("{}", ans);
}
```

#### Error

Output mismatch

1. **Line 10**: Wrong compare
   
   C++ src
   ```c++
   if (l > val[u] and r < val[u])
   ```

   Original
   ```rust
   if l < val[u as usize] && r < val[u as usize] {
   ```

   Fixed
   ```rust
   if l > val[u as usize] && r < val[u as usize] {
   ```
2. **Line 51**: Wrong counting correct
   
   C++ src
   ```c++
    int ans = 0;
    for (auto &x : val) ans += (!works.count(x));
   ```
   
   Original
   ```rust
   let ans = n - works.len();
   ```

    Fixed
    ```rust
    let mut ans: usize = 0;
    for x in val.iter() {
        ans += (!works.contains(&x)) as usize;
    }
    ```

### Attempt 2

1. Did not follow src
2. Should use usize:MAX-1 for usize

#### COT Translation

```rust
use std::collections::{HashSet, VecDeque};
use std::io::{self, BufRead};

fn dfs(u: usize, l: i32, r: i32, g: &[(i32, i32)], val: &[i32], works: &mut HashSet<i32>) {
    if u == usize::MAX {
        return;
    }
    if l < val[u] && val[u] < r {
        works.insert(val[u]);
    }
    let left = g[u].0;
    let right = g[u].1;
    if left != -1 {
        dfs(left as usize, l.min(val[u]), r, g, val, works);
    }
    if right != -1 {
        dfs(right as usize, l, r.max(val[u]), g, val, works);
    }
}

fn main() {
    let stdin = io::stdin();
    let mut lines = stdin.lock().lines();
    let n: usize = lines.next().unwrap().unwrap().trim().parse().unwrap();

    let mut val = vec![0; n];
    let mut g = vec![(-1, -1); n];
    let mut is_root = vec![true; n];

    for i in 0..n {
        let line = lines.next().unwrap().unwrap();
        let nums: Vec<i32> = line.split_whitespace().map(|num| num.parse().unwrap()).collect();
        val[i] = nums[0];
        g[i] = (nums[1], nums[2]);
    }

    // Mark non-root vertices
    for &(left, right) in &g {
        if left != -1 {
            is_root[left as usize] = false;
        }
        if right != -1 {
            is_root[right as usize] = false;
        }
    }

    let mut works = HashSet::new();
    let mut roots = VecDeque::new();

    for i in 0..n {
        if is_root[i] {
            roots.push_back(i);
            break; // Assuming only one connected component
        }
    }

    if let Some(root) = roots.pop_front() {
        dfs(root, i32::MIN, i32::MAX, &g, &val, &mut works);
    }

    let ans = val.iter().filter(|&&x| !works.contains(&x)).count();
    println!("{}", ans);
}
```

#### Error

Output mismatch

1. **Line 10**: Wrong compare
   
   C++ src
   ```c++
   if (l > val[u] and r < val[u])
   ```

   Original
   ```rust
   if l < val[u] && val[u] < r {
   ```

   Fixed
   ```rust
   if l > val[u] && r < val[u] {
   ```
2. **Line 10**: Wrong compare
   
   C++ src
   ```c++
   if (u == -2) return;
   ```

   Original
   ```rust
   if u == usize::MAX {
        return;
    }
   ```

   Fixed
   ```rust
   if u == usize::MAX - 1 {
        return;
    }
   ```
3. **Line 10**: Wrong compare
   
   C++ src
   ```c++
   if (u == -2) return;
   ```

   Original
   ```rust
   if u == usize::MAX {
        return;
    }
   ```

   Fixed
   ```rust
   if u == usize::MAX - 1 {
        return;
    }
   ```
4. **Line 34**: wrong arithmetic operation
   
   C++ src
   ```c++
   g[i] = {l - 1, r - 1};
   ```

   Original
   ```rust
   g[i] = (nums[1], nums[2]);
   ```

   Fixed
   ```rust
   g[i] = {l - 1, r - 1};
   ```
5. **Line 34**: wrong compare
   
   C++ src
   ```c++
   if (x.first != -2) is_root[x.first] = false;
   if (x.second != -2) is_root[x.second] = false;
   ```

   Original
   ```rust
   if left != -1 {
      is_root[left as usize] = false;
   }
   if right != -1 {
      is_root[right as usize] = false;
   }
   ```

   Fixed
   ```rust
   if left != -2 {
      is_root[left as usize] = false;
   }
   if right != -2 {
      is_root[right as usize] = false;
   }
   ```
6. **Line 30**: wrong function call
   
   C++ src
   ```c++
   dfs(i, INF, -INF);
   ```

   Original
   ```rust
   dfs(root, i32::MIN, i32::MAX, &g, &val, &mut works);
   ```

   Fixed
   ```rust
   dfs(root, i32::MAX, i32::MIN, &g, &val, &mut works);
   ```

## Task 51

```c++
#include <bits/stdc++.h>
using namespace std;
long long dp[1001][10010];
int c[1001];
long long cost[1001];
long long n, W, B, X;
signed main() {
  cin >> n >> W >> B >> X;
  for (int i = 1; i <= n; i++) cin >> c[i];
  for (int i = 1; i <= n; i++) cin >> cost[i];
  memset(dp, -1, sizeof(dp));
  dp[0][0] = W;
  int ans = 0;
  for (int i = 1; i <= n; i++) {
    for (int j = 0; j < 10010; j++) {
      long long max_m = W + j * B;
      for (int k = 0; k <= min(j, c[i]); k++) {
        if (dp[i - 1][j - k] == -1) continue;
        long long new_m =
            min(W + (j - k) * B, dp[i - 1][j - k] + X) - k * cost[i];
        if (new_m < 0) continue;
        dp[i][j] = max(dp[i][j], min(max_m, new_m));
        ans = max(ans, j);
        max_m += B;
      }
    }
  }
  cout << ans << endl;
  return 0;
}

```

### Attempt 1

#### Error

1. Read Wrong for c and cost, both need to start with 0 then input
2. Index access for c and cost wrong in loop, should be i - 1
3. did not add b to max_m

#### COT Translation

```rust
use std::collections::{HashSet, VecDeque};
use std::io::{self, BufRead};

fn dfs(u: usize, l: i32, r: i32, g: &[(i32, i32)], val: &[i32], works: &mut HashSet<i32>) {
    if u == usize::MAX {
        return;
    }
    if l < val[u] && val[u] < r {
        works.insert(val[u]);
    }
    let left = g[u].0;
    let right = g[u].1;
    if left != -1 {
        dfs(left as usize, l.min(val[u]), r, g, val, works);
    }
    if right != -1 {
        dfs(right as usize, l, r.max(val[u]), g, val, works);
    }
}

fn main() {
    let stdin = io::stdin();
    let mut lines = stdin.lock().lines();
    let n: usize = lines.next().unwrap().unwrap().trim().parse().unwrap();

    let mut val = vec![0; n];
    let mut g = vec![(-1, -1); n];
    let mut is_root = vec![true; n];

    for i in 0..n {
        let line = lines.next().unwrap().unwrap();
        let nums: Vec<i32> = line.split_whitespace().map(|num| num.parse().unwrap()).collect();
        val[i] = nums[0];
        g[i] = (nums[1], nums[2]);
    }

    // Mark non-root vertices
    for &(left, right) in &g {
        if left != -1 {
            is_root[left as usize] = false;
        }
        if right != -1 {
            is_root[right as usize] = false;
        }
    }

    let mut works = HashSet::new();
    let mut roots = VecDeque::new();

    for i in 0..n {
        if is_root[i] {
            roots.push_back(i);
            break; // Assuming only one connected component
        }
    }

    if let Some(root) = roots.pop_front() {
        dfs(root, i32::MIN, i32::MAX, &g, &val, &mut works);
    }

    let ans = val.iter().filter(|&&x| !works.contains(&x)).count();
    println!("{}", ans);
}
```

#### Corrected Code

```rust
use std::io;
use std::cmp::min;
use std::cmp::max;

fn main() {
    let mut input = String::new();
    io::stdin().read_line(&mut input).expect("Failed to read line");
    let parts: Vec<i64> = input.split_whitespace().map(|x| x.parse().unwrap()).collect();
    let (n, w, b, x) = (parts[0], parts[1], parts[2], parts[3]);

    let mut input = String::new();
    io::stdin().read_line(&mut input).expect("Failed to read line");
    let mut c: Vec<i64> = vec![0; 1001];
    let mut cnt = 0;
    for v in input.split_whitespace().map(|x| x.parse().unwrap()).collect::<Vec<i64>>(){
        c[cnt + 1] = v;
        cnt+=1;
    }

    let mut input = String::new();
    io::stdin().read_line(&mut input).expect("Failed to read line");
    let mut cost: Vec<i64> = vec![0; 1001];
    cnt = 0;
    for v in input.split_whitespace().map(|x| x.parse().unwrap()).collect::<Vec<i64>>(){
        cost[cnt + 1] = v;
        cnt+=1;
    }

    let mut dp = vec![vec![-1i64; 10010]; n as usize + 1];
    dp[0][0] = w;
    let mut ans = 0;
    for i in 1..=n as usize {
        for j in 0..10010 {
            let mut max_m = w + j as i64 * b;
            for k in 0..=min(j as i64, c[i]) as usize {
                if dp[i - 1][j - k] == -1 {
                    continue;
                }
                let new_m = min(w + (j - k) as i64 * b, dp[i - 1][j - k] + x) - k as i64 * cost[i];
                if new_m < 0 {
                    continue;
                }
                dp[i][j] = max(dp[i][j], min(max_m, new_m));
                ans = max(ans, j as i32);
                max_m += b;
            }
        }
    }
    println!("{}", ans);
}
```

### Attempt 2

#### Error

1. Read Wrong
2. Index access for c and cost wrong in loop, should be i - 1
3. did not add b to max_m

#### Original

```rust
use std::cmp::{max, min};
use std::io::{self, Read};

fn main() {
    let mut input = String::new();
    io::stdin().read_to_string(&mut input).unwrap();

    let mut iter = input.split_whitespace();
    let n: usize = iter.next().unwrap().parse().unwrap();
    let w: i64 = iter.next().unwrap().parse().unwrap();
    let b: i64 = iter.next().unwrap().parse().unwrap();
    let x: i64 = iter.next().unwrap().parse().unwrap();

    let c: Vec<i64> = (0..n).map(|_| iter.next().unwrap().parse().unwrap()).collect();
    let cost: Vec<i64> = (0..n).map(|_| iter.next().unwrap().parse().unwrap()).collect();

    let mut dp: Vec<Vec<i64>> = vec![vec![-1; 10010]; n + 1];
    dp[0][0] = w;
    let mut ans = 0;

    for i in 1..=n {
        for j in 0..10010 {
            if dp[i - 1][j] == -1 {
                continue;
            }
            let max_m = w + j as i64 * b;
            for k in 0..=min(j as i64, c[i - 1]) {
                if dp[i - 1][j - k as usize] == -1 {
                    continue;
                }
                let new_m = min(w + (j as i64 - k) * b, dp[i - 1][j - k as usize] + x) - k * cost[i - 1];
                if new_m < 0 {
                    continue;
                }
                dp[i][j] = max(dp[i][j], min(max_m, new_m));
                ans = max(ans, j);
            }
        }
    }

    println!("{}", ans);
}
```


#### Corrected

```rust
use std::cmp::{max, min};
use std::io::{self, Read};

fn main() {
    let mut input = String::new();
    io::stdin().read_line(&mut input).unwrap();

    let mut iter = input.split_whitespace();
    let n: usize = iter.next().unwrap().parse().unwrap();
    let w: i64 = iter.next().unwrap().parse().unwrap();
    let b: i64 = iter.next().unwrap().parse().unwrap();
    let x: i64 = iter.next().unwrap().parse().unwrap();

    io::stdin().read_line(&mut input).expect("Failed to read line");
    let mut c: Vec<i64> = vec![0; 1001];
    let mut cnt = 0;
    for v in input.split_whitespace().map(|x| x.parse().unwrap()).collect::<Vec<i64>>(){
        c[cnt + 1] = v;
        cnt+=1;
    }

    let mut input = String::new();
    io::stdin().read_line(&mut input).expect("Failed to read line");
    let mut cost: Vec<i64> = vec![0; 1001];
    cnt = 0;
    for v in input.split_whitespace().map(|x| x.parse().unwrap()).collect::<Vec<i64>>(){
        cost[cnt + 1] = v;
        cnt+=1;
    }

    let mut dp: Vec<Vec<i64>> = vec![vec![-1; 10010]; n + 1];
    dp[0][0] = w;
    let mut ans = 0;

    for i in 1..=n {
        for j in 0..10010 {
            let mut max_m = w + j as i64 * b;
            for k in 0..=min(j as i64, c[i]) {
                if dp[i - 1][j - k as usize] == -1 {
                    continue;
                }
                let new_m = min(w + (j as i64 - k) * b, dp[i - 1][j - k as usize] + x) - k * cost[i];
                if new_m < 0 {
                    continue;
                }
                dp[i][j] = max(dp[i][j], min(max_m, new_m));
                ans = max(ans, j);
                max_m += b;
            }
        }
    }

    println!("{}", ans);
}
```

### Attempt 3

#### Error

1. Read Wrong
2. Index access for c and cost wrong in loop, should be i - 1
3. did not add b to max_m

#### Original

```rust
use std::cmp::{max, min};
use std::io::{self, Read};

fn main() {
    let mut input = String::new();
    io::stdin().read_to_string(&mut input).expect("Failed to read input");

    let mut lines = input.lines();
    let first_line = lines.next().unwrap();
    let mut parts = first_line.split_whitespace();
    let n: usize = parts.next().unwrap().parse().unwrap();
    let w: i64 = parts.next().unwrap().parse().unwrap();
    let b: i64 = parts.next().unwrap().parse().unwrap();
    let x: i64 = parts.next().unwrap().parse().unwrap();

    let c: Vec<i32> = lines
        .next()
        .unwrap()
        .split_whitespace()
        .map(|number| number.parse().unwrap())
        .collect();

    let cost: Vec<i64> = lines
        .next()
        .unwrap()
        .split_whitespace()
        .map(|number| number.parse().unwrap())
        .collect();

    let mut dp = vec![vec![-1i64; 10010]; n + 1];
    dp[0][0] = w;
    let mut ans = 0;

    for i in 1..=n {
        for j in 0..10010 {
            let max_m = w + j as i64 * b;
            for k in 0..=min(j as i32, c[i - 1]) {
                if dp[i - 1][j - k as usize] == -1 {
                    continue;
                }
                let new_m = min(
                    w + (j - k as usize) as i64 * b,
                    dp[i - 1][j - k as usize] + x,
                ) - k as i64 * cost[i - 1];
                if new_m >= 0 {
                    dp[i][j] = max(dp[i][j], min(max_m, new_m));
                    ans = max(ans, j);
                }
            }
        }
    }

    println!("{}", ans);
}
```

#### Fixed

```rust
use std::cmp::{max, min};
use std::io::{self, Read};

fn main() {
    let mut input = String::new();
    io::stdin().read_to_string(&mut input).expect("Failed to read input");

    let mut lines = input.lines();
    let first_line = lines.next().unwrap();
    let mut parts = first_line.split_whitespace();
    let n: usize = parts.next().unwrap().parse().unwrap();
    let w: i64 = parts.next().unwrap().parse().unwrap();
    let b: i64 = parts.next().unwrap().parse().unwrap();
    let x: i64 = parts.next().unwrap().parse().unwrap();

    let mut c: Vec<i32> = lines
        .next()
        .unwrap()
        .split_whitespace()
        .map(|number| number.parse().unwrap())
        .collect();

    c.insert(0, 0);

    let mut cost: Vec<i64> = lines
        .next()
        .unwrap()
        .split_whitespace()
        .map(|number| number.parse().unwrap())
        .collect();
    cost.insert(0, 0);

    let mut dp = vec![vec![-1i64; 10010]; n + 1];
    dp[0][0] = w;
    let mut ans = 0;

    for i in 1..=n {
        for j in 0..10010 {
            let mut max_m = w + j as i64 * b;
            for k in 0..=min(j as i32, c[i]) {
                if dp[i - 1][j - k as usize] == -1 {
                    continue;
                }
                let new_m = min(
                    w + (j - k as usize) as i64 * b,
                    dp[i - 1][j - k as usize] + x,
                ) - k as i64 * cost[i];
                if new_m >= 0 {
                    dp[i][j] = max(dp[i][j], min(max_m, new_m));
                    ans = max(ans, j);
                }
                max_m += b;
            }
        }
    }

    println!("{}", ans);
}
```

### Attempt 4

#### Error

Literally same as above

#### Original

```rust
use std::io::{self, Read};
use std::cmp::{min, max};

fn main() {
    let mut input = String::new();
    io::stdin().read_to_string(&mut input).unwrap();

    let mut iter = input.split_whitespace();
    let n: usize = iter.next().unwrap().parse().unwrap();
    let w: i64 = iter.next().unwrap().parse().unwrap();
    let b: i64 = iter.next().unwrap().parse().unwrap();
    let x: i64 = iter.next().unwrap().parse().unwrap();

    let c: Vec<i64> = (0..n).map(|_| iter.next().unwrap().parse().unwrap()).collect();
    let cost: Vec<i64> = (0..n).map(|_| iter.next().unwrap().parse().unwrap()).collect();

    let mut dp = vec![vec![-1i64; 10010]; n + 1];
    dp[0][0] = w;
    let mut ans = 0;
    for i in 1..=n {
        for j in 0..10010 {
            if dp[i-1][j] == -1 {
                continue;
            }
            let max_m = w + j as i64 * b;
            for k in 0..=min(j as i64, c[i-1]) {
                let new_m = min(w + (j - k as usize) as i64 * b, dp[i-1][j - k as usize] + x) - k * cost[i-1];
                if new_m >= 0 {
                    dp[i][j] = max(dp[i][j], min(max_m, new_m));
                    ans = max(ans, j);
                }
            }
        }
    }
    println!("{}", ans);
}
```

#### Fixed

```rust
use std::io::{self, Read};
use std::cmp::{min, max};

fn main() {
    let mut input = String::new();
    io::stdin().read_to_string(&mut input).unwrap();

    let mut iter = input.split_whitespace();
    let n: usize = iter.next().unwrap().parse().unwrap();
    let w: i64 = iter.next().unwrap().parse().unwrap();
    let b: i64 = iter.next().unwrap().parse().unwrap();
    let x: i64 = iter.next().unwrap().parse().unwrap();

    let mut c: Vec<i64> = (0..n).map(|_| iter.next().unwrap().parse().unwrap()).collect();
    c.insert(0, 0);
    let mut cost: Vec<i64> = (0..n).map(|_| iter.next().unwrap().parse().unwrap()).collect();
    cost.insert(0, 0);

    let mut dp = vec![vec![-1i64; 10010]; n + 1];
    dp[0][0] = w;
    let mut ans = 0;
    for i in 1..=n {
        for j in 0..10010 {
            let mut max_m = w + j as i64 * b;
            for k in 0..=min(j as i64, c[i]) {
                if dp[i - 1][j - k as usize] == -1 {
                    continue;
                }
                let new_m = min(w + (j - k as usize) as i64 * b, dp[i-1][j - k as usize] + x) - k * cost[i];
                if new_m >= 0 {
                    dp[i][j] = max(dp[i][j], min(max_m, new_m));
                    ans = max(ans, j);
                }
                max_m += b;
            }
        }
    }
    println!("{}", ans);
}
```

## Task 54

All the attempts completely paraphrase the orignal program and all failed

```c++
#include <bits/stdc++.h>
using namespace std;
int nxt[200010][19 + 1];
int sum[200010][19 + 1];
int p[200010], l[200010];
int rm[200010];
int n;
namespace rmq {
int tree[200010];
int lowbit(int x) { return x & (-x); }
void add(int x, int mx) {
  while (x <= n) {
    tree[x] = max(tree[x], mx);
    x += lowbit(x);
  }
}
int get(int x) {
  int res = 0;
  while (x) {
    res = max(res, tree[x]);
    x -= lowbit(x);
  }
  return res;
}
};  // namespace rmq
int main() {
  scanf("%d", &n);
  for (int i = 1; i <= n; i++) {
    scanf("%d%d", &p[i], &l[i]);
  }
  for (int i = n; i; i--) {
    rm[i] = max(p[i] + l[i],
                rmq::get(upper_bound(p + 1, p + n + 1, p[i] + l[i]) - p - 1));
    nxt[i][0] = upper_bound(p + 1, p + 1 + n, rm[i]) - p;
    sum[i][0] = max(0, p[nxt[i][0]] - rm[i]);
    rmq::add(i, rm[i]);
  }
  nxt[n + 1][0] = n + 1;
  for (int i = 1; i < 19; i++) {
    nxt[n + 1][i] = n + 1;
    for (int j = 1; j <= n; j++) {
      nxt[j][i] = nxt[nxt[j][i - 1]][i - 1];
      sum[j][i] = sum[nxt[j][i - 1]][i - 1] + sum[j][i - 1];
    }
  }
  int m;
  scanf("%d", &m);
  for (int i = 1; i <= m; i++) {
    int x, y;
    scanf("%d%d", &x, &y);
    int ans = 0;
    for (int j = 19 - 1; j >= 0; j--) {
      if (nxt[x][j] <= y) {
        ans += sum[x][j];
        x = nxt[x][j];
      }
    }
    printf("%d\n", ans);
  }
  return 0;
}
```

### Attempt 1

#### Error

This is a complete "paraphrasing" of the original program, so won't even bother fixing.

#### Original

```rust
use std::io::{self, BufRead};
use std::cmp;

struct RMQ {
    tree: Vec<i32>,
    n: usize,
}

impl RMQ {
    fn new(size: usize) -> RMQ {
        RMQ {
            tree: vec![0; size + 1],
            n: size,
        }
    }

    fn lowbit(x: usize) -> usize {
        x & (x.wrapping_neg())
    }

    fn add(&mut self, mut x: usize, mx: i32) {
        while x <= self.n {
            self.tree[x] = cmp::max(self.tree[x], mx);
            x += RMQ::lowbit(x);
        }
    }

    fn get(&self, mut x: usize) -> i32 {
        let mut res = 0;
        while x > 0 {
            res = cmp::max(res, self.tree[x]);
            x -= RMQ::lowbit(x);
        }
        res
    }
}

fn main() {
    let stdin = io::stdin();
    let mut lines = stdin.lock().lines();
    let n: usize = lines.next().unwrap().unwrap().trim().parse().unwrap();

    let mut p = vec![0; n + 1];
    let mut l = vec![0; n + 1];
    let mut rm = vec![0; n + 1];
    let mut nxt = vec![vec![0; 20]; n + 1];
    let mut sum = vec![vec![0; 20]; n + 1];

    for i in 1..=n {
        let line = lines.next().unwrap().unwrap();
        let vals: Vec<i32> = line.split_whitespace().map(|x| x.parse().unwrap()).collect();
        p[i] = vals[0];
        l[i] = vals[1];
    }

    let mut rmq = RMQ::new(n);
    for i in (1..=n).rev() {
        let pos = match p[1..=n].binary_search(&(p[i] + l[i])) {
            Ok(pos) => pos + 1,
            Err(pos) => pos,
        };
        rm[i] = cmp::max(p[i] + l[i], rmq.get(pos));
        let nxt_pos = match p[1..=n].binary_search(&rm[i]) {
            Ok(pos) => pos + 1,
            Err(pos) => pos,
        };
        nxt[i][0] = nxt_pos;
        sum[i][0] = cmp::max(0, p.get(nxt[i][0]).unwrap_or(&0) - rm[i]);
        rmq.add(i, rm[i]);
    }

    for j in 1..20 {
        for i in 1..=n {
            let next = nxt[i][j - 1];
            nxt[i][j] = nxt[next][j - 1];
            sum[i][j] = sum[next][j - 1] + sum[i][j - 1];
        }
    }

    let m: usize = lines.next().unwrap().unwrap().trim().parse().unwrap();
    for _ in 0..m {
        let line = lines.next().unwrap().unwrap();
        let vals: Vec<usize> = line.split_whitespace().map(|x| x.parse().unwrap()).collect();
        let (mut x, y) = (vals[0], vals[1]);
        let mut ans = 0;
        for j in (0..20).rev() {
            if nxt[x][j] <= y {
                ans += sum[x][j];
                x = nxt[x][j];
            }
        }
        println!("{}", ans);
    }
}
```

## Task 99

```c++
#include <bits/stdc++.h>
using namespace std;
const int inf = (int)1e9;
const int mod = inf + 7;
const double eps = 1e-9;
const double pi = acos(-1.0);
long long read_long() {
  long long ans = 0;
  char c;
  bool started = false;
  bool is_negative = false;
  while (true) {
    c = getchar();
    if ((c < '0' || c > '9') && c != '-' && !started) continue;
    if ((c < '0' || c > '9') && c != '-' && started) break;
    if (started) {
      ans = ans * 10;
    }
    started = true;
    if (c == '-')
      is_negative = true;
    else
      ans += c - '0';
  }
  if (is_negative) ans *= -1;
  return ans;
}
int n;
char tmp[1001000];
int read() {
  scanf("%s\n", tmp);
  int sz = strlen(tmp);
  int ans = 0;
  for (int i = 0; i < sz; i++) {
    ans ^= (1 << (tmp[i] - 'a'));
  }
  return ans;
}
int main() {
  scanf("%d\n", &n);
  long long ans = 0;
  map<int, int> cnt;
  for (int i = 0; i < n; i++) {
    int x = read();
    ans += cnt[x];
    for (int j = 0; j < 26; j++) {
      int y = (x ^ (1 << j));
      ans += cnt[y];
    }
    cnt[x]++;
  }
  printf("%lld\n", ans);
  return 0;
}
```

### Attempt 1

#### Error

1. IO wrong

#### Original code

```rust
use std::collections::HashMap;
use std::io::{self, BufRead};

fn read() -> i32 {
    let mut line = String::new();
    io::stdin().lock().read_line(&mut line).unwrap();
    let line = line.trim();
    let mut ans = 0;
    for c in line.chars() {
        ans ^= 1 << (c as u8 - b'a');
    }
    ans
}

fn main() {
    let stdin = io::stdin();
    let mut lines = stdin.lock().lines();

    // Read n
    let n: usize = lines.next().unwrap().unwrap().trim().parse().unwrap();

    let mut ans: i64 = 0;
    let mut cnt: HashMap<i32, i32> = HashMap::new();

    for _ in 0..n {
        let x = read();
        ans += cnt.get(&x).cloned().unwrap_or(0) as i64;
        for j in 0..26 {
            let y = x ^ (1 << j);
            ans += cnt.get(&y).cloned().unwrap_or(0) as i64;
        }
        *cnt.entry(x).or_insert(0) += 1;
    }

    println!("{}", ans);
}
```

#### Fixed

```rust
use std::collections::HashMap;
use std::io::{self, BufRead};

fn read(line: &String) -> i32 {
    let line = line.trim();
    let mut ans = 0;
    for c in line.chars() {
        ans ^= 1 << (c as u8 - b'a');
    }
    ans
}

fn main() {
    let stdin = io::stdin();
    let mut lines = stdin.lock().lines();

    // Read n
    let n: usize = lines.next().unwrap().unwrap().trim().parse().unwrap();

    let mut ans: i64 = 0;
    let mut cnt: HashMap<i32, i32> = HashMap::new();

    for _ in 0..n {
        let x: i32 = read(&lines.next().unwrap().unwrap());
        ans += cnt.get(&x).cloned().unwrap_or(0) as i64;
        for j in 0..26 {
            let y = x ^ (1 << j);
            ans += cnt.get(&y).cloned().unwrap_or(0) as i64;
        }
        *cnt.entry(x).or_insert(0) += 1;
    }

    println!("{}", ans);
}
```

### Attempt 2



