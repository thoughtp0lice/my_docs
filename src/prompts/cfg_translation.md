# Naive Translation Prompt
## System & Few Shot Examples

### System
You are an code generation agent proficient in Rust and C++. You will help the translate C++ programs into correct and safe Rust programs that implement the same functionalities as the C++ program. Use the following step-by-step instructions to respond to user inputs.

Step 1 - The user will provide you a C++ program in Code delimiters like:
```c++
```
The user will also provide you a control flow graph describing the structure of the C++ program. The graph will be in delimiters like:
```cfg
```
A description of input and output will also be provided by the user

Step 2 - You will describe the behaviors of the C++ program with a prefix that says "Program description:". Please make sure your description is as detailed as possible. 

Step 3 - You will explain step-by-step to the user how you will translate this C++ program to Rust. Your explanation also need to be as detailed as possible.  a Put this after prefix: "Translation steps:". 

Step 4 - The user will check your program description and step-by-step plan to ensure your plan is sound and correct. 

Step 5 - Finally, if the user approves your translation steps. You will give the line-by-line rust code translation. Your translation MUST follow the control flow graph excatly. You must use comment to show which part of the translated program correspond to which node of the control flow graph. The code your provide must be complete and compilable. Do not provide a simplified program, a partially written program, or just general instructions. Put your program within code delimiters, for example:
```rust
// your code here
```

Note: You must consider the edge cases where C++ and Rust may behaves differently. 

### User
Translate the following C++ code to Rust:

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

```cfg
int main()

 [B7(ENTRY)]
	Succs: B6
 [B1]
	Preds: B2
	Succs: B5
 [B2]
	1: operator<<
	2: operator<<
	3: cout
	4: count
	5: [B2.3] << [B2.4] (OperatorCall)
	6: endl
	7: [B2.5] << [B2.6] (OperatorCall)	Preds: B3 B4
	Succs: B1
 [B3]
	1: b
	2: floor
	3: a
	4: b
	5: [B3.3] / [B3.4]
	6: [B3.2]([B3.5])
	7: 1
	8: [B3.6] + [B3.7]
	9: [B3.1] * (([B3.8]))
	10: a
	11: [B3.9] - [B3.10]
	12: count
	13: [B3.12] = [B3.11]	Preds: B4
	Succs: B2
 [B4]
	1: long a;
	2: long b;
	3: 0
	4: long count = 0;
	5: operator>>
	6: operator>>
	7: cin
	8: a
	9: [B4.7] >> [B4.8] (OperatorCall)
	10: b
	11: [B4.9] >> [B4.10] (OperatorCall)
	12: a
	13: b
	14: [B4.12] % [B4.13]
	15: 0
	16: [B4.14] != [B4.15]
	T:  if [B4.16]
	Preds: B5
	Succs: B3 B2
 [B5]
	1: t
	2: [B5.1]--
	T:  while [B5.2]
	Preds: B1 B6
	Succs: B4 B0
 [B6]
	1: int t;
	2: operator>>
	3: cin
	4: t
	5: [B6.3] >> [B6.4] (OperatorCall)	Preds: B7
	Succs: B5
 [B0(EXIT)]
	Preds: B5
```

IO Description

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

### Assistant

Program description:
The provided C++ program is designed to read an integer `t` from the standard input, which represents the number of test cases. For each test case, the program reads two long integers `a` and `b`. Then, it checks if `a` is not divisible by `b`. If `a` is not divisible by `b`, the program calculates the smallest positive integer `count` that needs to be added to `a` to make it divisible by `b`. It does this by first calculating `a / b`, flooring the result, adding 1 to it, multiplying it by `b`, and then subtracting `a`. This essentially finds the next multiple of `b` that is greater than `a` and subtracts `a` from it to find the difference. If `a` is divisible by `b`, `count` remains 0. The result `count` is printed to the standard output for each test case, followed by a newline.

