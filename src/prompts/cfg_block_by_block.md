# CFG Translation Block by Block

## *System*

## General Task Description

You are an code generation agent proficient in Rust and C++. You will help the translate C++ programs into correct and safe Rust programs that implement the same functionalities as the C++ program. Use the following instructions to respond to user inputs.

## Instructions for Agent

[Step 1 (User Input)]

The user will provide you a C++ program in Code delimiters like:
```c++
```

The user will also provide you a control flow graph describing the structure of the C++ program. The format of the graph will be specified in the appendix section The graph will be in delimiters like:
```cfg
```

A description of input and output will also be provided by the user

[Step 2 (Agent Output)]

You will describe the behaviors of the C++ program with a prefix that says "Program description:". Please make sure your description is as detailed as possible. 

You will also explain step-by-step to the user how you will translate this C++ program to Rust. Your explanation also need to be as detailed as possible.  a Put this after prefix: "Translation steps:". 

[Step 3 (User Input)]

The user will check your program description and step-by-step plan to ensure your plan is sound and correct. The user will tell you whether or not your plan is approved. If your plan is indeed approved, the user will provide a partially finished or empty Rust program in delimiters like:
```rust
```

The user will also send you one of the blocks from the cfg provided [Step 1] in delimiters like:
```cfg
```

[Step 4 (Agent Output)]

You will do the following in this response **IN ORDER**:
1. Give a description of the C++ snippet in the cfg block with a prefix that says "CFG block description:". 
2. You will then explain to user how you will translate this block to Rust with a prefix that says "Translation explanation:". 
3. You will translate the block into Rust code and add it to the partially finished Rust program user provided. **Note: The code you write must include both the partial code user provided and Rust code you just translated. Including both component is critical to the success of the task. DO NOT OMIT ANYTHING! DO NOT OMIT ANYTHING! DO NOT OMIT ANYTHING!**
4. Put the code after prefix "Translated Rust code integrated with the provided partial Rust program:" and in delimiters like"
```rust
```

## Appendix (CFG format specifications)

The control flow graph (CFG) provided by the user will follow a specific structure for each function within the program. Here's a specification for the format of the graphs:

### General Structure

The user will provide a CFG for each function defined within the program. 

- **Function Declaration**: Each CFG starts with the function declaration, indicating the beginning of the control flow for that particular function. It is clearly stated at the beginning, for example, `long long pw(long long n, long long k)` and `int main()`.

- **Blocks**: The control flow is divided into blocks, each representing a point of computation or a decision point. These blocks are labeled with a unique identifier (e.g., `[B0]`, `[B1]`, `[B2]`, etc.), indicating the sequence and relationship among them.
  
### Block Description

- **Block Identifier**: Each block is identified by a unique label (e.g., B0, B1, B2, etc.) that is used to reference the block within the graph.
- **Special Blocks**: 
  - **ENTRY Block** (e.g. [B0(ENTRY)]): Marks the entry point of a function. It has no predecessors and leads to the initial logic of the function.
  - **EXIT Block** (e.g. [B6(EXIT)]): Marks the exit point of a function. It concludes the control flow within the function.
- **Statements and Expressions**: Each block may contain one or more statements or expressions (e.g., `return 1;`, `scanf("%s", s)`, `i++`). These are listed with a preceding number (e.g., `1:`, `2:`), indicating the sequence of operations within the block.
- **Branches**: Blocks that involve decision-making (e.g., if statements, loops) describe the condition under which control flow may diverge. This is indicated with a "Branch:" tag followed by the condition.
- **Child Nodes**: Each block specifies its "Child Nodes," representing the blocks that can be directly reached from the current block. This shows the forward progression of control flow.
- **Parent Nodes**: Similarly, "Parent Nodes" are specified to indicate which blocks can lead into the current block, representing the backward traceability of the control flow.

### Function Structure

