# Progress Summary

## Current Set Up

### Assumptions

- Unit tests are given for each translations task
- If a translation attempt passed all unit tests, then the translation is considered successful
- All inputs and outputs from standard in and standard out

### Translation Issues That Are Ignored

- I/O differences between C++ and Rust
  - C++ use `scanf` or `cin` as to read from stdin, which reads and parse a string until a "whitespace"(`\n`, `\t`, `\s`) is encountered
  - Rust rust `std::io::stdin`, to read from stdin, which can: 1) read till end of line, 2) read till EOF is encountered
  - This sometimes cause differences in edges cases between C++ & Rust program when reading stdin: location of line breaks does not matter for C++ program but is critical for Rust program. Also, LLM may use read till EOF to bridge this gap, but that will require user to manually enter an EOF token to stdin which C++ programs don't need.
  - **Reason to Ignore**: This is not an interesting translation issue for now. Can potentially be solved by finetuning LLM on rust programs that use C-like stdin packages. The formatting of stdin is not critical outside of code contest.

### Small Scale Experiments Setup

- LLM: Using `gpt-4-0125-preview`, temperature at 0.8
- Test on 5 [interesting tasks](Interesting.md) selected from CodeContest dataset that
  - GPT-3.5 cannot generate compilable programs in 10 trials
  - GPT-4 cannot generate successful translation in 5 trials (with no few shot examples)  
- Few shot examples picked from CodeContest dataset, translated to Rust by GPT & human
- Specific setup is given in [naive prompt](./results/naive_prompt.md), [chain of thought translation](./results/cot.md), [chain of thought w/ repair](./results/cot_repair.md)

## Result Summary

The following table shows the result of small scale tests, each cell shows:

**{success}/{compilation success}/{total samples}**

| Method\Task    | 1     | 2     | 3     | 4     | 5     |
| -------------- | ----- | ----- | ----- | ----- | ----- |
| Naive few shot | 0/3/3 | 0/2/4 | 1/3/4 | 0/0/4 | 0/0/4 |
| COT            | 0/2/3 | 0/1/4 | 2/4/4 | 0/0/4 | 2/2/4 |
| COT + repair   | 3/3/3 | 1/1/4 | 2/4/4 | 1/1/4 | 3/3/4 |

