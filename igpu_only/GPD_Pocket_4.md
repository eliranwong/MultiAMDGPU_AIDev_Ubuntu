# Tested on a iGPU-only device

Tested device: AMD Ryzen™ AI 9 HX 370; AMD Radeon™ 890M

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

```
