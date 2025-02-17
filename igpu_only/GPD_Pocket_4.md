# Tested on GPD Pocket 4

Tested device: AMD Ryzen™ AI 9 HX 370; AMD Radeon™ 890M

BIOS Memory Setting (reboot+DEL key):

UEFI/BIOS -> Advanced -> AMD CBS -> NBIO -> GFX Configuration > 

```
iGPU Advanced Control > Disabled
Dedicated Graphics Memory > Medium (16GB)
Remaining System Memory > 48GB
```

# ROCM Version

Tested version: 6.3.2

Followed https://github.com/eliranwong/MultiAMDGPU_AIDev_Ubuntu/blob/main/README.md for for installation ROCm 6.3.2.

Main commands for installation:

```
sudo usermod -a -G render,video $LOGNAME
sudo apt update
sudo apt install -y libstdc++-12-dev
wget https://repo.radeon.com/amdgpu-install/6.3.2/ubuntu/noble/amdgpu-install_6.3.60302-1_all.deb
sudo apt install ./amdgpu-install_6.3.60302-1_all.deb
sudo amdgpu-install --usecase=graphics,multimedia,rocm,rocmdev,rocmdevtools,lrt,opencl,openclsdk,hip,hiplibsdk,openmpsdk,mllib,mlsdk --no-dkms -y
sudo reboot
```

Environment variables:

```
export ROCM_HOME=/opt/rocm-6.3.2
export LD_LIBRARY_PATH=/opt/rocm-6.3.2/include:/opt/rocm-6.3.2/lib:$LD_LIBRARY_PATH
export PATH=$HOME/.local/bin:/opt/rocm-6.3.2/bin:/opt/rocm-6.3.2/llvm/bin:$PATH
export HSA_OVERRIDE_GFX_VERSION=11.5.0
```

Remarks about HSA:

1. Check `rocminfo` output first

2. General workaround if gfx version is not available in `rocminfo` output:

for GCN 5th gen based GPUs and APUs HSA_OVERRIDE_GFX_VERSION=9.0.0

for RDNA 1 based GPUs and APUs HSA_OVERRIDE_GFX_VERSION=10.1.0

for RDNA 2 based GPUs and APUs HSA_OVERRIDE_GFX_VERSION=10.3.0

for RDNA 3 based GPUs and APUs HSA_OVERRIDE_GFX_VERSION=11.0.0

for RDNA 3.5 based GPUs and APUs HSA_OVERRIDE_GFX_VERSION=11.5.0

3. Read more at: https://llvm.org/docs/AMDGPUUsage.html#processors

# Speed Test with Ollama

Tested prompt: What is machine learning?

Model: `deepseek-r1:1.5b`

```
load duration:        12.277249ms
prompt eval count:    8 token(s)
prompt eval duration: 53ms
prompt eval rate:     150.94 tokens/s
eval count:           177 token(s)
eval duration:        3.808s
eval rate:            46.48 tokens/s
```

Model: `deepseek-r1:7b`

```
total duration:       35.692882833s
load duration:        24.075692ms
prompt eval count:    8 token(s)
prompt eval duration: 254ms
prompt eval rate:     31.50 tokens/s
eval count:           563 token(s)
eval duration:        35.412s
eval rate:            15.90 tokens/s
```

Model: `deepseek-r1:8b`

```
total duration:       19.19488017s
load duration:        19.389461ms
prompt eval count:    8 token(s)
prompt eval duration: 204ms
prompt eval rate:     39.22 tokens/s
eval count:           284 token(s)
eval duration:        18.969s
eval rate:            14.97 tokens/s
```

Model: `deepseek-r1:14b`

```
total duration:       1m29.657527203s
load duration:        19.170988ms
prompt eval count:    8 token(s)
prompt eval duration: 376ms
prompt eval rate:     21.28 tokens/s
eval count:           660 token(s)
eval duration:        1m29.261s
eval rate:            7.39 tokens/s
```

Model: `deepseek-r1:32b`

```
total duration:       5m53.281645538s
load duration:        18.26359ms
prompt eval count:    8 token(s)
prompt eval duration: 852ms
prompt eval rate:     9.39 tokens/s
eval count:           1308 token(s)
eval duration:        5m52.409s
eval rate:            3.71 tokens/s
```

