# Naive Result

## Method Description

- System prompt & few shot examples shown [here](../prompts/naive_translation.md)
- Few Shot examples picked from CodeContest dataset
- Only given c++ code as input
- Temperature = 0.8
- Take 4 samples

## Result Summary

- [**Task 1**](./naive_prompt/task_1.md): No compile error, always bit shift overflow at runtime
- [**Task 2**](./naive_prompt/task_2.md): 2 out of 4 with 1 minor runtime error, other 2 with multiple type error.
- [**Task 3**](./naive_prompt/task_3.md): 2 use unsafe, 1 miss one global variable in helper function signature, 1 success
- [**Task 4**](./naive_prompt/task_4.md): All with multiple type errors ( > 3 )
- [**Task 5**](./naive_prompt/task_5.md): All need to add copy trait to point struct