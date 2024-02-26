# Method Proposal 0214

## Motivation

### Analysis of LLM Failure

As models and prompts improve, majority of LLM failures are now runtime failures
- Failure decomposition:
  - Failure at compile time: 35.3% 
  - Failure at runtime: 64.7%

Compare to compile time failure, runtime failures are especially difficult to fix, as LLM struggle to locate the buggy codes.
- Repair Success:
  - Compiler Error: 20%
  - Runtime Error: 10.9%

### Analysis of Runtime Failure