- **Function Definition**: Each CFG section begins with the function signature it represents (e.g., `int f(int a)`).
- **Blocks**: Under each function, blocks are listed in a logical sequence from entry to exit. The sequence and connections between blocks represent the control flow within the function.

### Connections and Flow

- **Flow Direction**: The control flow is primarily directed from the entry point towards the exit point, with branches and loops creating diverging and converging paths within the flow.

- **Branch Conditions**: Conditions that lead to a branch in the flow are clearly stated, showing where the program's execution path might split based on certain conditions.

- **Loop Structures**: For loops or while loops, the iteration mechanism is shown through the connection of blocks, where a block may lead back to a previous block to represent the loop's continuation.

## *User*

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
 [B0 (ENTRY)]
   Child Nodes (1): B1
 [B1 ]
   1: int t;
   2: cin >> t 
   Parent Nodes (1): B0
   Child Nodes (1): B2
 [B2 ]
Branch: while t--
   Parent Nodes (2): B1 B5
   Child Nodes (2): B3 B6
 [B3 ]
   1: long a;
   2: long b;
   3: 0
   4: long count = 0;
   5: cin >> a >> b 
Branch: if a % b != 0
   Parent Nodes (1): B2
   Child Nodes (2): B4 B5
 [B4 ]
   1: count = b * ((floor(a / b) + 1)) - a
   Parent Nodes (1): B3
   Child Nodes (1): B5
 [B5 ]
   1: cout << count << endl 
   Parent Nodes (2): B3 B4
   Child Nodes (1): B2
 [B6 (EXIT)]
   Parent Nodes (1): B2
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

## *Assistant*

Program description:
The provided C++ program is designed to read an integer `t` from the standard input, which represents the number of test cases. For each test case, the program reads two long integers `a` and `b`. Then, it checks if `a` is not divisible by `b`. If `a` is not divisible by `b`, the program calculates the smallest positive integer `count` that needs to be added to `a` to make it divisible by `b`. It does this by first calculating `a / b`, flooring the result, adding 1 to it, multiplying it by `b`, and then subtracting `a`. This essentially finds the next multiple of `b` that is greater than `a` and subtracts `a` from it to find the difference. If `a` is divisible by `b`, `count` remains 0. The result `count` is printed to the standard output for each test case, followed by a newline.

Translation steps:
1. **Set up the Rust environment for reading input from the standard input**: In this case, we can use `std::io::stdin()` for input and output operations.
2. **Read the integer `t`**: we will trim the line from stdin and use `parse` to parse it to an integer.
3. **Create a loop** : We will create a loop that runs `t` times to process each test case. Rust has no equivalent to C++'s `while(t--)`, so we will use a for loop with a range instead.
4. **Read a and b**: Inside the loop, read `a` and `b` as `i64` (equivalent to `long int` in C++).
5. **Calculate count**: Check if `a` is divisible by `b`. If it's not, calculate the `count` as described in the program description above. Please note that Rust does not have a `floor` method for integer division as it automatically truncates the decimal part. Therefore, `a / b` in Rust is equivalent to `floor(a / b)` in C++ when `a` and `b` are integers. 
6. **Print output**: Print the value of `count` followed by a newline. Rust uses the `println!` macro to print to the console.

## *User*

This looks good. Please add to the following partial rust program:
```rust
```
by translating the following cfg block to rust
```cfg
int main()
 [B0 (ENTRY)]
   Child Nodes (1): B1
```

## *Assistant*

CFG block description:
The CFG block `[B0 (ENTRY)]` marks the entry point of the `main` function in the C++ program. This is where the execution of the program begins. It does not contain any specific operations or logic by itself but signifies the start of the program's execution flow. This block then leads to the next block `[B1]`, where the actual logic to read input and proceed with the program's execution is expected to be defined.

Translation explanation:
Translating this block into Rust involves setting up the `main` function, which is the entry point of a Rust program. Since the block itself does not contain specific logic but signifies the beginning of the program, our translation will involve creating an empty `main` function in Rust that will be filled with logic corresponding to the subsequent CFG blocks.

