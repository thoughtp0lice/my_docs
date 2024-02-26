# Task 5

1 of 4 success

## Attempt 1

**Success?: False**

### Generated Code

**Error Type**: runtime

```rust
use std::io::{self, Read};
use std::collections::VecDeque;

// Union-Find Data Structure with Path Compression
struct UnionFind {
    parent: Vec<usize>,
}

impl UnionFind {
    fn new(size: usize) -> Self {
        UnionFind {
            parent: (0..size).collect(),
        }
    }

    fn find(&mut self, x: usize) -> usize {
        if self.parent[x] != x {
            self.parent[x] = self.find(self.parent[x]);
        }
        self.parent[x]
    }

    fn union(&mut self, x: usize, y: usize) {
        let x_root = self.find(x);
        let y_root = self.find(y);
        if x_root != y_root {
            self.parent[x_root] = y_root;
        }
    }
}

fn dfs(graph: &Vec<Vec<(usize, i64)>>, node: usize, depth: &mut Vec<usize>, parent: &mut Vec<usize>, values: &mut Vec<i64>, d: usize) {
    depth[node] = d;
    for &(next, value) in &graph[node] {
        if depth[next] == 0 {
            parent[next] = node;
            values[next] = value;
            dfs(graph, next, depth, parent, values, d + 1);
        }
    }
}

fn main() {
    let mut input = String::new();
    io::stdin().read_to_string(&mut input).unwrap();
    let mut iter = input.split_whitespace().map(|x| x.parse::<i64>().unwrap());

    let n = iter.next().unwrap() as usize;
    let k = iter.next().unwrap();
    let m = iter.next().unwrap();

    let mut graph = vec![Vec::new(); n + 1];
    let mut uf = UnionFind::new(n + 1);
    let mut edges = VecDeque::new();

    for _ in 0..k {
        let u = iter.next().unwrap() as usize;
        let v = iter.next().unwrap() as usize;
        uf.union(u, v);
        graph[u].push((v, 1));
        graph[v].push((u, 1));
    }

    for _ in 0..m {
        let u = iter.next().unwrap() as usize;
        let v = iter.next().unwrap() as usize;
        let w = iter.next().unwrap();
        if uf.find(u) != uf.find(v) {
            uf.union(u, v);
            graph[u].push((v, -w));
            graph[v].push((u, -w));
        } else {
            edges.push_back((u, v, w));
        }
    }

    let mut depth = vec![0; n + 1];
    let mut parent = vec![0; n + 1];
    let mut values = vec![0; n + 1];
    dfs(&graph, 1, &mut depth, &mut parent, &mut values, 1);

    let mut ans: i64 = 0;
    let mut cnt = 0;

    for (u, v, w) in edges {
        let mut u = u;
        let mut v = v;
        while u != v {
            if depth[u] < depth[v] {
                std::mem::swap(&mut u, &mut v);
            }
            if values[u] > 0 {
                cnt += 1;
                ans += w;
            }
            u = parent[u];
        }
    }

    if cnt < k {
        println!("-1");
    } else {
        println!("{}", ans);
    }
}
```

## Attempt 2

**Success?: False**

### Generated Code

**Error Type**: compiler

