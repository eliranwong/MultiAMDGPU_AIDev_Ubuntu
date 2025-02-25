# Speed Test: CPU vs CPU+GPUx2

To test, I ran the same prompt `What is machine learning?` with the same model file `mistral.gguf`, using [llama.cpp](https://github.com/ggerganov/llama.cpp), on the same machine.

# CPU only

Build from source for CPU only:

```
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp
make -j$(lscpu | grep -m 1 '^Core(s)' | awk '{print $NF}')
```

To run:

> ./llama-cli -t $(lscpu | grep -m 1 '^Core(s)' | awk '{print $NF}') --temp 0 -m ../mistral.gguf -p "What is machine learning?"

Output:

```
llm_load_tensors: ggml ctx size =    0.14 MiB
llm_load_tensors:        CPU buffer size =  3917.87 MiB
...
...
...
llama_print_timings:        load time =    1602.50 ms
llama_print_timings:      sample time =      11.38 ms /   571 runs   (    0.02 ms per token, 50193.39 tokens per second)
llama_print_timings: prompt eval time =      81.22 ms /     6 tokens (   13.54 ms per token,    73.88 tokens per second)
llama_print_timings:        eval time =   24270.78 ms /   570 runs   (   42.58 ms per token,    23.49 tokens per second)
llama_print_timings:       total time =   24522.14 ms /   576 tokens
```

# CPU + GPU x 2

Build from source for GPU acceleration with ROCm:

```
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp
make GGML_HIPBLAS=1 AMDGPU_TARGETS=gfx1100 -j$(lscpu | grep -m 1 '^Core(s)' | awk '{print $NF}')
```

Remarks: Use `GGML_HIPBLAS` instead of `LLAMA_HIPBLAS`

To run:

> ./llama-cli -t $(lscpu | grep -m 1 '^Core(s)' | awk '{print $NF}') --temp 0 -m ../mistral.gguf -p "What is machine learning?" -ngl 33

Output:

```
ggml_cuda_init: found 2 ROCm devices:
  Device 0: Radeon RX 7900 XTX, compute capability 11.0, VMM: no
  Device 1: Radeon RX 7900 XTX, compute capability 11.0, VMM: no
llm_load_tensors: ggml ctx size =    0.41 MiB
llm_load_tensors: offloading 32 repeating layers to GPU
llm_load_tensors: offloading non-repeating layers to GPU
llm_load_tensors: offloaded 33/33 layers to GPU
llm_load_tensors:      ROCm0 buffer size =  1989.53 MiB
llm_load_tensors:      ROCm1 buffer size =  1858.02 MiB
llm_load_tensors:        CPU buffer size =    70.31 MiB
...
...
...
llama_print_timings:        load time =    3440.52 ms
llama_print_timings:      sample time =      17.78 ms /   952 runs   (    0.02 ms per token, 53540.30 tokens per second)
llama_print_timings: prompt eval time =      12.38 ms /     6 tokens (    2.06 ms per token,   484.46 tokens per second)
llama_print_timings:        eval time =    8928.70 ms /   951 runs   (    9.39 ms per token,   106.51 tokens per second)
llama_print_timings:       total time =    9119.94 ms /   957 tokens
```

# Result

The difference in speed is more than obvious.