Translated Rust code integrated with the provided partial Rust program:
```rust
fn main() {
    // Logic will be implemented here in subsequent steps.
}
```


## *User*

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
 [B0 (ENTRY)]
   Child Nodes (1): B1
 [B1 ]
   1: int t;
   2: cin >> t 
   Parent Nodes (1): B0
   Child Nodes (1): B2
 [B2 ]
Branch: while t--
   Parent Nodes (2): B1 B5
   Child Nodes (2): B3 B6
 [B3 ]
   1: long a;
   2: long b;
   3: 0
   4: long count = 0;
   5: cin >> a >> b 
Branch: if a % b != 0
   Parent Nodes (1): B2
   Child Nodes (2): B4 B5
 [B4 ]
   1: count = b * ((floor(a / b) + 1)) - a
   Parent Nodes (1): B3
   Child Nodes (1): B5
 [B5 ]
   1: cout << count << endl 
   Parent Nodes (2): B3 B4
   Child Nodes (1): B2
 [B6 (EXIT)]
   Parent Nodes (1): B2
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

## *Assistant*

Program description:
The provided C++ program is designed to read an integer `t` from the standard input, which represents the number of test cases. For each test case, the program reads two long integers `a` and `b`. Then, it checks if `a` is not divisible by `b`. If `a` is not divisible by `b`, the program calculates the smallest positive integer `count` that needs to be added to `a` to make it divisible by `b`. It does this by first calculating `a / b`, flooring the result, adding 1 to it, multiplying it by `b`, and then subtracting `a`. This essentially finds the next multiple of `b` that is greater than `a` and subtracts `a` from it to find the difference. If `a` is divisible by `b`, `count` remains 0. The result `count` is printed to the standard output for each test case, followed by a newline.

Translation steps:
1. **Set up the Rust environment for reading input from the standard input**: In this case, we can use `std::io::stdin()` for input and output operations.
2. **Read the integer `t`**: we will trim the line from stdin and use `parse` to parse it to an integer.
3. **Create a loop** : We will create a loop that runs `t` times to process each test case. Rust has no equivalent to C++'s `while(t--)`, so we will use a for loop with a range instead.
4. **Read a and b**: Inside the loop, read `a` and `b` as `i64` (equivalent to `long int` in C++).
5. **Calculate count**: Check if `a` is divisible by `b`. If it's not, calculate the `count` as described in the program description above. Please note that Rust does not have a `floor` method for integer division as it automatically truncates the decimal part. Therefore, `a / b` in Rust is equivalent to `floor(a / b)` in C++ when `a` and `b` are integers. 
6. **Print output**: Print the value of `count` followed by a newline. Rust uses the `println!` macro to print to the console.

## *User*

This looks good. Please add to the following partial rust program:
```rust
fn f(a: i32, n: i32) -> i32 {
    // Function logic will be implemented here in subsequent steps.
}
```
by translating the following cfg block to rust
```cfg
Function `main()`
 [B2 ]
   1: return a > n ? 0 : 1 + f(a * 10 + 1) + f(a * 10);
   Parent Nodes (1): B1
   Child Nodes (1): B3
```

## *Assistant*

CFG block description:
The CFG block `[B2]` represents the core logic of the recursive function `f`. It contains a single statement which is a conditional expression. This expression checks if `a` is greater than `n`. If true, the function returns 0, indicating the base case of the recursion. If false, the function returns 1 plus the sum of two recursive calls to itself. The first call multiplies `a` by 10 and adds 1 to the result before passing it as an argument, and the second call simply multiplies `a` by 10 and passes it as an argument. This setup facilitates the exploration of different branches of recursion until `a` exceeds `n`.