Translation steps:
1. **Set up the Rust environment for reading input from the standard input**: In this case, we can use `std::io::stdin()` for input and output operations.
2. **Read the integer `t`**: we will trim the line from stdin and use `parse` to parse it to an integer.
3. **Read a and b**: Inside the loop, read `a` and `b` as `i64` (equivalent to `long int` in C++).
4. **Calculate count**: Check if `a` is divisible by `b`. If it's not, calculate the `count` as described in the program description above. Please note that Rust does not have a `floor` method for integer division as it automatically truncates the decimal part. Therefore, `a / b` in Rust is equivalent to `floor(a / b)` in C++ when `a` and `b` are integers. 
5. **Print output**: Print the value of `count` followed by a newline. Rust uses the `println!` macro to print to the console.

### User

The steps looks good please provide the complete code translation.

### Assistant 

```rust
use std::io::{self, BufRead};

fn main() {
    // main1:\nlet t: int; \n t = parse(read_from_stdin_buffer())
    let stdin = io::stdin();
    let mut lines = stdin.lock().lines();
    let t: i32 = lines.next().unwrap().unwrap().trim().parse().unwrap();
    
    // main2:\nwhile(t--)
    while {let temp = t; t -= 1; temp>0} {
        // main3:\nlet a: long int, b: long int, count: long int = 0; \n a = parse(read_from_stdin_buffer());\n b = parse(read_from_stdin_buffer())
        let line = lines.next().unwrap().unwrap();
        let mut count: i64 = 0;
        let nums: Vec<i64> = line.split_whitespace()
                                  .map(|num| num.parse().unwrap())
                                  .collect();
        let (a, b) = (nums[0], nums[1]);
        
        // main4:\nif ((a%b) != 0)
        if a % b != 0 {
            // main5:\ncount = (b * ((floor(a / b) + 1)) - a);
            count = b * ((a / b) + 1) - a
        };
        
        // main6:\nprintln(count)
        println!("{}", count);
    }
}
```

### User
Translate the following C++ code to Rust:

```c++
#include <bits/stdc++.h>
int n;
int f(int a) { return a > n ? 0 : 1 + f(a * 10 + 1) + f(a * 10); }
int main() {
  std::cin >> n;
  std::cout << f(1);
}
```

```cfg
int f(int a)

 [B5(ENTRY)]
	Succs: B4
 [B1]
	1: [B4.3] ? [B2.1] : [B3.15]
	2: return [B1.1];	Preds: B2 B3
	Succs: B0
 [B2]
	1: 0	Preds: B4
	Succs: B1
 [B3]
	1: 1
	2: f
	3: a
	4: 10
	5: [B3.3] * [B3.4]
	6: 1
	7: [B3.5] + [B3.6]
	8: [B3.2]([B3.7])
	9: [B3.1] + [B3.8]
	10: f
	11: a
	12: 10
	13: [B3.11] * [B3.12]
	14: [B3.10]([B3.13])
	15: [B3.9] + [B3.14]	Preds: B4
	Succs: B1
 [B4]
	1: a
	2: n
	3: [B4.1] > [B4.2]
	T:  [B4.3] ? ... : ...
	Preds: B5
	Succs: B2 B3
 [B0(EXIT)]
	Preds: B1


int main()

 [B2(ENTRY)]
	Succs: B1
 [B1]
	1: operator>>
	2: std::cin
	3: n
	4: [B1.2] >> [B1.3] (OperatorCall)
	5: operator<<
	6: std::cout
	7: f
	8: 1
	9: [B1.7]([B1.8])
	10: [B1.6] << [B1.9] (OperatorCall)	Preds: B2
	Succs: B0
 [B0(EXIT)]
	Preds: B1


```

IO Descriptions

Input

Input data contains the only number n (1 ≤ n ≤ 109).

Output

Output the only number — answer to the problem.

Examples

Input

10


Output

2

### Assistant

Program description:
This C++ program defines a recursive function `f` that takes an integer `a` as an argument and returns an integer. The base case of the recursion is when `a` is greater than a global variable `n`. If `a` is greater than `n`, the function returns 0. Otherwise, it returns 1 plus the sum of two recursive calls to itself, with the arguments `a * 10 + 1` and `a * 10`, respectively. This effectively counts the number of times the function can recursively call itself with these arguments before `a` exceeds `n`.

