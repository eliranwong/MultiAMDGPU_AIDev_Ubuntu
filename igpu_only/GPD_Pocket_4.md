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