# Test Speed with llama.cpp - CPU Backend

> ./llama-bench -t $(lscpu | grep '^Core(s)' | awk '{print $NF}') -m '/home/eliran/agentmake/models/gguf/deepseek-r1_1.5b.gguf'

```
| model                          |       size |     params | backend    | threads |          test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | ------: | ------------: | -------------------: |
| qwen2 1.5B Q4_K - Medium       |   1.04 GiB |     1.78 B | CPU        |      12 |         pp512 |        227.01 ± 0.18 |
| qwen2 1.5B Q4_K - Medium       |   1.04 GiB |     1.78 B | CPU        |      12 |         tg128 |         65.23 ± 0.80 |
```

> ./llama-bench -t $(lscpu | grep '^Core(s)' | awk '{print $NF}') -m '/home/eliran/agentmake/models/gguf/deepseek-r1_7b.gguf'

```
| model                          |       size |     params | backend    | threads |          test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | ------: | ------------: | -------------------: |
| qwen2 7B Q4_K - Medium         |   4.36 GiB |     7.62 B | CPU        |      12 |         pp512 |         49.13 ± 0.40 |
| qwen2 7B Q4_K - Medium         |   4.36 GiB |     7.62 B | CPU        |      12 |         tg128 |         17.32 ± 0.20 |
```

> ./llama-bench -t $(lscpu | grep '^Core(s)' | awk '{print $NF}') -m '/home/eliran/agentmake/models/gguf/deepseek-r1_8b.gguf'

```
| model                          |       size |     params | backend    | threads |          test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | ------: | ------------: | -------------------: |
| llama 8B Q4_K - Medium         |   4.58 GiB |     8.03 B | CPU        |      12 |         pp512 |         45.85 ± 0.14 |
| llama 8B Q4_K - Medium         |   4.58 GiB |     8.03 B | CPU        |      12 |         tg128 |         16.70 ± 0.19 |
```

> ./llama-bench -t $(lscpu | grep '^Core(s)' | awk '{print $NF}') -m '/home/eliran/agentmake/models/gguf/deepseek-r1_14b.gguf'

```
| model                          |       size |     params | backend    | threads |          test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | ------: | ------------: | -------------------: |
| qwen2 14B Q4_K - Medium        |   8.37 GiB |    14.77 B | CPU        |      12 |         pp512 |         24.06 ± 0.19 |
| qwen2 14B Q4_K - Medium        |   8.37 GiB |    14.77 B | CPU        |      12 |         tg128 |          9.21 ± 0.03 |
```

> ./llama-bench -t $(lscpu | grep '^Core(s)' | awk '{print $NF}') -m '/home/eliran/agentmake/models/gguf/deepseek-r1_32b.gguf'

```
| model                          |       size |     params | backend    | threads |          test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | ------: | ------------: | -------------------: |
| qwen2 32B Q4_K - Medium        |  18.48 GiB |    32.76 B | CPU        |      12 |         pp512 |         10.31 ± 0.01 |
| qwen2 32B Q4_K - Medium        |  18.48 GiB |    32.76 B | CPU        |      12 |         tg128 |          4.16 ± 0.03 |
```

# Test Speed with llama.cpp - ROCm Backend

> ./build/bin/llama-bench -t $(lscpu | grep '^Core(s)' | awk '{print $NF}') -m '/home/eliran/agentmake/models/gguf/deepseek-r1_1.5b.gguf'

```
ggml_cuda_init: GGML_CUDA_FORCE_MMQ:    no
ggml_cuda_init: GGML_CUDA_FORCE_CUBLAS: no
ggml_cuda_init: found 1 ROCm devices:
  Device 0: AMD Radeon Graphics, gfx1151 (0x1151), VMM: no, Wave Size: 32
| model                          |       size |     params | backend    | ngl |          test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | ------------: | -------------------: |
| qwen2 1.5B Q4_K - Medium       |   1.04 GiB |     1.78 B | ROCm       |  99 |         pp512 |       486.73 ± 44.23 |
| qwen2 1.5B Q4_K - Medium       |   1.04 GiB |     1.78 B | ROCm       |  99 |         tg128 |         59.33 ± 2.00 |
```

./build/bin/llama-bench -t $(lscpu | grep '^Core(s)' | awk '{print $NF}') -m '/home/eliran/agentmake/models/gguf/deepseek-r1_7b.gguf'