```rust
use std::io::{self, Read};
use std::collections::VecDeque;

const N: usize = 500007;
const MOD: i64 = 100000;

struct Edge {
    node: usize,
    flag: i32,
}

static mut E: Vec<(usize, usize, i64)> = Vec::new();
static mut E_VEC: Vec<Vec<Edge>> = Vec::new();
static mut DEP: Vec<i32> = Vec::new();
static mut P: Vec<i32> = Vec::new();
static mut FA: Vec<isize> = Vec::new();

fn find(x: usize) -> usize {
    unsafe {
        if FA[x] < 0 {
            x
        } else {
            let parent = FA[x] as usize;
            let root = find(parent);
            FA[x] = root as isize;
            root
        }
    }
}

fn dfs(u: usize, d: i32) {
    unsafe {
        DEP[u] = d;
        for edge in &E_VEC[u] {
            let v = edge.node;
            if DEP[v] == 0 {
                P[v] = u as i32 * edge.flag;
                dfs(v, d + 1);
            }
        }
    }
}

fn main() {
    let mut buffer = String::new();
    io::stdin().read_to_string(&mut buffer).expect("Failed to read input");
    let mut iter = buffer.split_whitespace();
    let n: usize = iter.next().unwrap().parse().unwrap();
    let k: usize = iter.next().unwrap().parse().unwrap();
    let m: usize = iter.next().unwrap().parse().unwrap();
    let mut cnt = 0;
    let mut ans = 0;

    unsafe {
        E_VEC = vec![Vec::new(); N];
        DEP = vec![0; N];
        P = vec![0; N];
        FA = vec![-1; N];

        for _ in 0..k {
            let u: usize = iter.next().unwrap().parse().unwrap();
            let v: usize = iter.next().unwrap().parse().unwrap();
            E_VEC[u].push(Edge { node: v, flag: 1 });
            E_VEC[v].push(Edge { node: u, flag: 1 });
            let fu = find(u);
            let fv = find(v);
            if fu != fv {
                FA[fu] = fv as isize;
            }
        }

        for _ in 0..m {
            let u: usize = iter.next().unwrap().parse().unwrap();
            let v: usize = iter.next().unwrap().parse().unwrap();
            let w: i64 = iter.next().unwrap().parse().unwrap();
            let fu = find(u);
            let fv = find(v);
            if fu != fv {
                E_VEC[u].push(Edge { node: v, flag: -1 });
                E_VEC[v].push(Edge { node: u, flag: -1 });
                FA[fu] = fv as isize;
            } else {
                E.push((u, v, w));
            }
        }

        dfs(1, 1);
        for i in 1..=n {
            FA[i] = -1;
        }
        for &(u, v, w) in &E {
            let mut u = u;
            let mut v = v;
            while find(u) != find(v) {
                if DEP[u] < DEP[v] {
                    std::mem::swap(&mut u, &mut v);
                }
                if P[u] > 0 {
                    cnt += 1;
                    ans += w;
                }
                let pu = P[u].abs() as usize;
                FA[u] = pu as isize;
                u = pu;
            }
        }

        if cnt < k {
            println!("-1");
        } else {
            println!("{}", ans);
        }
    }
}
```

## Attempt 3

**Success?: False**

### Generated Code

**Error Type**: runtime

```rust
use std::io::{self, Read};
use std::collections::VecDeque;

const N: usize = 500007;
const MOD: i64 = 100000;

fn read_numbers() -> Vec<i64> {
    let mut input = String::new();
    io::stdin().read_to_string(&mut input).unwrap();
    input.split_whitespace().map(|x| x.parse().unwrap()).collect()
}

fn find(fa: &mut Vec<usize>, x: usize) -> usize {
    if fa[x] != x {
        fa[x] = find(fa, fa[x]);
    }
    fa[x]
}

fn dfs(u: usize, d: i64, dep: &mut Vec<i64>, p: &mut Vec<i64>, e: &Vec<Vec<(usize, i64)>>) {
    dep[u] = d;
    for &(v, f) in &e[u] {
        if dep[v] == 0 {
            p[v] = u as i64 * f;
            dfs(v, d + 1, dep, p, e);
        }
    }
}

fn main() {
    let numbers = read_numbers();
    let mut iter = numbers.iter();
    let n = *iter.next().unwrap() as usize;
    let k = *iter.next().unwrap() as usize;
    let m = *iter.next().unwrap() as usize;
    let mut e = vec![vec![]; N];
    let mut fa = (0..N).collect::<Vec<_>>();
    let mut dep = vec![0; N];
    let mut p = vec![0; N];
    let mut E = VecDeque::new();
    let mut ans = 0;
    let mut cnt = 0;
    
    for _ in 0..k {
        let u = *iter.next().unwrap() as usize;
        let v = *iter.next().unwrap() as usize;
        e[u].push((v, 1));
        e[v].push((u, 1));
        let fu = find(&mut fa, u);
        let fv = find(&mut fa, v);
        if fu != fv {
            fa[fu] = fv;
        }
    }
    
    for _ in 0..m {
        let u = *iter.next().unwrap() as usize;
        let v = *iter.next().unwrap() as usize;
        let w = *iter.next().unwrap();
        let fu = find(&mut fa, u);
        let fv = find(&mut fa, v);
        if fu != fv {
            e[u].push((v, -1));
            e[v].push((u, -1));
            fa[fu] = fv;
        } else {
            E.push_back((u, v, w));
        }
    }
    
    dfs(1, 1, &mut dep, &mut p, &e);
    fa = (0..N).collect::<Vec<_>>();
    
    while let Some((mut u, mut v, w)) = E.pop_front() {
        while let (fu, fv) = (find(&mut fa, u), find(&mut fa, v)) {
            if fu == fv {
                break;
            }
            if dep[fu] < dep[fv] {
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

## Attempt 4

**Success?: False**

### Generated Code

**Error Type**: runtime

```rust
use std::io::{self, Read};
use std::collections::VecDeque;

