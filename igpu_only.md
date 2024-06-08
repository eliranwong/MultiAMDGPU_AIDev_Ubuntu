# Tested on a iGPU-only device

Tested device: Beelink GTR6 (Ryzen 9 6900HX CPU + integrated Radeon 680M GPU + 64GB RAM)

Followed https://github.com/eliranwong/MultiAMDGPU_AIDev_Ubuntu/blob/main/README.md for for installation ROCm 6.0.2.

Environment variables:

```
export ROCM_HOME=/opt/rocm-6.0.2
export LD_LIBRARY_PATH=/opt/rocm-6.0.2/include:/opt/rocm-6.0.2/lib:$LD_LIBRARY_PATH
export PATH=$HOME/.local/bin:/opt/rocm-6.0.2/bin:/opt/rocm-6.0.2/llvm/bin:$PATH
export HSA_OVERRIDE_GFX_VERSION=10.3.0
```

# Compile llama.cpp from source:

Compiled with ROCM:

> make LLAMA_HIPBLAS=1 LLAMA_HIP_UMA=1 AMDGPU_TARGETS=gfx1030 -j$(lscpu | grep '^Core(s)' | awk '{print $NF}')

Remarks: Do NOT use LLAMA_HIP_UMA with discrete GPUs.

# Compare CPU vs OpenBLAS vs ROCm vs ROCm+iGPU offloading

Setup 3 copies:

```
# Set up in home directory
cd ~
# CPU ONLY
git clone https://github.com/ggerganov/llama.cpp
mv llama.cpp cpu
cd cpu
make -j$(lscpu | grep '^Core(s)' | awk '{print $NF}')
# CPU ONLY with OpenBLAS
cd ..
git clone https://github.com/ggerganov/llama.cpp
mv llama.cpp openblas
cd openblas
make LLAMA_OPENBLAS=1 -j$(lscpu | grep '^Core(s)' | awk '{print $NF}')
cd ..
git clone https://github.com/ggerganov/llama.cpp
mv llama.cpp rocm
cd rocm
make LLAMA_HIPBLAS=1 LLAMA_HIP_UMA=1 AMDGPU_TARGETS=gfx1030 -j$(lscpu | grep '^Core(s)' | awk '{print $NF}')
cd ..
```

# CPU

To test:

```
cd ~
cd cpu
./main -t $(lscpu | grep '^Core(s)' | awk '{print $NF}') --temp 0 -m 'mistral.gguf' -p "What is machine learning?"
```

Result:

```
llama_print_timings:        load time =    2326.54 ms
llama_print_timings:      sample time =       6.37 ms /   345 runs   (    0.02 ms per token, 54194.16 tokens per second)
llama_print_timings: prompt eval time =     226.95 ms /     6 tokens (   37.83 ms per token,    26.44 tokens per second)
llama_print_timings:        eval time =   31452.13 ms /   344 runs   (   91.43 ms per token,    10.94 tokens per second)
llama_print_timings:       total time =   31790.97 ms /   350 tokens
```

# OpenBLAS

To test:

```
cd ~
cd openblas
./main -t $(lscpu | grep '^Core(s)' | awk '{print $NF}') --temp 0 -m 'mistral.gguf' -p "What is machine learning?"
```

Result:

```
llama_print_timings:        load time =    2305.72 ms
llama_print_timings:      sample time =       4.46 ms /   345 runs   (    0.01 ms per token, 77423.70 tokens per second)
llama_print_timings: prompt eval time =     233.74 ms /     6 tokens (   38.96 ms per token,    25.67 tokens per second)
llama_print_timings:        eval time =   31242.08 ms /   344 runs   (   90.82 ms per token,    11.01 tokens per second)
llama_print_timings:       total time =   31580.83 ms /   350 tokens
```

# ROCm

To test:

```
cd ~
cd rocm
./main -t $(lscpu | grep '^Core(s)' | awk '{print $NF}') --temp 0 -m 'mistral.gguf' -p "What is machine learning?"
```

Result:

```
llama_print_timings:        load time =    2879.46 ms
llama_print_timings:      sample time =       4.79 ms /   345 runs   (    0.01 ms per token, 71979.97 tokens per second)
llama_print_timings: prompt eval time =     226.71 ms /     6 tokens (   37.79 ms per token,    26.47 tokens per second)
llama_print_timings:        eval time =   31528.21 ms /   344 runs   (   91.65 ms per token,    10.91 tokens per second)
llama_print_timings:       total time =   31875.51 ms /   350 tokens
```

# ROCm + iGPU offloading

We tested iGPU offloading, by appending the same command that tested ROCm above with '-ngl 33', i.e.:

```
cd ~
cd rocm
./main -t $(lscpu | grep '^Core(s)' | awk '{print $NF}') --temp 0 -m 'mistral.gguf' -p "What is machine learning?" -ngl 33
```

Result:

```
llama_print_timings:        load time =    3289.71 ms
llama_print_timings:      sample time =      11.92 ms /   343 runs   (    0.03 ms per token, 28775.17 tokens per second)
llama_print_timings: prompt eval time =     143.15 ms /     6 tokens (   23.86 ms per token,    41.91 tokens per second)
llama_print_timings:        eval time =   23456.76 ms /   342 runs   (   68.59 ms per token,    14.58 tokens per second)
llama_print_timings:       total time =   23803.76 ms /   348 tokens
```

## Observation

Inference with ROCm + iGPU offloading is roughly 1.5x faster (prompt eval time, eval time) than other tested methods.
