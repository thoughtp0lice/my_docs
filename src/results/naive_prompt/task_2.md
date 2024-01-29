# Task 2 Naive Result

## Prompt 

```c++
#include <bits/stdc++.h>
namespace IO {
char ibuf[(1 << 21) + 1], *iS, *iT;
char Get() {
  return (iS == iT ? (iT = (iS = ibuf) + fread(ibuf, 1, (1 << 21) + 1, stdin),
                      (iS == iT ? EOF : *iS++))
                   : *iS++);
}
int read() {
  int x = 0, c = Get();
  while (!isdigit(c)) c = Get();
  while (isdigit(c)) x = x * 10 + c - 48, c = Get();
  return x;
}
}  // namespace IO
using IO::read;
using std::pair;
using std::tuple;
using std::vector;
using i64 = long long;
const int N = 500007;
vector<pair<int, int>> e[N];
vector<tuple<int, int, int>> E;
int dep[N], p[N], fa[N];
void dfs(int u, int d) {
  dep[u] = d;
  for (auto [v, f] : e[u])
    if (!dep[v]) p[v] = u * f, dfs(v, d + 1);
}
int find(int x) { return fa[x] ? fa[x] = find(fa[x]) : x; }
int main() {
  int n = read(), k = read(), m = read(), cnt = 0;
  i64 ans = 0;
  for (int i = 1, u, v; i <= k; ++i)
    u = read(), v = read(), e[u].emplace_back(v, 1), e[v].emplace_back(u, 1),
    fa[find(u)] = find(v);
  for (int i = 1, u, v, fu, fv; i <= m; ++i)
    if ((fu = find(u = read())) ^ (fv = find(v = read())))
      read(), e[u].emplace_back(v, -1), e[v].emplace_back(u, -1), fa[fu] = fv;
    else
      E.emplace_back(u, v, read());
  dfs(1, 1), memset(fa + 1, 0, n << 2);
  for (auto [u, v, w] : E)
    while ((u = find(u)) ^ (v = find(v))) {
      if (dep[u] < dep[v]) std::swap(u, v);
      if (p[u] > 0) ++cnt, ans += w;
      fa[u] = abs(p[u]);
    }
  cnt < k ? puts("-1") : printf("%lld", ans);
}
```

## GPT-4-0125-preview Result

Temperature = 0.8 

### Attempt 1

**Summary**: Compile fine, find func wrong, [also read wrong]

```rust
use std::collections::VecDeque;
use std::io::{self, Read};

fn read() -> i32 {
    let stdin = io::stdin();
    let mut it = stdin.bytes().map(|b| b.unwrap());
    let mut n = 0;
    let mut c = it.next().unwrap();
    while !c.is_ascii_digit() {
        c = it.next().unwrap();
    }
    while c.is_ascii_digit() {
        n = n * 10 + (c as i32 - '0' as i32);
        c = it.next().unwrap();
    }
    n
}

const N: usize = 500007;
type Pair = (usize, i32);
type Tuple = (usize, usize, i64);

fn dfs(u: usize, d: i32, e: &Vec<Vec<Pair>>, dep: &mut Vec<i32>, p: &mut Vec<i32>) {
    dep[u] = d;
    for &(v, f) in &e[u] {
        if dep[v] == 0 {
            p[v] = u as i32 * f;
            dfs(v, d + 1, e, dep, p);
        }
    }
}

fn find(x: usize, fa: &mut Vec<usize>) -> usize {
    if fa[x] != 0 {
        fa[x] = find(fa[x], fa);
    }
    fa[x]
}

fn main() {
    let n = read() as usize;
    let k = read() as usize;
    let m = read() as usize;

    let mut e: Vec<Vec<Pair>> = vec![Vec::new(); N];
    let mut E: Vec<Tuple> = Vec::new();
    let mut dep = vec![0; N];
    let mut p = vec![0; N];
    let mut fa = vec![0; N];
    let mut ans = 0i64;
    let mut cnt = 0;

    for _ in 0..k {
        let u = read() as usize;
        let v = read() as usize;
        e[u].push((v, 1));
        e[v].push((u, 1));
        let fu = find(u, &mut fa);
        let fv = find(v, &mut fa);
        fa[fu] = fv;
    }

    for _ in 0..m {
        let u = read() as usize;
        let v = read() as usize;
        let fu = find(u, &mut fa);
        let fv = find(v, &mut fa);
        if fu != fv {
            read(); // Skip the weight as it's not used here
            e[u].push((v, -1));
            e[v].push((u, -1));
            fa[fu] = fv;
        } else {
            let w = read() as i64;
            E.push((u, v, w));
        }
    }

    dfs(1, 1, &e, &mut dep, &mut p);
    fa.fill(0);

    for &(u, v, w) in &E {
        let (mut u, mut v) = (u, v);
        while {
            u = find(u, &mut fa);
            v = find(v, &mut fa);
            u != v
        } {
            if dep[u] < dep[v] {
                std::mem::swap(&mut u, &mut v);
            }
            if p[u] > 0 {
                cnt += 1;
                ans += w;
            }
            fa[u] = p[u].abs() as usize;
        }
    }

    if cnt < k {
        println!("-1");
    } else {
        println!("{}", ans);
    }
}
```