const N: usize = 500007;
const MOD: i64 = 100000;

// Struct to encapsulate the buffered input functionality.
struct BufferedInput {
    buffer: VecDeque<char>,
}

impl BufferedInput {
    fn new() -> Self {
        Self { buffer: VecDeque::new() }
    }

    fn read_char(&mut self) -> char {
        if let Some(c) = self.buffer.pop_front() {
            c
        } else {
            let mut input = String::new();
            io::stdin().read_to_string(&mut input).expect("Failed to read input");
            for c in input.chars() {
                self.buffer.push_back(c);
            }
            self.buffer.pop_front().unwrap_or('\0')
        }
    }

    fn read_int(&mut self) -> i32 {
        let mut num = 0;
        let mut c = self.read_char();
        while !c.is_digit(10) {
            c = self.read_char();
        }
        while c.is_digit(10) {
            num = num * 10 + c.to_digit(10).unwrap() as i32;
            c = self.read_char();
        }
        num
    }
}

fn find(fa: &mut Vec<i32>, x: usize) -> usize {
    if fa[x] != 0 {
        fa[x] = find(fa, fa[x] as usize) as i32;
        fa[x] as usize
    } else {
        x
    }
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

fn main() {
    let mut input = BufferedInput::new();
    let n = input.read_int() as usize;
    let k = input.read_int() as usize;
    let m = input.read_int() as usize;

    let mut e = vec![vec![]; N];
    let mut fa = vec![0; N];
    let mut dep = vec![0; N];
    let mut p = vec![0; N];
    let mut E = vec![];

    for _ in 0..k {
        let u = input.read_int() as usize;
        let v = input.read_int() as usize;
        e[u].push((v, 1));
        e[v].push((u, 1));
        let fu = find(&mut fa, u);
        let fv = find(&mut fa, v);
        if fu != fv {
            fa[fu] = fv as i32;
        }
    }
    for _ in 0..m {
        let u = input.read_int() as usize;
        let v = input.read_int() as usize;
        let w = input.read_int();
        E.push((u, v, w));
    }

    dfs(1, 1, &e, &mut dep, &mut p);

    let mut ans = 0;
    let mut cnt = 0;

    for (u, v, w) in E {
        let mut u = find(&mut fa, u);
        let mut v = find(&mut fa, v);
        if u != v {
            if dep[u] < dep[v] {
                std::mem::swap(&mut u, &mut v);
            }
            if p[u] > 0 {
                cnt += 1;
                ans += w;
            }
            fa[u] = p[u].abs() as i32;
        }
    }

    if cnt < k {
        println!("-1");
    } else {
        println!("{}", ans);
    }
}
```

