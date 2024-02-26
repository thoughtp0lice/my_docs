# Task 70

1 of 4 success

## Attempt 1

**Success?: False**

### Generated Code

**Error Type**: runtime

```rust
use std::io::{self, BufRead};

// Constants
const N: usize = 1 << 20;

// Global arrays (In Rust, we manage without global state when possible)
struct SegmentTree {
    cnt: Vec<i32>,
    tag: Vec<i32>,
}

impl SegmentTree {
    fn new() -> Self {
        SegmentTree {
            cnt: vec![0; N << 2],
            tag: vec![0; N << 2],
        }
    }

    fn update(&mut self, p: usize, l: usize, r: usize, c: &Vec<i32>) {
        if l == r {
            self.cnt[p] = if c[l] != 0 { 1 } else { 0 } * if self.tag[p] == 0 { 1 } else { 0 };
        } else {
            self.cnt[p] = (self.cnt[p << 1] + self.cnt[p << 1 | 1]) * if self.tag[p] == 0 { 1 } else { 0 };
        }
    }

    fn modify(&mut self, p: usize, l: usize, r: usize, x: usize, y: usize, val: i32, c: &mut Vec<i32>) {
        if x <= l && r <= y {
            self.tag[p] += val;
            self.update(p, l, r, c);
            return;
        }
        let mid = (l + r) >> 1;
        if x <= mid {
            self.modify(p << 1, l, mid, x, y, val, c);
        }
        if y > mid {
            self.modify(p << 1 | 1, mid + 1, r, x, y, val, c);
        }
        self.update(p, l, r, c);
    }
}

fn settle(x: usize, a: &Vec<i32>, st: &mut SegmentTree, c: &mut Vec<i32>, w: i32) {
    if a[x] > a[x - 1] {
        st.modify(1, 1, N, (a[x - 1] + 1) as usize, a[x] as usize, w, c);
    }
}

fn init(x: usize, a: &Vec<i32>, st: &mut SegmentTree, c: &mut Vec<i32>, w: i32) {
    if x < a.len() - 1 {
        settle(x, a, st, c, w);
        settle(x + 1, a, st, c, w);
    }
}

fn ins(x: usize, a: &mut Vec<i32>, st: &mut SegmentTree, c: &mut Vec<i32>, w: i32) {
    c[x] += w;
    st.modify(1, 1, N, x, x, 0, c);
}

fn main() {
    let mut input = String::new();
    io::stdin().read_line(&mut input).unwrap();
    let parts: Vec<usize> = input
        .split_whitespace()
        .map(|x| x.parse().unwrap())
        .collect();
    let (n, m) = (parts[0], parts[1]);

    let mut a = vec![N as i32; n + 1];
    let mut c = vec![0; N + 1];
    let mut st = SegmentTree::new();

    let mut buffer = String::new();
    let stdin = io::stdin();
    let mut handle = stdin.lock();
    handle.read_line(&mut buffer).unwrap();
    let nums: Vec<i32> = buffer
                        .split_whitespace()
                        .map(|num| num.parse().unwrap())
                        .collect();
    for (i, &num) in nums.iter().enumerate() {
        a[i + 1] = num;
        ins(num as usize, &mut a, &mut st, &mut c, 1);
        settle(i + 1, &a, &mut st, &mut c, 1);
    }
    
    for _ in 0..m {
        let mut line = String::new();
        handle.read_line(&mut line).unwrap();
        let parts: Vec<usize> = line
            .split_whitespace()
            .map(|x| x.parse().unwrap())
            .collect();
        let (x, y) = (parts[0], parts[1]);

        init(x, &a, &mut st, &mut c, -1);
        ins(a[x] as usize, &mut a, &mut st, &mut c, -1);
        a[x] = y as i32;
        ins(y, &mut a, &mut st, &mut c, 1);
        init(x, &a, &mut st, &mut c, 1);

        println!("{}", st.cnt[1]);
    }
}
```

## Attempt 2

**Success?: False**

### Generated Code

**Error Type**: runtime

