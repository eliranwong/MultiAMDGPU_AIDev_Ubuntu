# Speed Test

Loading the same model file: llama3.3:70b

The same question asked: What is generative AI?

# Llama.cpp with ROCm backend

slot launch_slot_: id  0 | task 0 | processing task
slot update_slots: id  0 | task 0 | new prompt, n_ctx_slot = 2048, n_keep = 0, n_prompt_tokens = 99
slot update_slots: id  0 | task 0 | kv cache rm [0, end)
slot update_slots: id  0 | task 0 | prompt processing progress, n_past = 99, n_tokens = 99, progress = 1.000000
slot update_slots: id  0 | task 0 | prompt done, n_past = 99, n_tokens = 99
slot      release: id  0 | task 0 | stop processing: n_past = 496, truncated = 0
slot print_timing: id  0 | task 0 | 
prompt eval time =     690.52 ms /    99 tokens (    6.97 ms per token,   143.37 tokens per second)
       eval time =   30117.78 ms /   398 tokens (   75.67 ms per token,    13.21 tokens per second)
      total time =   30808.30 ms /   497 tokens

# Llama.cpp with Vulkan backend

slot launch_slot_: id  0 | task 0 | processing task
slot update_slots: id  0 | task 0 | new prompt, n_ctx_slot = 2048, n_keep = 0, n_prompt_tokens = 99
slot update_slots: id  0 | task 0 | kv cache rm [0, end)
slot update_slots: id  0 | task 0 | prompt processing progress, n_past = 99, n_tokens = 99, progress = 1.000000
slot update_slots: id  0 | task 0 | prompt done, n_past = 99, n_tokens = 99
slot      release: id  0 | task 0 | stop processing: n_past = 423, truncated = 0
slot print_timing: id  0 | task 0 | 
prompt eval time =    1736.12 ms /    99 tokens (   17.54 ms per token,    57.02 tokens per second)
       eval time =   46466.87 ms /   325 tokens (  142.97 ms per token,     6.99 tokens per second)
      total time =   48203.00 ms /   424 tokens

# Ollama

total duration:       41.153087104s
load duration:        13.202153ms
prompt eval count:    16 token(s)
prompt eval duration: 1.109s
prompt eval rate:     14.43 tokens/s
eval count:           526 token(s)
eval duration:        40.029s
eval rate:            13.14 tokens/s