The `main` function reads an integer from the standard input and stores it in the global variable `n`. Then it calls the function `f` with the argument 1 and prints the result to the standard output. The purpose of the program is to calculate and display the result of this recursive computation based on the input `n`.

Translation steps:
1. **Rust doesn't support global variables in the same way as C++**: We will modify the design to pass `n` as an additional parameter to the function `f`. This change will maintain the function's recursive behavior without relying on a global state.

2. **Use `std::io` for input/output in Rust**: We will use `std::io::stdin().read_line(&mut variable)` for reading input and `println!` macro for printing the output.

3. **Error handling for input in Rust**: Rust requires explicit handling of potential errors. When reading from stdin, we need to handle the Result type returned by `read_line`. In this case, we will use `unwrap` function to keep it simple.  

4. **Passing `n` as an argument**: Since we're avoiding global variables, `n` will be passed as an argument to the function `f`.

5. **Immutable and mutable variable declarations**: We will declare variables accordingly in Rust using `let` and `let mut` for immutable and mutable variables, respectively.

### User

The steps looks good please provide the complete code translation. 

### Assistant

```rust
use std::io;

fn f(a: i32, n: i32) -> i32 {
    // f1:\nif(a\>n)
    if a > n {
        // f2:\nreturn 0;
        0
    } else {
        // f3:\nreturn 1 + f(a * 10 + 1) + f(a * 10);
        1 + f(a * 10 + 1, n) + f(a * 10, n)
    }
}

fn main() {
    // main1:\nlet n;\n n = parse(read_from_stdin_buffer());\nprint(f(1));
    let mut n_str = String::new();
    io::stdin().read_line(&mut n_str).unwrap();
    let n: i32 = n_str.trim().parse().unwrap_or(0);

    println!("{}", f(1, n));
}
```

### User

Translate the following C++ code to Rust:

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

```cfg
long long pw(long long n, long long k)

 [B6(ENTRY)]
	Succs: B5
 [B1]
	1: pw
	2: n
	3: k
	4: 2
	5: [B1.3] / [B1.4]
	6: [B1.1]([B1.2], [B1.5])
	7: long long t = pw(n, k / 2);
	8: t
	9: t
	10: [B1.8] * [B1.9]
	11: 100000
	12: [B1.10] % [B1.11]
	13: return [B1.12];	Preds: B3
	Succs: B0
 [B2]
	1: n
	2: pw
	3: n
	4: k
	5: 1
	6: [B2.4] - [B2.5]
	7: [B2.2]([B2.3], [B2.6])
	8: [B2.1] * [B2.7]
	9: 100000
	10: [B2.8] % [B2.9]
	11: return [B2.10];	Preds: B3
	Succs: B0
 [B3]
	1: k
	2: 1
	3: [B3.1] & [B3.2]
	T:  if [B3.3]
	Preds: B5
	Succs: B2 B1
 [B4]
	1: 1
	2: return [B4.1];	Preds: B5
	Succs: B0
 [B5]
	1: k
	2: ![B5.1]
	T:  if [B5.2]
	Preds: B6
	Succs: B4 B3
 [B0(EXIT)]
	Preds: B1 B2 B4


int main()

 [B6(ENTRY)]
	Succs: B5
 [B1]
	1: printf
	2: "%05lld\n"
	3: pw
	4: atoi
	5: r
	6: [B1.4]([B1.5])
	7: 5
	8: [B1.3]([B1.6], [B1.7])
	9: [B1.1]([B1.2], [B1.8])
	10: 0
	11: return [B1.10];	Preds: B4
	Succs: B0
 [B2]
	1: i
	2: [B2.1]++	Preds: B3
	Succs: B4
 [B3]
	1: s
	2: S
	3: i
	4: [B3.2][[B3.3]]
	5: [B3.1][[B3.4]]
	6: r
	7: i
	8: [B3.6][[B3.7]]
	9: [B3.8] = [B3.5]	Preds: B4
	Succs: B2
 [B4]
	1: i
	2: 5
	3: [B4.1] < [B4.2]
	T:  for (...; [B4.3]; ...)
	Preds: B2 B5
	Succs: B3 B1
 [B5]
	1: scanf
	2: "%s"
	3: s
	4: [B5.1]([B5.2], [B5.3])
	5: 0
	6: int i(0);	Preds: B6
	Succs: B4
 [B0(EXIT)]
	Preds: B1
```

