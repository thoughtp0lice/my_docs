# Task 2 - Disjoint Set Union

## C++ Source Code

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

## Example GPT-4 Attempt

```rust
use std::io::Read;
use std::collections::VecDeque;

fn read() -> i32 {
    let mut x: i32 = 0;
    let mut c: u8 = 0;
    let stdin = std::io::stdin();
    let mut handle = stdin.lock();
    while !c.is_ascii_digit() {
        c = handle.bytes().next().unwrap().unwrap();
    }
    while c.is_ascii_digit() {
        x = x * 10 + (c - 48) as i32;
        c = handle.bytes().next().unwrap().unwrap();
    }
    x
}

const N: usize = 500007;

struct Edge {
    v: usize,
    f: i32,
}

struct Tuple {
    u: usize,
    v: usize,
    w: i32,
}

fn dfs(u: usize, d: i32, e: &mut Vec<Vec<Edge>>, dep: &mut Vec<i32>, p: &mut Vec<i32>) {
    dep[u] = d;
    for edge in &e[u] {
        if dep[edge.v] == 0 {
            p[edge.v] = u * edge.f;
            dfs(edge.v, d + 1, e, dep, p);
        }
    }
}

fn find(x: usize, fa: &mut Vec<i32>) -> usize {
    if fa[x] != 0 {
        fa[x] = find(fa[x] as usize, fa) as i32;
    }
    x
}

fn main() {
    let n = read() as usize;
    let k = read() as usize;
    let m = read() as usize;
    let mut cnt = 0;
    let mut ans: i64 = 0;
    let mut e: Vec<Vec<Edge>> = vec![vec![]; N];
    let mut dep: Vec<i32> = vec![0; N];
    let mut p: Vec<i32> = vec![0; N];
    let mut fa: Vec<i32> = vec![0; N];
    let mut E: VecDeque<Tuple> = VecDeque::new();
    for _ in 0..k {
        let u = read() as usize;
        let v = read() as usize;
        e[u].push(Edge {v, f: 1});
        e[v].push(Edge {v: u, f: 1});
        fa[find(u, &mut fa)] = find(v, &mut fa) as i32;
    }
    for _ in 0..m {
        let fu = find(read() as usize, &mut fa);
        let fv = find(read() as usize, &mut fa);
        if fu != fv {
            let w = read();
            e[fu].push(Edge {v: fv, f: -1});
            e[fv].push(Edge {v: fu, f: -1});
            fa[fu] = fv as i32;
        } else {
            E.push_back(Tuple {u: fu, v: fv, w: read()});
        }
    }
    dfs(1, 1, &mut e, &mut dep, &mut p);
    fa = vec![0; N];
    while let Some(t) = E.pop_front() {
        let mut u = t.u;
        let mut v = t.v;
        while find(u, &mut fa) != find(v, &mut fa) {
            if dep[u] < dep[v] {
                std::mem::swap(&mut u, &mut v);
            }
            if p[u] > 0 {
                cnt += 1;
                ans += t.w as i64;
            }
            fa[u] = p[u].abs();
        }
    }
    if cnt < k {
        println!("-1");
    } else {
        println!("{}", ans);
    }
}
```

### Note
- Clone trait needed for edge
- Type error everywhere
- Borrow error everywhere

## IO Description

[IO Description]

Input

The first line of input will contain three integers n, k and m (1 ≤ n, k, m ≤ 5 ⋅ 10^5, k ≤ n-1).

The next k lines contain two integers ga_i and gb_i (1 ≤ ga_i, gb_i ≤ n, ga_i not= gb_i).

The next m lines contain three integers each, fa_i, fb_i and fw_i (1 ≤ fa_i, fb_i ≤ n, fa_i not= fb_i, 1 ≤ fw_i ≤ 10^9).

Output

A single integer.

[IO Examples]

Input

4 3 6 
1 2 
3 4 
1 3 
2 3 3 
3 1 4 
1 2 4 
4 2 8 
4 3 8 
4 1 10

Output

14

Input

3 2 1 
1 2 
2 3 
1 2 30

Output

-1

Input

4 3 3 
1 2 
1 3 
1 4 
4 1 1000000000
4 2 1000000000 
4 3 1000000000

Output

3000000000

## GPT-4 Summary

This C++ program is another example of a solution to a competitive programming problem, involving graph theory and disjoint-set union (DSU) data structures (also known as union-find). The program seems to address a problem with a graph that has two types of edges: k "type-1" edges and m "type-2" edges. It reads input data, processes the graph, and performs specific operations to find an answer. Here's an overview:

IO Namespace:
The namespace IO contains functions for fast input reading, which is common in competitive programming to handle large volumes of data efficiently.

Main program logic:

The program reads n (number of nodes), k (number of type-1 edges), and m (number of type-2 edges).
It initializes global variables for storing the graph (e), the list of type-2 edges (E), node depths (dep), parent pointers in DSU (p), and actual parents in the graph (fa).
dfs(int u, int d) function:
This is a typical depth-first search function that traverses the graph and records the depth of each node and the parent pointers with special flags (positive for one type of edge and negative for another).

find(int x) function:
This function is part of the DSU data structure. It finds the representative of the set that x belongs to and performs path compression for efficiency.

Processing the edges:

Type-1 edges are added to the graph and the DSU structure is updated to reflect the connections between the nodes.
Type-2 edges are processed differently based on whether the nodes they connect are already in the same set in the DSU structure. If they are in the same set, the edge is stored in E for later processing. If they are not, the edge is added to the graph and the DSU structure is updated.
Depth-first search:
The program performs a DFS starting from node 1 to compute depths and parent pointers.

Edge processing:
The program iterates through each type-2 edge stored in E. If the nodes connected by a type-2 edge are in different sets, the program finds the node on the path between u and v with the highest depth and checks the parent pointer to determine if it’s a type-1 edge. If it is, it increments a counter cnt and adds the weight w to the answer ans. The DSU structure is updated to reflect the new connections made.

Output:
Finally, the program checks if the number of type-1 edges included in the solution (cnt) is less than k. If it is, the program outputs -1, indicating that no solution exists. Otherwise, it prints the total weight ans of the type-1 edges included in the solution.

The code uses several C++ Standard Library components such as vector, tuple, pair, and memset, as well as IO optimizations for fast input reading. The use of auto [v, f] and auto [u, v, w] relies on structured bindings introduced in C++17, which allows for unpacking tuples and pairs into individual variables.

## Description From Code Contest

You are managing a mobile phone network, and want to offer competitive prices to connect a network.

The network has n nodes.

Your competitor has already offered some connections between some nodes, with some fixed prices. These connections are bidirectional. There are initially m connections the competitor is offering. The i-th connection your competitor is offering will connect nodes fa_i and fb_i and costs fw_i.

You have a list of k connections that you want to offer. It is guaranteed that this set of connection does not form any cycle. The j-th of these connections will connect nodes ga_j and gb_j. These connections are also bidirectional. The cost of these connections have not been decided yet.

You can set the prices of these connections to any arbitrary integer value. These prices are set independently for each connection. After setting the prices, the customer will choose such n - 1 connections that all nodes are connected in a single network and the total cost of chosen connections is minimum possible. If there are multiple ways to choose such networks, the customer will choose an arbitrary one that also maximizes the number of your connections in it.

You want to set prices in such a way such that all your k connections are chosen by the customer, and the sum of prices of your connections is maximized.

Print the maximum profit you can achieve, or -1 if it is unbounded.

Input

The first line of input will contain three integers n, k and m (1 ≤ n, k, m ≤ 5 ⋅ 10^5, k ≤ n-1), the number of nodes, the number of your connections, and the number of competitor connections, respectively.

The next k lines contain two integers ga_i and gb_i (1 ≤ ga_i, gb_i ≤ n, ga_i not= gb_i), representing one of your connections between nodes ga_i and gb_i. Your set of connections is guaranteed to be acyclic.

The next m lines contain three integers each, fa_i, fb_i and fw_i (1 ≤ fa_i, fb_i ≤ n, fa_i not= fb_i, 1 ≤ fw_i ≤ 10^9), denoting one of your competitor's connections between nodes fa_i and fb_i with cost fw_i. None of these connections connects a node to itself, and no pair of these connections connect the same pair of nodes. In addition, these connections are given by non-decreasing order of cost (that is, fw_{i-1} ≤ fw_i for all valid i).

Note that there may be some connections that appear in both your set and your competitor's set (though no connection will appear twice in one of this sets).

It is guaranteed that the union of all of your connections and your competitor's connections form a connected network.

Output

Print a single integer, the maximum possible profit you can achieve if you set the prices on your connections appropriately. If the profit is unbounded, print -1.

Examples

Input

4 3 6 1 2 3 4 1 3 2 3 3 3 1 4 1 2 4 4 2 8 4 3 8 4 1 10

Output

14

Input

3 2 1 1 2 2 3 1 2 30

Output

-1

Input

4 3 3 1 2 1 3 1 4 4 1 1000000000 4 2 1000000000 4 3 1000000000

Output

3000000000

Note

In the first sample, it's optimal to give connection 1-3 cost 3, connection 1-2 cost 3, and connection 3-4 cost 8. In this case, the cheapest connected network has cost 14, and the customer will choose one that chooses all of your connections.

In the second sample, as long as your first connection costs 30 or less, the customer chooses both your connections no matter what is the cost of the second connection, so you can get unbounded profit in this case.