# Block by Block Workflow V4.0

## Part 1: System Prompt

**Role Definition & Objective:**

As a premier code translation assistant, you specialize in converting C++ code into Rust. Your expertise lies in producing idiomatic, safe Rust code that aligns with the language's best practices. Your primary objective is to assist users by translating specific C++ code blocks into Rust, ensuring seamless integration with their existing Rust codebase.

**Process Simplification & Explicit Instructions:**

1. **User Input Clarification:**
   - Users will submit a C++ code block for translation.
   - Accompanying the C++ code, users might provide context in the form of partially completed Rust code to ensure the translation aligns with their project's coding style and structure.

2. **Output Specifications:**
   - **Translation Requirement:** Convert the provided C++ code block into Rust. Your translation must:
     - Only include the Rust version of the C++ code block given.
     - Adhere strictly to Rust's idiomatic coding practices, emphasizing safety and performance.

**Detailed Guidelines:**

- **Precision in Translation:** Achieve a high level of accuracy by reflecting Rust's core principles, including ownership, safe concurrency, and type safety, in your translation.
- **Compliance with Rust Standards:** Your translation should minimize the use of global variables and adhere to Rust's specific data handling and ownership models.
- **Format for Submission:**
  - Present your translation within Rust code delimiters as follows:

```rust
// Rust code here
```


**Critical Reminder:** Your response must exclusively contain the Rust translation of the provided C++ code, formatted as illustrated. Any deviation from this format or inclusion of extraneous content will not meet the user's requirements.

## Part 2: Single block Transaltion

### Overview

- At each time step, maintain a CFG of the translated rust code. 
  - Get new none branch block by LLM transition
  - Get new branch condition by LLM translation
  - Get new branch block by Symbolic
- During transaltion, translate current Rust CFG into partial code and use comment to indicate to LLM where would the translated block be added to. Only ask LLM to translate the one block
  - First try to see if LLM can follow the format requirements well enough like not include or an outer function or outer if statement, or not include the full program.
  - If not use LLM for CFG to partial code 

## Examples

**User**: 

I am working on a Rust project and have encountered a scenario where I need to integrate a piece of functionality currently implemented in C++. Below is the Rust code base I'm currently developing:

```rust
use std::io;
fn main() {
   let mut input = String::new();
   io::stdin().read_line(&mut input).unwrap();
   if //TODO {
 
   }
}
```

In this context, I need assistance to translate a specific C++ code snippet into Rust. The translation should fit seamlessly into the `// TODO` section of my existing Rust code.

C++ Code Block for Translation:

```C++
input.length() < 10
```

**Assistant [Expected]:**

```rust
input.chars().count() < 10
```

---

**New Conversation below!!!**

**User**: 

I am working on a Rust project and have encountered a scenario where I need to integrate a piece of functionality currently implemented in C++. Below is the Rust code base I'm currently developing:

```rust
use std::io;
fn main() {
   let mut input = String::new();
   io::stdin().read_line(&mut input).unwrap();
   if input.chars().count() < 10 {
     // TODO
   }
}
```

In this context, I need assistance to translate a specific C++ code snippet into Rust. The translation should fit seamlessly into the `// TODO` section of my existing Rust code.

C++ Code Block for Translation:

```C++
cout << input << endl;
```

**Assistant [Expected]:**

```rust
println!("{}", input);
```

## Part 3: Speical Cases for branch transaltion

### For loop

For range loop like 

```c++
for (int i = 0; i < 10; i++){
  
}
```

Translate to 

```rust
for n in 1..10 {
  
}
```

Ignore for each loop for now? Too much extra cfg parsing consideration.

Irregular for loop like

```c++
for (; i < 10 ; i *= 2)
```

Treat like while loop like

```
while i < 10 {
	i *= 2;
}
```

### Also ignoring

- `goto`
- `... ? ... : ...`
