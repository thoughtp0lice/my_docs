# CFG Examples

## Example 1

```c++
#include <bits/stdc++.h>
using namespace std;
long long pw(long long n, long long k) {
  if (!k) return 1;
  if (k & 1) return n * pw(n, k - 1) % (100000);
  long long t = pw(n, k / 2);
  return t * t % (100000);
}
char s[8], r[8];
int S[] = {0, 2, 4, 3, 1};
int main(void) {
  scanf("%s", s);
  for (int i(0); i < 5; i++) r[i] = s[S[i]];
  printf("%05lld\n", pw(atoi(r), 5));
  return 0;
}
```

```
long long pw(long long n, long long k)

 [B0(ENTRY)]
        Child Nodes: B1
 [B1]

        Branch:  if !k

        Parent Nodes: B0
        Child Nodes: B2 B3
 [B2]
        1: return 1; 

        Parent Nodes: B1
        Child Nodes: B6
 [B3]

        Branch:  if k & 1

        Parent Nodes: B1
        Child Nodes: B4 B5
 [B4]
        1: return n * pw(n, k - 1) % 100000; 

        Parent Nodes: B3
        Child Nodes: B6
 [B5]
        1: pw(n, k / 2) 
        2: long long t = pw(n, k / 2); 
        3: return t * t % 100000; 

        Parent Nodes: B3
        Child Nodes: B6
 [B6(EXIT)]


        Parent Nodes: B5 B4 B2


int main()

 [B0(ENTRY)]
        Child Nodes: B1
 [B1]
        1: scanf("%s", s) 
        2: 0 
        3: int i(0); 

        Parent Nodes: B0
        Child Nodes: B2
 [B2]

        Branch:  for (...; i < 5; ...)

        Parent Nodes: B4 B1
        Child Nodes: B3 B5
 [B3]
        1: r[i] = s[S[i]] 

        Parent Nodes: B2
        Child Nodes: B4
 [B4]
        1: i++ 

        Parent Nodes: B3
        Child Nodes: B2
 [B5]
        1: printf("%05lld\n", pw(atoi(r), 5)) 
        2: return 0; 

        Parent Nodes: B2
        Child Nodes: B6
 [B6(EXIT)]


        Parent Nodes: B5
```

## Example 2

### Source Code

```c++
#include <bits/stdc++.h>
int n;
int f(int a) { return a > n ? 0 : 1 + f(a * 10 + 1) + f(a * 10); }
int main()
{
    std::cin >> n;
    std::cout << f(1);
}
```

```
int f(int a)

 [B0(ENTRY)]
        Child Nodes: B1
 [B1]

        Branch:  a > n ? ... : ...

        Parent Nodes: B0
        Child Nodes: B2 B2
 [B2]
        1: return a > n ? 0 : 1 + f(a * 10 + 1) + f(a * 10); 

        Parent Nodes: B1 B1
        Child Nodes: B3
 [B3(EXIT)]


        Parent Nodes: B2


int main()

 [B0(ENTRY)]
        Child Nodes: B1
 [B1]
        1: operator>> 
        2: std::cin >> n 
        3: operator<< 
        4: std::cout << f(1) 

        Parent Nodes: B0
        Child Nodes: B2
 [B2(EXIT)]


        Parent Nodes: B1
```

## Example 3

```c++
#include <bits/stdc++.h>
const int N = 1 << 20;
int n, m, a[N], c[N], tag[N << 2], cnt[N << 2];
void update(int p, int l, int r)
{
    if (l == r)
        cnt[p] = !!c[l] * !tag[p];
    else
        cnt[p] = (cnt[p << 1] + cnt[p << 1 | 1]) * !tag[p];
}
void modify(int p, int L, int R, int l, int r, int x)
{
    if (L == l && R == r)
        return tag[p] += x, update(p, L, R);
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
void settle(int x, int w)
{
    if (a[x] > a[x - 1])
        modify(1, 1, N, a[x - 1] + 1, a[x], w);
}
void init(int x, int w) { settle(x, w), settle(x + 1, w); }
void ins(int x, int w) { c[x] += w, modify(1, 1, N, x, x, 0); }
int main()
{
    scanf("%d%d", &n, &m), a[0] = N;
    for (int i = 1; i <= n; i++)
        scanf("%d", a + i), ins(a[i], 1), settle(i, 1);
    for (int x, y; m--;)
    {
        scanf("%d%d", &x, &y), init(x, -1), ins(a[x], -1);
        ins(a[x] = y, 1), init(x, 1), printf("%d\n", cnt[1]);
    }
    return 0;
}

```