```rust
use std::io::{self, BufRead};

const N: usize = 1 << 20;

struct SegmentTree {
    cnt: Vec<i32>,
    tag: Vec<i32>,
    c: Vec<i32>,
}

impl SegmentTree {
    fn new() -> Self {
        Self {
            cnt: vec![0; N << 2],
            tag: vec![0; N << 2],
            c: vec![0; N],
        }
    }

    fn update(&mut self, p: usize, l: usize, r: usize) {
        if l == r {
            self.cnt[p] = (self.c[l] > 0 && self.tag[p] == 0) as i32;
        } else {
            self.cnt[p] = (self.cnt[p << 1] + self.cnt[p << 1 | 1]) * (self.tag[p] == 0) as i32;
        }
    }

    fn modify(&mut self, p: usize, l: usize, r: usize, L: usize, R: usize, x: i32) {
        if L == l && R == r {
            self.tag[p] += x;
            self.update(p, l, r);
            return;
        }
        let mid = (L + R) >> 1;
        if l > mid {
            self.modify(p << 1 | 1, mid + 1, R, l, r, x);
        } else if r <= mid {
            self.modify(p << 1, L, mid, l, r, x);
        } else {
            self.modify(p << 1, L, mid, l, mid, x);
            self.modify(p << 1 | 1, mid + 1, R, mid + 1, r, x);
        }
        self.update(p, L, R);
    }

    fn settle(&mut self, x: usize, w: i32, a: &Vec<i32>) {
        if a[x] > a[x - 1] {
            self.modify(1, 1, N, (a[x - 1] + 1) as usize, a[x] as usize, w);
        }
    }

    fn init(&mut self, x: usize, w: i32, a: &Vec<i32>) {
        self.settle(x, w, a);
        self.settle(x + 1, w, a);
    }

    fn ins(&mut self, x: usize, w: i32) {
        self.c[x] += w;
        self.modify(1, 1, N, x, x, 0);
    }
}

fn main() {
    let stdin = io::stdin();
    let mut lines = stdin.lock().lines();
    let first_line = lines.next().unwrap().unwrap();
    let mut split = first_line.split_whitespace();
    let n: usize = split.next().unwrap().parse().unwrap();
    let m: usize = split.next().unwrap().parse().unwrap();

    let mut a: Vec<i32> = vec![0; n + 1];
    let input_line = lines.next().unwrap().unwrap();
    a[1..].copy_from_slice(&input_line.split_whitespace().map(|x| x.parse().unwrap()).collect::<Vec<i32>>());

    let mut st = SegmentTree::new();
    a[0] = N as i32;
    for i in 1..=n {
        st.ins(a[i] as usize, 1);
        st.settle(i, 1, &a);
    }

    for _ in 0..m {
        let line = lines.next().unwrap().unwrap();
        let mut parts = line.split_whitespace();
        let x: usize = parts.next().unwrap().parse().unwrap();
        let y: i32 = parts.next().unwrap().parse().unwrap();

        st.init(x, -1, &a);
        st.ins(a[x] as usize, -1);
        st.ins(y as usize, 1);
        a[x] = y;
        st.init(x, 1, &a);
        println!("{}", st.cnt[1]);
    }
}
```

## Attempt 3

**Success?: False**

### Generated Code

**Error Type**: runtime

```rust
use std::io::{self, BufRead};

const N: usize = 1 << 20;

struct SegmentTree {
    c: Vec<i32>,
    tag: Vec<i32>,
    cnt: Vec<i32>,
}

impl SegmentTree {
    fn new() -> Self {
        SegmentTree {
            c: vec![0; N],
            tag: vec![0; N << 2],
            cnt: vec![0; N << 2],
        }
    }

    fn update(&mut self, p: usize, l: usize, r: usize) {
        if l == r {
            self.cnt[p] = if self.c[l] > 0 { 1 } else { 0 } * if self.tag[p] == 0 { 1 } else { 0 };
        } else {
            self.cnt[p] = (self.cnt[p << 1] + self.cnt[(p << 1) | 1]) * if self.tag[p] == 0 { 1 } else { 0 };
        }
    }

    fn modify(&mut self, p: usize, l: usize, r: usize, left: usize, right: usize, x: i32) {
        if l == left && r == right {
            self.tag[p] += x;
            self.update(p, l, r);
            return;
        }
        let mid = (l + r) >> 1;
        if left > mid {
            self.modify((p << 1) | 1, mid + 1, r, left, right, x);
        } else if right <= mid {
            self.modify(p << 1, l, mid, left, right, x);
        } else {
            self.modify(p << 1, l, mid, left, mid, x);
            self.modify((p << 1) | 1, mid + 1, r, mid + 1, right, x);
        }
        self.update(p, l, r);
    }

    fn settle(&mut self, x: usize, w: i32, a: &Vec<i32>) {
        if a[x] > a[x - 1] {
            self.modify(1, 1, N, (a[x - 1] + 1) as usize, a[x] as usize, w);
        }
    }

    fn init(&mut self, x: usize, w: i32, a: &Vec<i32>) {
        self.settle(x, w, a);
        self.settle(x + 1, w, a);
    }

    fn ins(&mut self, x: usize, w: i32) {
        self.c[x] += w;
        self.modify(1, 1, N, x, x, 0);
    }
}

fn main() {
    let stdin = io::stdin();
    let mut lines = stdin.lock().lines();
    let first_line = lines.next().unwrap().unwrap();
    let mut parts = first_line.split_whitespace();
    let n: i32 = parts.next().unwrap().parse().unwrap();
    let m: i32 = parts.next().unwrap().parse().unwrap();

    let mut segment_tree = SegmentTree::new();
    let mut a = vec![N as i32; n as usize + 1];

    let second_line = lines.next().unwrap().unwrap();
    let nums: Vec<i32> = second_line.split_whitespace().map(|num| num.parse().unwrap()).collect();

    for i in 1..=n as usize {
        a[i] = nums[i - 1];
        segment_tree.ins(a[i] as usize, 1);
        segment_tree.settle(i, 1, &a);
    }

    for _ in 0..m {
        let line = lines.next().unwrap().unwrap();
        let mut parts = line.split_whitespace();
        let x: usize = parts.next().unwrap().parse().unwrap();
        let y: i32 = parts.next().unwrap().parse().unwrap();
        segment_tree.init(x, -1, &a);
        segment_tree.ins(a[x] as usize, -1);
        a[x] = y;
        segment_tree.ins(a[x] as usize, 1);
        segment_tree.init(x, 1, &a);
        println!("{}", segment_tree.cnt[1]);
    }
}
```