### Attempt 2

**Summary**: 
- Missing global var N
- e size wrong
- e type wrong
- p type wrong
- find func wrong
- [io wrong]

```rust
use std::io::{self, Read};
use std::collections::VecDeque;

fn read() -> usize {
    let mut input = String::new();
    io::stdin().read_to_string(&mut input).unwrap();
    let mut numbers = input.split_ascii_whitespace().map(|n| n.parse::<usize>().unwrap());
    numbers.next().unwrap()
}

fn find(x: usize, parents: &mut Vec<usize>) -> usize {
    if parents[x] != x {
        parents[x] = find(parents[x], parents);
    }
    parents[x]
}

fn main() {
    let n = read();
    let k = read();
    let m = read();
    let mut e = vec![Vec::new(); n + 1];
    let mut parents = (0..=n).collect::<Vec<_>>();
    let mut edges = Vec::new();
    let mut dep = vec![0; n + 1];
    let mut p = vec![0; n + 1];
    let mut ans: i64 = 0;
    let mut cnt = 0;

    for _ in 0..k {
        let u = read();
        let v = read();
        e[u].push((v, 1));
        e[v].push((u, 1));
        let fu = find(u, &mut parents);
        let fv = find(v, &mut parents);
        if fu != fv {
            parents[fu] = fv;
        }
    }

    for _ in 0..m {
        let u = read();
        let v = read();
        let w = read() as i64;
        let fu = find(u, &mut parents);
        let fv = find(v, &mut parents);
        if fu != fv {
            e[u].push((v, -1));
            e[v].push((u, -1));
            parents[fu] = fv;
        } else {
            edges.push((u, v, w));
        }
    }

    let mut queue = VecDeque::new();
    queue.push_back(1);
    dep[1] = 1;
    while let Some(u) = queue.pop_front() {
        for &(v, f) in &e[u] {
            if dep[v] == 0 {
                p[v] = u * f as usize;
                dep[v] = dep[u] + 1;
                queue.push_back(v);
            }
        }
    }

    parents = (0..=n).collect::<Vec<_>>();
    for (u, v, w) in edges {
        let (mut u, mut v) = (u, v);
        while find(u, &mut parents) != find(v, &mut parents) {
            if dep[u] < dep[v] {
                std::mem::swap(&mut u, &mut v);
            }
            if p[u] > 0 {
                cnt += 1;
                ans += w;
            }
            parents[find(u, &mut parents)] = find(p[u].abs() as usize, &mut parents);
        }
    }

    if cnt < k {
        println!("-1");
    } else {
        println!("{}", ans);
    }
}
```

### Attempt 3

**Summary**: 
- read mostly right
- Missing N, so all vec size wrong
- e type wrong
- `ans += w` missing conversion
- `find` still wrong
- `p` type wrong

