# Running Local LLM

## [LM Studio](vscode-remote://ssh-remote%2Butopia3/home/han/myproj/code-translate/code_translate/llm_experiment/chat/llm_list.md)

- Best for playing with smaller models on MacBook
- UI pretty intuitive

## Llama.cpp server

- Clone [Llama.cpp](https://github.com/ggerganov/llama.cpp)
- Build with `make LLAMA_CUDA=1`
- Run [Llama.cpp server](vscode-remote://ssh-remote%2Butopia3/home/han/myproj/code-translate/code_translate/llm_experiment/chat/llm_list.md)
  - command: `./server -m {path_to_llm_model} -c {context_size}`
  - llm models stored at `/mnt/nas/han/llms/{model_family}/{specific_model}/ggml-ggml-model-{quantization_level}.gguf`
  - Web UI will be run on port 8080
- SSH tunnel to localhost
  - `ssh -L 8080:localhost:8080 utopia3.csres.utexas.edu`
- **NOTE**: need to set chat format by hand, here is model to chat format correspondence (also in [llama_cpp_engine.py](https://github.com/thoughtp0lice/llm_experiment/blob/main/llm_engines/llama_cpp_engine.py)):
  ```python
    def _model_name_to_chat_format(model_name):
        if model_name.startswith("CodeLlama"):
            return "llama-2"
        elif model_name.lower().startswith("llama-2"):
            return "llama-2"
        elif model_name.lower().startswith("mixtral"):
            return "mistral-instruct"
        elif model_name.lower().startswith("dolphin"):
            return "chatml"
        elif model_name.lower().startswith("nous-hermes"):
            return "chatml"
        elif model_name.lower().startswith("phind"):
            return "alpaca"
        elif model_name.lower().startswith("codebooga"):
            return "alpaca"
        elif model_name.lower().startswith("wizardcoder"):
            return "alpaca"
  ```

## Run from LLM [llm_experiment/chat](https://github.com/thoughtp0lice/llm_experiment/tree/main/chat)
- dependencies
  - `lxml`, `fire` 
  - install llama-cpp-python with `CMAKE_ARGS="-DLLAMA_CUBLAS=on" pip install llama-cpp-python --force-reinstall --upgrade --no-cache-dir`
  - Or use `code-trans` conda env on Han's
- Set message history in `history.xml` 
  - `<message>` is the root node
  - `<system>` for system message and so on ...
  - **Note**: Must to do xml escape for most text. Here is a too for that https://www.freeformatter.com/xml-escape.html
- run `python chat/llama_cpp_chat.py`
  - Arguments:
    - `-m`, `--model_name` sets the `specific_model` name for the model being used, check `llm_list.md` for supported LLMs.
    - `-q`, `--quantization_level` sets quantization_level
    - `-h`, `--hist_file` set the file for chat history default to `history.xml`
  - When running
    - The program will print a bunch of log, to remove that change the global variable `MODEL_CONFIG['verbose']` to false
    - When ">" appears, the model is fully loaded to gpu. Type anything and enter. The program will read the messages from xml, then append llm response to xml and also print to console
    - To continue chatting with LLM, just add to xml and save. Type anything and enter after ">"
    - Type "q" to quit
## Appendix: Common Chat Format

### ChatML

```
<|im_start|>system 
Provide some context and/or instructions to the model.
<|im_end|> 
<|im_start|>user 
The user’s message goes here
<|im_end|> 
<|im_start|>assistant 
```

### Alpaca

```
Below is an instruction that describes a task. Write a response that appropriately completes the request.

### Instruction:
{instruction}

### Response:
```

### LLama-2

```
<s> [INST] <<SYS>>
You are a Llama
<</SYS>>
[INST] Hi! [/INST]
Hello! How are you?
[INST] I'm great, thanks for asking. Could you help me with a task? [/INST]
```