## Attempt 4

**Success?: False**

### Generated Code

**Error Type**: runtime

```rust
use std::io::{self, BufRead};

const N: usize = 1 << 20;

struct SegmentTree {
    tag: Vec<i32>,
    cnt: Vec<i32>,
}

impl SegmentTree {
    fn new() -> Self {
        Self {
            tag: vec![0; N << 2],
            cnt: vec![0; N << 2],
        }
    }

    fn update(&mut self, p: usize, l: usize, r: usize, c: &Vec<i32>) {
        if l == r {
            self.cnt[p] = if c[l] != 0 && self.tag[p] == 0 { 1 } else { 0 };
        } else {
            let mid = (l + r) >> 1;
            self.cnt[p] = (self.cnt[p << 1] + self.cnt[p << 1 | 1]) * if self.tag[p] == 0 { 1 } else { 0 };
        }
    }

    fn modify(&mut self, p: usize, l: usize, r: usize, ql: usize, qr: usize, x: i32, c: &mut Vec<i32>) {
        if ql <= l && qr >= r {
            self.tag[p] += x;
            self.update(p, l, r, c);
            return;
        }
        let mid = (l + r) >> 1;
        if ql <= mid {
            self.modify(p << 1, l, mid, ql, qr.min(mid), x, c);
        }
        if qr > mid {
            self.modify(p << 1 | 1, mid + 1, r, (ql.max(mid + 1)), qr, x, c);
        }
        self.update(p, l, r, c);
    }
}

fn main() {
    let stdin = io::stdin();
    let mut iterator = stdin.lock().lines();
    let first_line = iterator.next().unwrap().unwrap();
    let mut parts = first_line.split_whitespace();
    let n: usize = parts.next().unwrap().parse().unwrap();
    let m: usize = parts.next().unwrap().parse().unwrap();

    let mut a: Vec<i32> = vec![0; N];
    let mut c: Vec<i32> = vec![0; N];

    let seg_tree = &mut SegmentTree::new();

    for i in 1..=n {
        let val: i32 = iterator.next().unwrap().unwrap().parse().unwrap();
        a[i] = val;
        c[val as usize] += 1;
        seg_tree.modify(1, 1, N, val as usize, val as usize, 0, &mut c);
    }

    for _ in 0..m {
        let line = iterator.next().unwrap().unwrap();
        let mut parts = line.split_whitespace();
        let pos: usize = parts.next().unwrap().parse().unwrap();
        let x: i32 = parts.next().unwrap().parse().unwrap();

        c[a[pos] as usize] -= 1;
        seg_tree.modify(1, 1, N, a[pos] as usize, a[pos] as usize, -1, &mut c);

        a[pos] = x;
        c[x as usize] += 1;
        seg_tree.modify(1, 1, N, x as usize, x as usize, 1, &mut c);

        println!("{}", seg_tree.cnt[1]);
    }
}
```

