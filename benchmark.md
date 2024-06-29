# Benchmark

> ./llama-bench -t $(lscpu | grep '^Core(s)' | awk '{print $NF}') -ngl 999 -p 512 -n 512 -r 3 -m ../../gguf/phi3.gguf 

```
ggml_cuda_init: GGML_CUDA_FORCE_MMQ:    no
ggml_cuda_init: GGML_CUDA_FORCE_CUBLAS: no
ggml_cuda_init: found 2 ROCm devices:
  Device 0: Radeon RX 7900 XTX, compute capability 11.0, VMM: no
  Device 1: Radeon RX 7900 XTX, compute capability 11.0, VMM: no
| model                          |       size |     params | backend    | ngl |          test |              t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | ------------: | ---------------: |
| llama 7B Q4_K - Medium         |   2.16 GiB |     3.82 B | ROCm       | 999 |         pp512 |  5404.80 ± 10.80 |
| llama 7B Q4_K - Medium         |   2.16 GiB |     3.82 B | ROCm       | 999 |         tg512 |    128.50 ± 0.13 |

build: 72272b83 (3265)
```

> ./llama-bench -t $(lscpu | grep '^Core(s)' | awk '{print $NF}') -ngl 999 -p 512 -n 512 -r 3 -m ../../gguf/llama3.gguf 

```
ggml_cuda_init: GGML_CUDA_FORCE_MMQ:    no
ggml_cuda_init: GGML_CUDA_FORCE_CUBLAS: no
ggml_cuda_init: found 2 ROCm devices:
  Device 0: Radeon RX 7900 XTX, compute capability 11.0, VMM: no
  Device 1: Radeon RX 7900 XTX, compute capability 11.0, VMM: no
| model                          |       size |     params | backend    | ngl |          test |              t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | ------------: | ---------------: |
| llama 8B Q4_0                  |   4.33 GiB |     8.03 B | ROCm       | 999 |         pp512 |   3210.36 ± 7.95 |
| llama 8B Q4_0                  |   4.33 GiB |     8.03 B | ROCm       | 999 |         tg512 |    104.51 ± 0.10 |

build: 72272b83 (3265)
```

> ./llama-bench -t $(lscpu | grep '^Core(s)' | awk '{print $NF}') -ngl 999 -p 512 -n 512 -r 3 -m ../../gguf/llama3_70b.gguf 

```
ggml_cuda_init: GGML_CUDA_FORCE_MMQ:    no
ggml_cuda_init: GGML_CUDA_FORCE_CUBLAS: no
ggml_cuda_init: found 2 ROCm devices:
  Device 0: Radeon RX 7900 XTX, compute capability 11.0, VMM: no
  Device 1: Radeon RX 7900 XTX, compute capability 11.0, VMM: no
| model                          |       size |     params | backend    | ngl |          test |              t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | ------------: | ---------------: |
| llama 70B Q4_0                 |  37.22 GiB |    70.55 B | ROCm       | 999 |         pp512 |    362.94 ± 0.26 |
| llama 70B Q4_0                 |  37.22 GiB |    70.55 B | ROCm       | 999 |         tg512 |     17.40 ± 0.01 |

build: 72272b83 (3265)
```

> ./llama-bench -t $(lscpu | grep '^Core(s)' | awk '{print $NF}') -ngl 999 -p 512 -n 512 -r 3 -m ../../gguf/mistral.gguf 

```
ggml_cuda_init: GGML_CUDA_FORCE_MMQ:    no
ggml_cuda_init: GGML_CUDA_FORCE_CUBLAS: no
ggml_cuda_init: found 2 ROCm devices:
  Device 0: Radeon RX 7900 XTX, compute capability 11.0, VMM: no
  Device 1: Radeon RX 7900 XTX, compute capability 11.0, VMM: no
| model                          |       size |     params | backend    | ngl |          test |              t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | ------------: | ---------------: |
| llama 7B Q4_0                  |   3.83 GiB |     7.24 B | ROCm       | 999 |         pp512 |   3228.35 ± 9.58 |
| llama 7B Q4_0                  |   3.83 GiB |     7.24 B | ROCm       | 999 |         tg512 |    108.79 ± 0.09 |

build: 72272b83 (3265)
```

> ./llama-bench -t $(lscpu | grep '^Core(s)' | awk '{print $NF}') -ngl 999 -p 512 -n 512 -r 3 -m ../../gguf/wizardlm2_7b.gguf 