```rust
use std::io::{self, Read};
use std::collections::VecDeque;

fn read_ints() -> Vec<i32> {
    let mut input = String::new();
    io::stdin().read_to_string(&mut input).unwrap();
    input.split_whitespace().map(|x| x.parse().unwrap()).collect()
}

fn find(x: usize, fa: &mut Vec<usize>) -> usize {
    if fa[x] != x { fa[x] = find(fa[x], fa); }
    fa[x]
}

fn main() {
    let nums = read_ints();
    let mut iter = nums.iter();
    let n = *iter.next().unwrap() as usize;
    let k = *iter.next().unwrap() as usize;
    let m = *iter.next().unwrap() as usize;

    let mut e = vec![Vec::new(); n + 1];
    let mut fa = (0..=n).collect::<Vec<_>>();
    let mut E = Vec::new();
    let mut ans = 0_i64;
    let mut cnt = 0;

    for _ in 0..k {
        let u = *iter.next().unwrap() as usize;
        let v = *iter.next().unwrap() as usize;
        e[u].push((v, 1));
        e[v].push((u, 1));
        let fu = find(u, &mut fa);
        let fv = find(v, &mut fa);
        fa[fu] = fv;
    }

    for _ in 0..m {
        let u = *iter.next().unwrap() as usize;
        let v = *iter.next().unwrap() as usize;
        let w = *iter.next().unwrap();
        let fu = find(u, &mut fa);
        let fv = find(v, &mut fa);
        if fu != fv {
            e[u].push((v, -1));
            e[v].push((u, -1));
            fa[fu] = fv;
        } else {
            E.push((u, v, w));
        }
    }

    let mut dep = vec![0; n + 1];
    let mut p = vec![0; n + 1];
    fa = (0..=n).collect::<Vec<_>>();

    let mut queue = VecDeque::new();
    queue.push_back(1);
    dep[1] = 1;

    while let Some(u) = queue.pop_front() {
        for &(v, f) in &e[u] {
            if dep[v] == 0 {
                dep[v] = dep[u] + 1;
                p[v] = u * f as usize;
                queue.push_back(v);
            }
        }
    }

    for (u, v, w) in E {
        let (mut u, mut v) = (u, v);
        while find(u, &mut fa) != find(v, &mut fa) {
            if dep[u] < dep[v] { std::mem::swap(&mut u, &mut v); }
            if p[u] > 0 { cnt += 1; ans += w; }
            fa[find(u, &mut fa)] = find(p[u].abs() as usize, &mut fa);
        }
    }

    if cnt < k {
        println!("-1");
    } else {
        println!("{}", ans);
    }
}

```

### Attempt 4

**Summary**
- only last while loop wrong

```rust
use std::io::{self, Read};
use std::collections::VecDeque;

fn read_ints() -> Vec<i32> {
    let mut input = String::new();
    io::stdin().read_to_string(&mut input).unwrap();
    input.split_whitespace().map(|x| x.parse().unwrap()).collect()
}

fn main() {
    let inputs = read_ints();
    let mut iter = inputs.iter();
    let n = *iter.next().unwrap() as usize;
    let k = *iter.next().unwrap() as usize;
    let m = *iter.next().unwrap() as usize;

    let mut e: Vec<Vec<(usize, i32)>> = vec![vec![]; n + 1];
    let mut fa: Vec<usize> = vec![0; n + 1];
    let mut cnt = 0;
    let mut ans: i64 = 0;
    let mut edges: VecDeque<(usize, usize, i32)> = VecDeque::new();

    for _ in 0..k {
        let u = *iter.next().unwrap() as usize;
        let v = *iter.next().unwrap() as usize;
        e[u].push((v, 1));
        e[v].push((u, 1));
        let fu = find(u, &mut fa);
        let fv = find(v, &mut fa);
        fa[fu] = fv;
    }

    for _ in 0..m {
        let u = *iter.next().unwrap() as usize;
        let v = *iter.next().unwrap() as usize;
        let w = *iter.next().unwrap();
        let fu = find(u, &mut fa);
        let fv = find(v, &mut fa);
        if fu != fv {
            e[u].push((v, -1));
            e[v].push((u, -1));
            fa[fu] = fv;
        } else {
            edges.push_back((u, v, w));
        }
    }

    let mut dep = vec![0; n + 1];
    let mut p = vec![0; n + 1];
    dfs(1, 1, &e, &mut dep, &mut p);

    for fa in &mut fa[1..] {
        *fa = 0;
    }

    for (mut u, mut v, w) in edges {
        while find(u, &mut fa) != find(v, &mut fa) {
            if dep[u] < dep[v] {
                std::mem::swap(&mut u, &mut v);
            }
            if p[u] > 0 {
                cnt += 1;
                ans += w as i64;
            }
            fa[u] = p[u].abs() as usize;
            u = fa[u];
        }
    }

    if cnt < k {
        println!("-1");
    } else {
        println!("{}", ans);
    }
}

fn find(mut x: usize, fa: &mut Vec<usize>) -> usize {
    while fa[x] != 0 {
        x = fa[x];
    }
    x
}

fn dfs(u: usize, d: i32, e: &Vec<Vec<(usize, i32)>>, dep: &mut Vec<i32>, p: &mut Vec<i32>) {
    dep[u] = d;
    for &(v, f) in &e[u] {
        if dep[v] == 0 {
            p[v] = u as i32 * f;
            dfs(v, d + 1, e, dep, p);
        }
    }
}
```