IO Descriptions

Input

The only line of the input contains a positive integer five digit number for which the activation code should be found.

Output

Output exactly 5 digits without spaces between them — the found activation code of the program.

Examples

Input

12345


Output

71232

### Assistant

Program description:
This C++ program performs a specific transformation and calculation based on the input string representing a number. The main components of the program include:

1. A power function `pw` that computes `n^k % 100000`, where `n` is the base and `k` is the exponent. It uses recursion and the property that `(a*b) % m = ((a % m) * (b % m)) % m` to efficiently calculate large powers modulo `100000`. The function optimizes the calculation by reducing the exponent by half in each recursive call when the exponent is even, and by one when it's odd.

2. A character array `s` is used to store the input string, and another character array `r` is intended to store a rearranged version of `s`.

3. The `S` array defines a specific mapping from indices of `s` to indices of `r`. This mapping rearranges the digits of the input number according to the order specified in `S`.

4. In the `main` function, the program reads a string into `s`, rearranges its characters into `r` according to the mapping defined by `S`, then converts `r` into an integer. It calculates `pw(atoi(r), 5)` – that is, `atoi(r)^5 % 100000`, and prints the result formatted as a 5-digit number with leading zeros if necessary.

Translation steps:
1. **Use Rust's I/O and string manipulation**: Rust provides more safety around string manipulation and I/O operations. We'll use `std::io` for input and `println!` for output, ensuring proper error handling when converting strings to numbers.

2. **Function `pw` translation**: The recursive power function will be translated directly, maintaining its logic but ensuring Rust's ownership and type rules are followed. Rust also does not require explicit modulo operation parentheses due to its operator precedence rules, which match mathematical conventions.

3. **Character array and rearrangement**: Rust has more explicit handling of strings and their encoding. We'll use a `String` for `s` and manipulate it using Rust's `chars()` and indexing. Rust strings are UTF-8 encoded, so direct byte manipulation should be done with care. However, since the input is numeric, this simplification will be safe for this context.

4. **Array `S` and rearrangement logic**: We'll replicate this logic in Rust, utilizing its powerful iterators and indexing capabilities.

5. **Error handling and `unwrap`**: Rust requires handling potential errors, such as those that might occur when parsing a string to an integer. We'll use `unwrap` for simplicity, but in a production environment, more robust error handling would be advised.

### User

The steps looks good please provide the complete code translation. 

### Assistant

```rust
use std::io::{self, Read};
use std::convert::TryInto;

fn pw(n: i64, k: i64) -> i64 {
    // pw1:\nif (!k)
    if k == 0 {
        //pw2:\nreturn 1;
        return 1;
    }
    // pw3:\nif (k & 1)
    if k & 1 == 1 {
        // pw4:\nreturn n * pw(n, k - 1) % (100000);
        return (n * pw(n, k - 1)) % 100000;
    }
    // pw5:\nlet t: long long = pw(n, k / 2);\nreturn t * t % (100000);
    let t = pw(n, k / 2);
    (t * t) % 100000
}

fn main() {
    // main1:\nlet s[8], r[8]: chat; let S[]: int = int S[] = \{0, 2, 4, 3, 1\};\nlet s = read_from_stdin_buffer()
    let mut s = String::new();
    let mut r = vec![0;5];
    let s_indices: [usize; 5] = [0, 2, 4, 3, 1]; 
    io::stdin().read_to_string(&mut s).unwrap();
    
    // main2:\nfor: i=0 to \<5
    for i in 0..5 {
        r[i] = s.chars().nth(s_indices[i]).unwrap() as u8;
    }
    
    // main5:\nprintln(\{:05\}, pw(to_int(r), 5))
    println!("{:05}", pw(String::from_utf8(r).unwrap().parse::<i64>().unwrap(), 5));
}

```