```
ggml_cuda_init: GGML_CUDA_FORCE_MMQ:    no
ggml_cuda_init: GGML_CUDA_FORCE_CUBLAS: no
ggml_cuda_init: found 2 ROCm devices:
  Device 0: Radeon RX 7900 XTX, compute capability 11.0, VMM: no
  Device 1: Radeon RX 7900 XTX, compute capability 11.0, VMM: no
| model                          |       size |     params | backend    | ngl |          test |              t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | ------------: | ---------------: |
| llama 7B Q4_0                  |   3.83 GiB |     7.24 B | ROCm       | 999 |         pp512 |   3239.21 ± 3.99 |
| llama 7B Q4_0                  |   3.83 GiB |     7.24 B | ROCm       | 999 |         tg512 |    108.99 ± 0.18 |

build: 72272b83 (3265)
```

> ./llama-bench -t $(lscpu | grep '^Core(s)' | awk '{print $NF}') -ngl 999 -p 512 -n 512 -r 3 -m ../../gguf/mixtral_8x7b.gguf 

```
ggml_cuda_init: GGML_CUDA_FORCE_MMQ:    no
ggml_cuda_init: GGML_CUDA_FORCE_CUBLAS: no
ggml_cuda_init: found 2 ROCm devices:
  Device 0: Radeon RX 7900 XTX, compute capability 11.0, VMM: no
  Device 1: Radeon RX 7900 XTX, compute capability 11.0, VMM: no
| model                          |       size |     params | backend    | ngl |          test |              t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | ------------: | ---------------: |
| llama 8x7B Q4_0                |  48.25 GiB |    91.80 B | ROCm       | 999 |         pp512 |   1200.80 ± 8.04 |
| llama 8x7B Q4_0                |  48.25 GiB |    91.80 B | ROCm       | 999 |         tg512 |     53.24 ± 0.07 |

build: 72272b83 (3265)
```

> ./llama-bench -t $(lscpu | grep '^Core(s)' | awk '{print $NF}') -ngl 999 -p 512 -n 512 -r 3 -m ../../gguf/starling-lm.gguf

```
ggml_cuda_init: GGML_CUDA_FORCE_MMQ:    no
ggml_cuda_init: GGML_CUDA_FORCE_CUBLAS: no
ggml_cuda_init: found 2 ROCm devices:
  Device 0: Radeon RX 7900 XTX, compute capability 11.0, VMM: no
  Device 1: Radeon RX 7900 XTX, compute capability 11.0, VMM: no
| model                          |       size |     params | backend    | ngl |          test |              t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | ------------: | ---------------: |
| llama 7B Q4_0                  |   3.83 GiB |     7.24 B | ROCm       | 999 |         pp512 |   3212.78 ± 5.43 |
| llama 7B Q4_0                  |   3.83 GiB |     7.24 B | ROCm       | 999 |         tg512 |    108.90 ± 0.08 |

build: 72272b83 (3265)
```

> ./llama-bench -t $(lscpu | grep '^Core(s)' | awk '{print $NF}') -ngl 999 -p 512 -n 512 -r 3 -m ../../gguf/orca2_7b.gguf 

```
ggml_cuda_init: GGML_CUDA_FORCE_MMQ:    no
ggml_cuda_init: GGML_CUDA_FORCE_CUBLAS: no
ggml_cuda_init: found 2 ROCm devices:
  Device 0: Radeon RX 7900 XTX, compute capability 11.0, VMM: no
  Device 1: Radeon RX 7900 XTX, compute capability 11.0, VMM: no
| model                          |       size |     params | backend    | ngl |          test |              t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | ------------: | ---------------: |
| llama 7B Q4_0                  |   3.56 GiB |     6.74 B | ROCm       | 999 |         pp512 |  3406.46 ± 21.05 |
| llama 7B Q4_0                  |   3.56 GiB |     6.74 B | ROCm       | 999 |         tg512 |    112.48 ± 0.09 |

build: 72272b83 (3265)
```

> ./llama-bench -t $(lscpu | grep '^Core(s)' | awk '{print $NF}') -ngl 999 -p 512 -n 512 -r 3 -m ../../gguf/orca2_13b.gguf 

```
ggml_cuda_init: GGML_CUDA_FORCE_MMQ:    no
ggml_cuda_init: GGML_CUDA_FORCE_CUBLAS: no
ggml_cuda_init: found 2 ROCm devices:
  Device 0: Radeon RX 7900 XTX, compute capability 11.0, VMM: no
  Device 1: Radeon RX 7900 XTX, compute capability 11.0, VMM: no
| model                          |       size |     params | backend    | ngl |          test |              t/s |
| ------------------------------ | ---------: | ---------: | ---------- | --: | ------------: | ---------------: |
| llama 13B Q4_0                 |   6.86 GiB |    13.02 B | ROCm       | 999 |         pp512 |   1819.86 ± 7.45 |
| llama 13B Q4_0                 |   6.86 GiB |    13.02 B | ROCm       | 999 |         tg512 |     71.13 ± 0.03 |

build: 72272b83 (3265)
```