```
void update(int p, int l, int r)

 [B0(ENTRY)]
	Child Nodes: B1
 [B1]

	Branch:  if l == r

	Parent Nodes: B0
	Child Nodes: B2 B3
 [B2]
	1: cnt[p] = !!c[l] * !tag[p] 

	Parent Nodes: B1
	Child Nodes: B4
 [B3]
	1: cnt[p] = (cnt[p << 1] + cnt[p << 1 | 1]) * !tag[p] 

	Parent Nodes: B1
	Child Nodes: B4
 [B4(EXIT)]


	Parent Nodes: B3 B2


void modify(int p, int L, int R, int l, int r, int x)

 [B0(ENTRY)]
	Child Nodes: B1
 [B1]

	Branch:  L == l && ...

	Parent Nodes: B0
	Child Nodes: B2 B4
 [B2]

	Branch:  if L == l && R == r

	Parent Nodes: B1
	Child Nodes: B3 B4
 [B3]
	1: tag[p] += x 
	2: return ... , update(p, L, R); 

	Parent Nodes: B2
	Child Nodes: B10
 [B4]
	1: L + R >> 1 
	2: int mid = L + R >> 1; 
	Branch:  if l > mid

	Parent Nodes: B2 B1
	Child Nodes: B5 B6
 [B5]
	1: x 
	2: modify(p << 1 | 1, mid + 1, R, l, r, R) 

	Parent Nodes: B4
	Child Nodes: B9
 [B6]

	Branch:  if r <= mid

	Parent Nodes: B4
	Child Nodes: B7 B8
 [B7]
	1: modify(p << 1, L, mid, l, r, x) 

	Parent Nodes: B6
	Child Nodes: B9
 [B8]
	1: modify(p << 1, L, mid, l, mid, x) 
	2: ... , modify(p << 1 | 1, mid + 1, R, mid + 1, r, x) 

	Parent Nodes: B6
	Child Nodes: B9
 [B9]
	1: update(p, L, R) 

	Parent Nodes: B8 B7 B5
	Child Nodes: B10
 [B10(EXIT)]


	Parent Nodes: B9 B3


void settle(int x, int w)

 [B0(ENTRY)]
	Child Nodes: B1
 [B1]

	Branch:  if a[x] > a[x - 1]

	Parent Nodes: B0
	Child Nodes: B2 B3
 [B2]
	1: w 
	2: modify(1, 1, N, a[x - 1] + 1, a, a[x]) 

	Parent Nodes: B1
	Child Nodes: B3
 [B3(EXIT)]


	Parent Nodes: B2 B1


void init(int x, int w)

 [B0(ENTRY)]
	Child Nodes: B1
 [B1]
	1: settle(x, w) 
	2: ... , settle(x + 1, w) 

	Parent Nodes: B0
	Child Nodes: B2
 [B2(EXIT)]


	Parent Nodes: B1


void ins(int x, int w)

 [B0(ENTRY)]
	Child Nodes: B1
 [B1]
	1: c[x] += w 
	2: ... , modify(1, 1, N, x, x, 0) 

	Parent Nodes: B0
	Child Nodes: B2
 [B2(EXIT)]


	Parent Nodes: B1


int main()

 [B0(ENTRY)]
	Child Nodes: B1
 [B1]
	1: scanf("%d%d", &n, &m) 
	2: a[0] 
	3: ... , N = N 
	4: 1 
	5: int i = 1; 

	Parent Nodes: B0
	Child Nodes: B2
 [B2]

	Branch:  for (...; i <= n; ...)

	Parent Nodes: B4 B1
	Child Nodes: B3 B5
 [B3]
	1: scanf("%d", a + i) 
	2: ... , ins(a[i], 1) 
	3: ... , settle(i, 1) 

	Parent Nodes: B2
	Child Nodes: B4
 [B4]
	1: i++ 

	Parent Nodes: B3
	Child Nodes: B2
 [B5]
	1: int x; 
	2: int y; 

	Parent Nodes: B2
	Child Nodes: B6
 [B6]

	Branch:  for (...; m--; )

	Parent Nodes: B7 B5
	Child Nodes: B7 B8
 [B7]
	1: scanf("%d%d", &x, &y) 
	2: ... , init(x, -1) 
	3: ... , ins(a[x], -1) 
	4: ins(a[x] = y, 1) 
	5: ... , init(x, 1) 
	6: ... , printf("%d\n", cnt[1]) 

	Parent Nodes: B6
	Child Nodes: B6
 [B8]
	1: return 0; 

	Parent Nodes: B6
	Child Nodes: B9
 [B9(EXIT)]


	Parent Nodes: B8

```