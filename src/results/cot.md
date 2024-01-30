# Chain of Thought Result

## Method Description

- System prompt & few shot examples shown [here](../prompts/cot_translation.md)
- Few Shot examples picked from CodeContest dataset, reasoning given by gpt-4-turbo + human tuning.
- Only given c++ code as input
- Temperature = 0.8
- Take 4 samples

## Result Summary

- [**Task 1**](./naive_prompt/task_1.md): 1 with 1 type error, all with bit shift overflow.
- [**Task 2**](./naive_prompt/task_2.md): 1 with 1 type error, other 3 with runtime errors
- [**Task 3**](./naive_prompt/task_3.md): 2 success, 2 with runtime error
- [**Task 4**](./naive_prompt/task_4.md): 3 with 1 or 2 type error or grammar error, 1 with multiple (> 3) type errors
- [**Task 5**](./naive_prompt/task_5.md): 2 success , 2 need to add copy trait to point struct