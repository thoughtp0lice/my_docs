# Few Shot Examples

Few shot examples of C++ to Rust translation selected from code contest dataset

## Task_id 41

### C++ Code

```c++
#include <bits/stdc++.h>
using namespace std;
int main() {
  int t;
  cin >> t;
  while (t--) {
    long int a, b, count = 0;
    cin >> a >> b;
    if (a % b != 0) count = (b * ((floor(a / b) + 1)) - a);
    cout << count << endl;
  }
}
```

### CodeContest Description

You are given two positive integers a and b. In one move you can increase a by 1 (replace a with a+1). Your task is to find the minimum number of moves you need to do in order to make a divisible by b. It is possible, that you have to make 0 moves, as a is already divisible by b. You have to answer t independent test cases.

Input

The first line of the input contains one integer t (1 ≤ t ≤ 10^4) — the number of test cases. Then t test cases follow.

The only line of the test case contains two integers a and b (1 ≤ a, b ≤ 10^9).

Output

For each test case print the answer — the minimum number of moves you need to do in order to make a divisible by b.

Example

Input


5
10 4
13 9
100 13
123 456
92 46


Output

2
5
4
333
0

## Task_id 60

### C++ Code

```c++
#include <bits/stdc++.h>
int n;
int f(int a) { return a > n ? 0 : 1 + f(a * 10 + 1) + f(a * 10); }
int main() {
  std::cin >> n;
  std::cout << f(1);
}
```

### Code Contest Description

One beautiful July morning a terrible thing happened in Mainframe: a mean virus Megabyte somehow got access to the memory of his not less mean sister Hexadecimal. He loaded there a huge amount of n different natural numbers from 1 to n to obtain total control over her energy.

But his plan failed. The reason for this was very simple: Hexadecimal didn't perceive any information, apart from numbers written in binary format. This means that if a number in a decimal representation contained characters apart from 0 and 1, it was not stored in the memory. Now Megabyte wants to know, how many numbers were loaded successfully.

Input

Input data contains the only number n (1 ≤ n ≤ 109).

Output

Output the only number — answer to the problem.

Examples

Input

10


Output

2

Note

For n = 10 the answer includes numbers 1 and 10.

## Task_id 90

### C++ Code

```c++
#include <bits/stdc++.h>
using namespace std;
void ga(int N, int *A) {
  for (int i(0); i < N; i++) scanf("%d", A + i);
}
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

### Code Contest Description

The protection of a popular program developed by one of IT City companies is organized the following way. After installation it outputs a random five digit number which should be sent in SMS to a particular phone number. In response an SMS activation code arrives.

A young hacker Vasya disassembled the program and found the algorithm that transforms the shown number into the activation code. Note: it is clear that Vasya is a law-abiding hacker, and made it for a noble purpose — to show the developer the imperfection of their protection.

The found algorithm looks the following way. At first the digits of the number are shuffled in the following order <first digit><third digit><fifth digit><fourth digit><second digit>. For example the shuffle of 12345 should lead to 13542. On the second stage the number is raised to the fifth power. The result of the shuffle and exponentiation of the number 12345 is 455 422 043 125 550 171 232. The answer is the 5 last digits of this result. For the number 12345 the answer should be 71232.

Vasya is going to write a keygen program implementing this algorithm. Can you do the same?

Input

The only line of the input contains a positive integer five digit number for which the activation code should be found.

Output

Output exactly 5 digits without spaces between them — the found activation code of the program.

Examples

Input

12345


Output

71232