```
ggml_cuda_init: GGML_CUDA_FORCE_MMQ:    no
ggml_cuda_init: GGML_CUDA_FORCE_CUBLAS: no
ggml_cuda_init: found 1 ROCm devices:
  Device 0: AMD Radeon Graphics, gfx1151 (0x1151), VMM: no, Wave Size: 32
| model                          |       size |     params | backend    | ngl |          test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | ------------: | -------------------: |
| qwen2 7B Q4_K - Medium         |   4.36 GiB |     7.62 B | ROCm       |  99 |         pp512 |         92.78 ± 0.36 |
| qwen2 7B Q4_K - Medium         |   4.36 GiB |     7.62 B | ROCm       |  99 |         tg128 |         17.49 ± 0.01 |
```

> ./build/bin/llama-bench -t $(lscpu | grep '^Core(s)' | awk '{print $NF}') -m '/home/eliran/agentmake/models/gguf/deepseek-r1_8b.gguf'

```
ggml_cuda_init: GGML_CUDA_FORCE_MMQ:    no
ggml_cuda_init: GGML_CUDA_FORCE_CUBLAS: no
ggml_cuda_init: found 1 ROCm devices:
  Device 0: AMD Radeon Graphics, gfx1151 (0x1151), VMM: no, Wave Size: 32
| model                          |       size |     params | backend    | ngl |          test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | ------------: | -------------------: |
| llama 8B Q4_K - Medium         |   4.58 GiB |     8.03 B | ROCm       |  99 |         pp512 |         88.05 ± 2.18 |
| llama 8B Q4_K - Medium         |   4.58 GiB |     8.03 B | ROCm       |  99 |         tg128 |         16.44 ± 0.26 |
```

> ./build/bin/llama-bench -t $(lscpu | grep '^Core(s)' | awk '{print $NF}') -m '/home/eliran/agentmake/models/gguf/deepseek-r1_14b.gguf'

```
ggml_cuda_init: GGML_CUDA_FORCE_MMQ:    no
ggml_cuda_init: GGML_CUDA_FORCE_CUBLAS: no
ggml_cuda_init: found 1 ROCm devices:
  Device 0: AMD Radeon Graphics, gfx1151 (0x1151), VMM: no, Wave Size: 32
| model                          |       size |     params | backend    | ngl |          test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | ------------: | -------------------: |
| qwen2 14B Q4_K - Medium        |   8.37 GiB |    14.77 B | ROCm       |  99 |         pp512 |         46.88 ± 2.31 |
| qwen2 14B Q4_K - Medium        |   8.37 GiB |    14.77 B | ROCm       |  99 |         tg128 |          8.96 ± 0.09 |
```

> ./build/bin/llama-bench -t $(lscpu | grep '^Core(s)' | awk '{print $NF}') -m '/home/eliran/agentmake/models/gguf/deepseek-r1_32b.gguf'

```
ggml_cuda_init: GGML_CUDA_FORCE_MMQ:    no
ggml_cuda_init: GGML_CUDA_FORCE_CUBLAS: no
ggml_cuda_init: found 1 ROCm devices:
  Device 0: AMD Radeon Graphics, gfx1151 (0x1151), VMM: no, Wave Size: 32
| model                          |       size |     params | backend    | ngl |          test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | ------------: | -------------------: |
| qwen2 32B Q4_K - Medium        |  18.48 GiB |    32.76 B | ROCm       |  99 |         pp512 |         20.17 ± 0.67 |
| qwen2 32B Q4_K - Medium        |  18.48 GiB |    32.76 B | ROCm       |  99 |         tg128 |          4.09 ± 0.07 |
```

# Llama.cpp vs Ollama

With the same memory settings, llama.cpp is significantly faster than ollama in all tests, with the same model weights.

# Links

https://gpdstore.net/kb/gpd-pocket-4-support-hub/kb-article/getting-started-with-the-gpd-pocket-4/?srsltid=AfmBOoq-RCtsGnkt

https://gpd.hk/gpdpocket4firmware

https://github.com/ftfpteams/focaltech-linux-fingerprint-driver

https://gpdstore.net/kb/faq/kb-article/how-to-install-ubuntu-linux-24-10-on-the-gpd-pocket-3/