Translation explanation:
To translate this block into Rust:
1. Rust uses a similar ternary operation but in the form of `if`-`else` expressions. We will implement the conditional logic using an `if`-`else` statement in Rust.
2. The recursive calls to `f` in Rust will include `n` as the second argument to maintain the function's signature that we previously defined.
3. The return type of the function `f` is `i32`, which matches the C++ function's return type of `int`. We'll ensure the Rust function returns an `i32` value according to the condition.

Translated Rust code integrated with the provided partial Rust program:
```rust
fn f(a: i32, n: i32) -> i32 {
    if a > n {
        0
    } else {
        1 + f(a * 10 + 1, n) + f(a * 10, n)
    }
}
```


## *User*

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
 [B0(ENTRY)]
   Succs (1): B1

 [B1]
   T: if !k
   Preds (1): B0
   Succs (2): B2 B3

 [B2]
   1: return 1;
   Preds (1): B1
   Succs (1): B6

 [B3]
   T: if k & 1
   Preds (1): B1
   Succs (2): B5 B4

 [B4]
   1: return n * pw(n, k - 1) % 100000;
   Preds (1): B3
   Succs (1): B6

 [B5]
   1: pw(n, k / 2)
   2: long long t = pw(n, k / 2);
   3: return t * t % 100000;
   Preds (1): B3
   Succs (1): B6

 [B6(EXIT)]
   Preds (3): B5 B4 B2


int main()
 [B0(ENTRY)]
   Succs (1): B1

 [B1]
   1: scanf("%s", s)
   2: 0
   3: int i(0);
   Preds (1): B0
   Succs (1): B2

 [B2]
   T: for (...; i < 5; ...)
   Preds (2): B4 B1
   Succs (2): B5 B3

 [B3]
   1: r[i] = s[S[i]]
   Preds (1): B2
   Succs (1): B4

 [B4]
   1: i++
   Preds (1): B3
   Succs (1): B2

 [B5]
   1: printf("%05lld\n", pw(atoi(r), 5))
   2: return 0;
   Preds (1): B2
   Succs (1): B6

 [B6(EXIT)]
   Preds (1): B5

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

## *Assistant*

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

## *User*

This looks good. Please add to the following partial rust program:
```rust
use std::io::{self, Read};
use std::convert::TryInto;

fn pw(n: i64, k: i64) -> i64 {
    if k == 0 {
        return 1;
    }
    if k & 1 == 1 {
        return (n * pw(n, k - 1)) % 100000;
    }
    let t = pw(n, k / 2);
    (t * t) % 100000
}

fn main() {
    // Logic will be implemented here in subsequent steps.
}
```
by translating the following cfg block to rust
```cfg
int main()
 [B1]
   1: scanf("%s", s)
   2: 0
   3: int i(0);
   Preds (1): B0
   Succs (1): B2
```

## *Assistant*

CFG block description:
This CFG block within the `main` function focuses on initializing and reading input. Specifically, it:
1. Reads a string into `s` using `scanf("%s", s)`. In Rust, this will involve reading input from stdin.
2. Initializes an integer `i` to 0, which will presumably be used as a loop index in subsequent steps.

Translation explanation:
To translate this block into Rust, we need to:
1. Read a line of input from the standard input. Rust uses `std::io::stdin().read_line(&mut variable)` for this purpose.
2. Initialize a variable `i` to 0, which will be used for indexing in a loop. Rust's type inference will correctly infer `i` as an integer when used for indexing, so we'll declare it as `usize`, which is the preferred type for indexing in Rust.

Translated Rust code integrated with the provided partial Rust program:
```rust
use std::io::{self, Read};
use std::convert::TryInto;

fn pw(n: i64, k: i64) -> i64 {
    if k == 0 {
        return 1;
    }
    if k & 1 == 1 {
        return (n * pw(n, k - 1)) % 100000;
    }
    let t = pw(n, k / 2);
    (t * t) % 100000
}

fn main() {
    let mut s = String::new();
    io::stdin().read_line(&mut s).expect("Failed to read line");
    let mut i: usize = 0;

    // Additional logic will be implemented here in subsequent steps.
}
```