# Tested on a iGPU-only device

Tested device: Beelink GTR6

Tested iGPU: AMD 680M

Followed https://github.com/eliranwong/MultiAMDGPU_AIDev_Ubuntu/blob/main/README.md for for installation ROCm, pytorch, MIGraphX, etc.

Environment variables:

```
export ROCM_HOME=/opt/rocm
export LD_LIBRARY_PATH=/opt/rocm/include:/opt/rocm/lib:$LD_LIBRARY_PATH
export PATH=$HOME/.local/bin:/opt/rocm/bin:/opt/rocm/llvm/bin:$PATH
export HSA_OVERRIDE_GFX_VERSION=10.3.0
```

# Compile llama.cpp from source:

Compiled with ROCM:

> HIPCXX="$(hipconfig -l)/clang" HIP_PATH="$(hipconfig -p)" HIP_DEVICE_LIB_PATH=/opt/rocm-6.0.2/lib/llvm/lib/clang/17.0.0/lib/amdgcn/bitcode/ cmake -S . -B build -DLLAMA_HIPBLAS=ON -DAMDGPU_TARGETS=gfx1030 -DCMAKE_BUILD_TYPE=Release && cmake --build build -- -j 8

or 

> make -j8 LLAMA_HIPBLAS=1 LLAMA_HIP_UMA=1 AMDGPU_TARGETS=gfx1030

Remarks: Do NOT use LLAMA_HIP_UMA with discrete GPUs.
