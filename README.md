# Multi AMD GPU Setup for AI Development on Ubuntu

Welcome to this repository, where I share my notes and insights on 
setting up multiple AMD GPUs on Ubuntu for AI development. This initiative stems
from the noticeable gap in resources and discussions around AMD GPU setups for 
AI, as most online documentation and forums predominantly focus on Nvidia GPUs. 
This repository aims to bridge that gap, providing an overview and step-by-step guide based on my personal experience and research.

![result2](https://github.com/eliranwong/MultiAMDGPU_AIDev_Ubuntu/assets/25262722/29e067dc-2bbd-4140-b0e3-39206a72c4b1)

### What You Will Find Here

This repository contains notes, guides, and scripts that I've compiled during my
setup process. While my setup specifically involves Ubuntu 22.04, kernel 6.5, 
with 2 AMD RX 7900 XTX GPUs, the information provided here should be applicable 
or easily adaptable to similar configurations. Here's what to expect:

- **Installation Guides:** Detailed steps on installing and configuring the 
necessary drivers and tools for AMD GPUs on Ubuntu.
- **Troubleshooting Tips:** Solutions to common issues that might arise during 
the setup process.
- **Performance Optimization:** Tips on optimizing your AMD GPU setup for better
performance in AI development tasks.
- **Useful Resources:** A curated list of resources that I found invaluable 
during my setup process.

### Disclaimer

The notes and guides in this repository are based on my personal experience and 
research. While I strive to provide accurate and up-to-date information, I 
cannot guarantee that everything will work perfectly for every setup. Always 
back up your data and proceed with caution.

# Select Ubuntu and Kernel Versions

Read supporting versions at https://rocm.docs.amd.com/projects/install-on-linux/en/latest/reference/system-requirements.html#supported-distributions

![Ubuntu_support](https://github.com/eliranwong/MultiAMDGPU_AIDev_Ubuntu/assets/25262722/ad82d57f-90d7-4d22-bc82-a43cff68dcbb)

Check your current Ubuntu and kernal versions, read:

https://rocm.docs.amd.com/projects/install-on-linux/en/latest/how-to/prerequisites.html

Select supported versions of Ubuntu and kernel, e.g.

> Ubuntu 22.04.4 + Kernel 6.5

## Install a Supported Version or Kernel

https://github.com/eliranwong/MultiAMDGPU_AIDev_Ubuntu/blob/main/Install_Ubuntu_Kernel.md

# Add User to Groups for GPU Access

> sudo usermod -a -G render,video $LOGNAME

<details><summary>Explanation</summary>

For AMD GPUs on Linux, the groups you might need to add users to for proper GPU access are similar to those for NVIDIA GPUs. Here are the key groups:

- video: This group grants access to video devices and may include GPU devices.
- render: As previously mentioned, this group allows access to GPU rendering devices.

When using AMD GPUs, especially with ROCm (Radeon Open Compute), you may also need to add users to these groups to ensure they have the necessary permissions to access the GPU for computing tasks. [The ROCm documentation](https://rocm.docs.amd.com/projects/radeon/en/latest/docs/install/install-radeon.html) specifically mentions adding users to both the render and video groups to set the correct permissions.

To add a user to these groups, you can use the following command:

> sudo usermod -a -G render,video username

Replace username with the actual username of the user you want to add to the groups. After adding the user to these groups, they should have the necessary permissions to access the GPU resources on your system. It's always a good practice to log out and log back in or reboot the system to ensure the changes take effect.

If you're using specific AMDGPU control applications or tools, they might have their own group requirements or recommendations, so it's a good idea to check the documentation for those tools as well1. Remember, managing user access to GPUs is an important aspect of system administration, especially in multi-user environments or when dealing with sensitive compute tasks.

</details>

# Install ROCM 6.0

Version 6.0.2 is preferred as it is officially supported by PyTorch latest stable version 2.3.0. Read more at supported versions at https://pytorch.org/get-started/locally/

![Screenshot from 2024-04-29 16-07-45](https://github.com/eliranwong/MultiAMDGPU_AIDev_Ubuntu/assets/25262722/148bf15e-437c-4b94-b292-73f56c009f3d)

## Uninstall Old Copies

> amdgpu-install --uninstall

> sudo apt remove --purge amdgpu-install

## Install

> wget https://repo.radeon.com/amdgpu-install/6.0.2/ubuntu/jammy/amdgpu-install_6.0.60002-1_all.deb

> sudo apt install ./amdgpu-install_6.0.60002-1_all.deb

> sudo amdgpu-install --usecase=graphics,opencl,hip,hiplibsdk,rocm,rocmdev,mllib,mlsdk --no-dkms

For more options of use cases, read https://rocm.docs.amd.com/projects/install-on-linux/en/latest/how-to/amdgpu-install.html#use-cases

## Modify Grub to Avoid Hangs

Reference: https://rocm.docs.amd.com/projects/install-on-linux/en/docs-6.1.0/how-to/native-install/install-faq.html#issue-5-application-hangs-on-multi-gpu-systems

Add "iommu=pt" to GRUB_CMDLINE_LINUX_DEFAULT in /etc/default/grub. 

For example, 

> sudo nano /etc/default/grub

Changed from:

```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
```

To:

```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash iommu=pt"
```

Update grub

> sudo update-grub

## Verify

Restart to make changes effective:

> sudo reboot

To install rocm-related packages:

> sudo apt install rocminfo rocm-libs rocm-smi-lib

To verify, run:

> rocminfo

Read https://rocm.docs.amd.com/en/docs-6.0.2/deploy/linux/os-native/install.html#post-install-actions-and-verification-process

# Disable Integrated GPU

To avoid [a known bug](https://github.com/vosen/ZLUDA#hardware) in underlying ROCm/HIP runtime, disable the integrated GPU.

The file `/etc/default/grub` is a configuration file for the GRUB bootloader on Ubuntu. The line `GRUB_CMDLINE_LINUX_DEFAULT` in this file is used to pass arguments to the Linux kernel at boot time. To disable an integrated GPU system-wide, you can do so by adding a specific argument to this line. The argument you need is `pci-stub.ids=\<DEVICE_VENDOR\>:\<DEVICE_CODE\>`, where `\<DEVICE_VENDOR\>:\<DEVICE_CODE\>` is the vendor and device code of your GPU.

Check the vendor and device code with 'lspci', e.g.:

> lspci -k | grep -A 2 -i "VGA"

For example, the following output tells that the vendor and device code is '1f66:0001':

```
05:00.0 VGA compatible controller: Advanced Micro Devices, Inc. [AMD/ATI] Rembrandt (rev c7)
	Subsystem: Device 1f66:0001
	Kernel driver in use: amdgpu
```

Edit GRUB bootloader configuration file:

> sudo nano /etc/default/grub

```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash pci-stub.ids=1f66:0001"
```

Update GRUB for the changes to take effect

> sudo update-grub

<details><summary>Explanation</summary>

The GRUB_CMDLINE_LINUX_DEFAULT line in the GRUB configuration file is used to set the default kernel boot parameters. These parameters are passed to the Linux kernel at boot time and can be used to control various aspects of the system’s behavior.

The `pci-stub.ids=\<DEVICE_VENDOR\>:\<DEVICE_CODE\>` argument doesn't exactly "disable" the integrated graphics card, but rather it prevents the Linux kernel from loading a specific driver for that device during the boot process.

Here's how it works:

- Each hardware device in your computer, including your integrated graphics card, has a unique vendor and device ID. This ID is used by the operating system to identify the device and load the appropriate driver for it.

- When you pass `pci-stub.ids=\<DEVICE_VENDOR\>:\<DEVICE_CODE\>` to the kernel at boot time, you're telling the kernel to reserve the specified device (in this case, your integrated graphics card) for the `pci-stub` driver.

- The `pci-stub` driver is a "dummy" driver that doesn't do anything—it simply claims the device and prevents other drivers from being able to use it. This is useful in situations where you want to prevent the operating system from interacting with a device, such as when you're setting up a device for pass-through to a virtual machine.

So, while the integrated graphics card is still physically present and powered on, the operating system is unable to interact with it because the `pci-stub` driver has claimed it. This effectively "disables" the integrated graphics card from the operating system's perspective.

Remember, this is a low-level operation that can have significant effects on your system, so it should be done with caution. Always make sure to consult the relevant documentation or seek expert advice if you're unsure. And don't forget to run `sudo update-grub` and reboot your system after making changes to the GRUB configuration file.

</details>

# UPDATE GRUB

**Configure Vulkan to use AMD graphics card**:

    - Edit the file `/etc/default/grub` and in the line that reads `GRUB_CMDLINE_LINUX_DEFAULT`, add: `radeon.cik_support=0 amdgpu.cik_support=1 radeon.si_support=0 amdgpu.si_support=1`.
    - Create a new file `/etc/modprobe.d/amdgpu.conf` and add the following lines to it:
        ```
        options amdgpu si_support=1
        options amdgpu cik_support=1
        ```
    - Create a new file `/etc/modprobe.d/radeon.conf` and add the following lines to it:
        ```
        options radeon si_support=0
        options radeon cik_support=0
        ```
    - Create a new file `/etc/modprobe.d/blacklist.conf` and add the following lines to it:
        ```
	blacklist radeon
	```
    - Run `sudo update-grub` and then reboot your system.

# Hardware Detection

With GUI:

1. Launch Settings application
2. Select About > Graphics

With CLI:

> rocminfo

From rocminfo output, make a note of the node values of the installed GPUs.

Remarks: Be careful that some non-GPU devices may be assigned with node numbers.  In that case, ignore the node numbers.  Use index numbers, starting from 0. The largest number should be the number of available GPUs minus 1.

# Environment Variables

Modify the values to suit your cases.

The following examples assume:

* ROCm version 6.0.2 installed

* No integrated GPU

* Two AMD RX 7900 XTX installed. (Their node values in 'rocminfo' output are '1' and '2', as the AMD Ryzen Threadripper CPU on my device is identified as "Agent 1" and is assigned 0 as its node value.  Therefore, I use indexes '0', '1' instead for GPUs setting below.)

## Overview

I use my case as an example:

```
export ROCM_HOME=/opt/rocm-6.0.2
export LD_LIBRARY_PATH=/opt/rocm-6.0.2/lib:$LD_LIBRARY_PATH
export PATH=/home/eliran/.local/bin:/opt/rocm-6.0.2/bin:/opt/rocm-6.0.2/llvm/bin:$PATH
export HSA_OVERRIDE_GFX_VERSION=11.0.0
export ROCR_VISIBLE_DEVICES=GPU-bad1680dbd90c4ff,GPU-35086d5e71d6c9ab
export GPU_DEVICE_ORDINAL=0,1
export HIP_VISIBLE_DEVICES=0,1
export CUDA_VISIBLE_DEVICES=0,1
export LLAMA_HIPLAS=0,1
export GGML_VULKAN_DEVICE=0,1
export GGML_VK_VISIBLE_DEVICES=0,1
export DRI_PRIME=0
export OMP_DEFAULT_DEVICE=1
```

ROCM_HOME - tells AI libraries where ROCM is stored; typically somewhere in /opt, e.g.:

> export ROCM_HOME=/opt/rocm-6.0.2

<details><summary>Explanation</summary>

`ROCM_HOME` is an environment variable in Linux that is used to specify the location of the ROCm (Radeon Open Compute) software on your system. ROCm is a set of open-source libraries and tools that are used to create high performance, machine learning applications on AMD GPUs.

When you install ROCm, it is typically installed in a directory under `/opt`. For example, if you installed version 6.0.2 of ROCm, it might be installed in `/opt/rocm-6.0.2`.

The `export` command in Linux is used to set environment variables. So, when you run the command `export ROCM_HOME=/opt/rocm-6.0.2`, you are telling the system "Whenever I refer to `ROCM_HOME`, I actually mean `/opt/rocm-6.0.2`".

This is useful because many AI libraries that use ROCm will look for the `ROCM_HOME` environment variable to know where to find the ROCm software. By setting `ROCM_HOME`, you ensure that these libraries can find and use ROCm correctly.

</details>

LD_LIBRARY_PATH - loader library path; typically this is set to $ROCM_HOME/lib. An indication you’re missing this flag is if you import pytorch and see an error like undefined reference to...

> export LD_LIBRARY_PATH=/opt/rocm-6.0.2/lib:$LD_LIBRARY_PATH

> export PATH=/opt/rocm-6.0.2/bin:/opt/rocm-6.0.2/opencl/bin:$PATH

<details><summary>Explanation</summary>

`LD_LIBRARY_PATH` is an environment variable that specifies a list of directories where the dynamic linker should look for dynamically linked libraries. When a program is launched, the dynamic linker checks the `LD_LIBRARY_PATH` to find the libraries that the program needs to run.

In this example, `LD_LIBRARY_PATH` is being set to include the `/opt/rocm-6.0.2/lib` directory. This is likely where the ROCm (Radeon Open Compute) libraries are installed. If you're trying to use PyTorch and it's built with ROCm support, it will need to know where these libraries are. If `LD_LIBRARY_PATH` doesn't include the ROCm library directory, you might see errors like "undefined reference to..." when you try to import PyTorch.

The command `export LD_LIBRARY_PATH=/opt/rocm-6.0.2/lib:$LD_LIBRARY_PATH` is adding `/opt/rocm-6.0.2/lib` to your existing `LD_LIBRARY_PATH`.

The `PATH` environment variable is similar, but it's used to tell the shell where to look for executable files. The command `export PATH=/opt/rocm-6.0.2/bin:/opt/rocm-6.0.2/opencl/bin:$PATH` is adding the `/opt/rocm-6.0.2/bin` and `/opt/rocm-6.0.2/opencl/bin` directories to your `PATH`. This means that when you type a command, the shell will also look in these directories to find it.

</details>

HSA_OVERRIDE_GFX_VERSION - workaround for software that doesn’t yet fully support the installed gpu. 

> rocminfo | grep gfx

For example, gfx1100 used by AMD RX 7900 XTX:

> export HSA_OVERRIDE_GFX_VERSION=11.0.0

<details><summary>Explanation</summary>

HSA_OVERRIDE_GFX_VERSION: This is an environment variable used as a workaround for software that doesn’t yet fully support the installed GPU. It's used to override the Graphics/Compute version. For example, if you have a GPU that is not yet fully supported by the ROCm software (like the AMD Radeon RX 7900 XTX which uses gfx1100), you can set this environment variable to tell the ROCm software to treat your GPU as if it were a different, fully supported version.

rocminfo | grep gfx: This is a command-line instruction. `rocminfo` is a tool that provides information about the HSA (Heterogeneous System Architecture) system attributes and agents. The `grep gfx` part of the command filters the output of `rocminfo` to only show lines that contain 'gfx', which are the lines that tell you the version of the AMD GCN ISA or architecture names.

export HSA_OVERRIDE_GFX_VERSION=11.0.0: This is a command that sets the `HSA_OVERRIDE_GFX_VERSION` environment variable to '11.0.0'. This tells the ROCm software to treat your GPU as if it were a gfx1100, even if it's actually a different version. This can be useful if your GPU is not yet fully supported by the ROCm software.

In summary, these commands and variables are used to help ensure compatibility between your GPU and the ROCm software, even if your GPU is not yet fully supported. They allow you to override the reported version of your GPU so that the ROCm software treats it as a fully supported version. This can be particularly useful when working with newer GPUs like the AMD Radeon RX 7900 XTX. Please note that while this can enable you to use the ROCm software with unsupported GPUs, it may not provide optimal performance or full functionality. It's always best to check the official ROCm documentation or the GPU manufacturer's documentation for the most accurate and up-to-date information.

</details>

ROCR_VISIBLE_DEVICES - device indices or UUIDs that will be exposed to applications, e.g.:

> export ROCR_VISIBLE_DEVICES=0,1

Remarks: Tough documents state that device indices are accepted, but device indeces does not work in my case.  I have to use UUIDs, in order for my system to detect the GPUs correctly. UUIDs of individual GPUs can be found with command 'rocminfo'.

<details><summary>Explanation</summary>

`ROCR_VISIBLE_DEVICES` is an environment variable used in the ROCm (Radeon Open Compute) software stack. It specifies which GPU devices will be exposed to applications.

Device Indices or UUIDs - These are identifiers for the GPUs in your system. A device index is a numerical value assigned to each GPU, starting from 0. A UUID (Universally Unique Identifier) is a unique string that can also be used to identify a GPU.

You set this environment variable to a list of device indices or UUIDs that you want to expose to applications. For example, `export ROCR_VISIBLE_DEVICES=0,1` means that only the first and second GPUs (indices start from 0) will be visible to applications.

Applications running on the ROCm platform will only be able to see and use the GPUs specified in `ROCR_VISIBLE_DEVICES`. Other GPUs in the system will be hidden from these applications.

This feature is useful for isolating GPU resources, especially in multi-GPU systems. For instance, you might want certain applications to only use specific GPUs, while other GPUs are reserved for different tasks.

</details>

GPU_DEVICE_ORDINAL - devices indices exposed to OpenCL and HIP applications, e.g.:

> export GPU_DEVICE_ORDINAL=0,1

<details><summary>Explanation</summary>

`GPU_DEVICE_ORDINAL` is an environment variable used in both OpenCL and HIP (Heterogeneous-Compute Interface for Portability) applications. It's used to control the visibility of devices to these applications, similar to `HIP_VISIBLE_DEVICES`.

This specific environment variable, `GPU_DEVICE_ORDINAL`, is used to control which devices are available for OpenCL and HIP programs to use when they are run.

Device Indices are the numerical identifiers assigned to each device. In a system with multiple GPUs, each GPU is assigned a unique device index.

The value `0,1` means that the first and second devices (as device indices start from 0) are visible to OpenCL and HIP applications. So, if you have two GPUs in your system, both will be available for use by these programs.

</details>

HIP_VISIBLE_DEVICES - Device indices exposed to HIP applications, e.g.:

> export HIP_VISIBLE_DEVICES=0,1

<details><summary>Explanation</summary>

`HIP_VISIBLE_DEVICES` is an environment variable used in HIP (Heterogeneous-Compute Interface for Portability) applications. HIP is a part of AMD's ROCm (Radeon Open Compute) platform designed to ease the task of porting CUDA applications to AMD's hardware.

This specific environment variable, `HIP_VISIBLE_DEVICES`, is used to control the visibility of devices to HIP applications. It determines which devices are available for HIP programs to use when they are run.

Device Indices are the numerical identifiers assigned to each device. In a system with multiple GPUs, each GPU is assigned a unique device index.

The value `0,1` means that the first and second devices (as device indices start from 0) are visible to HIP applications. So, if you have two GPUs in your system, both will be available for use by HIP programs.

</details>

CUDA_VISIBLE_DEVICES - provided for CUDA compatibility, e.g.:

> export CUDA_VISIBLE_DEVICES=0,1

<details><summary>Explanation</summary>

`CUDA_VISIBLE_DEVICES` is an environment variable in Linux that CUDA applications use to control which GPUs they can use. This variable is provided for CUDA compatibility.

When you run a CUDA application, it can see all the GPUs in your system by default. However, there might be situations where you want to limit an application to only use certain GPUs. This is where `CUDA_VISIBLE_DEVICES` comes into play.

The `export CUDA_VISIBLE_DEVICES=0,1` command is an example of how you can use this environment variable. Here's what it does:

- `export`: This is a command in Linux that sets environment variables.
- `CUDA_VISIBLE_DEVICES`: This is the environment variable we're setting. It controls which GPUs a CUDA application can use.
- `0,1`: This is the value we're setting for `CUDA_VISIBLE_DEVICES`. The numbers correspond to the IDs of the GPUs. `0,1` means the application can use GPU 0 and GPU 1.

So, `export CUDA_VISIBLE_DEVICES=0,1` means "set the `CUDA_VISIBLE_DEVICES` environment variable to `0,1`". After running this command, any CUDA application you run will only be able to see and use GPU 0 and GPU 1, even if there are more GPUs in the system.

This can be particularly useful in multi-GPU systems, where you might want to reserve certain GPUs for specific tasks or users. By setting `CUDA_VISIBLE_DEVICES`, you can control the resources that different applications or users can access. 

Please note that the GPU IDs are not necessarily fixed, they can change based on the system configuration, GPU topology, or after a system reboot. So, it's always a good idea to verify the GPU IDs before setting `CUDA_VISIBLE_DEVICES`. You can do this using the `nvidia-smi` command, which provides information about the GPUs in your system, including their IDs. 

</details>

LLAMA_HIPLAS - applicable to Llama.cpp setup

> export LLAMA_HIPLAS=0,1

<details><summary>Explanation</summary>

The LLAMA_HIPLAS=0,1 setting is likely related to the configuration of the LLaMA (Large Language Model) library in the llama.cpp project. However, the exact meaning of this setting is not directly available in the search results.

</details>

DRI_PRIME - optional in case with no integrated GPU

> export DRI_PRIME=0

<details><summary>Explanation</summary>

`DRI_PRIME` is an environment variable used in Linux systems to manage hybrid graphics. Hybrid graphics are found on recent desktops and laptops, where there are two graphics cards: an integrated one (usually Intel) and a discrete one (like NVIDIA or AMD Radeon). The integrated card is used for regular tasks to save power, while the discrete card is used for GPU-intensive applications like gaming or 3D rendering.

When you run a command with `DRI_PRIME=1`, it tells the system to use the discrete GPU for that particular application. For example, if you want to run Firefox using the discrete GPU, you would use the command `DRI_PRIME=1 firefox`.

On the other hand, `export DRI_PRIME=0` sets the `DRI_PRIME` environment variable to `0` for the entire session or script where the command is run. This means that the integrated GPU (which is usually less powerful but more energy-efficient) will be used for all applications run in that session or script.

Please note that the actual GPU used can depend on your specific system configuration. You can check which GPU an application is using with the command `glxinfo | grep "OpenGL renderer"`.

</details>

OMP_DEFAULT_DEVICE - default device used for OpenMP target offloading, e.g.:

> export OMP_DEFAULT_DEVICE=1

<details><summary>Explanation</summary>

`OMP_DEFAULT_DEVICE` is an environment variable used in the context of OpenMP, a parallel programming model. This variable is used to specify the default device for OpenMP target offloading.

In OpenMP, "offloading" refers to the process of transferring computation from the host (usually a CPU) to a device (usually a GPU or another accelerator). This is particularly useful in high-performance computing scenarios where you want to leverage the power of GPUs for certain parts of your computation.

The value of `OMP_DEFAULT_DEVICE` is an integer that corresponds to the device ID. Device IDs usually start from 0 and increment for each additional device. So, if you have two devices and you want to use the second device as the default for offloading, you would set `OMP_DEFAULT_DEVICE="1"` (since we start counting from 0).

It's not uncommon to skip the first device for offloading. The first device (device 0) could be reserved for other tasks, such as rendering graphics in a desktop environment. Offloading compute-intensive tasks to other devices can help ensure that the system remains responsive. However, this can vary based on the specific system configuration and the requirements of the application. It's always a good idea to check the documentation for your specific hardware and software setup to understand the best practices for your situation.

</details>

GGML_VULKAN_DEVICE & GGML_VK_VISIBLE_DEVICES - use Llama.cpp via Vulkan backend

> export GGML_VULKAN_DEVICE=0,1

> export GGML_VK_VISIBLE_DEVICES=0,1

<details><summary>Explanation</summary>

https://github.com/ggerganov/llama.cpp/issues/6166

</details>

Read more at:

- https://rocmdocs.amd.com/en/latest/conceptual/gpu-isolation.html#environment-variables

- https://medium.com/@damngoodtech/amd-rocm-pytorch-and-ai-on-ubuntu-the-rules-of-the-jungle-24a7ab280b17

# Python libraries

To make it clean, uninstall old copies, if any

> pip uninstall torch torchaudio torchvision cupy spacy -y

# pytorch

https://pytorch.org/get-started/locally/

Install packages that support rocm, e.g.

> pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/rocm6.0 --no-cache-dir

To verify:

> python3

> import torch

> torch.\_\_version\_\_

> torch.cuda.is_available()

> torch.cuda.device_count()

> torch.cuda.get_device_properties(0).total_memory

> torch.cuda.get_device_properties(1).total_memory

> torch.cuda.current_device()

> torch.cuda.get_device_name(torch.cuda.current_device())

Read more at https://pytorch.org/get-started/locally/#linux-verification

# ONNX with ROCm

With docker:

> git clone https://github.com/microsoft/onnxruntime.git

> cd onnxruntime/dockerfiles

> docker build -t onnxruntime-rocm -f Dockerfile.rocm .

> docker run -it --device=/dev/kfd --device=/dev/dri --group-add video onnxruntime-rocm

Read: https://github.com/microsoft/onnxruntime/tree/main/dockerfiles#rocm

To build from source, read: https://onnxruntime.ai/docs/build/eps.html#amd-rocm

More at: https://onnxruntime.ai/docs/

# MIGraphX

Install ROCm before installing MIGraphX.  To install MIGraphX

> sudo apt update && sudo apt install -y migraphx

Header files and libraries are installed under /opt/rocm-<version>, where <version> is the ROCm version.

Read: https://github.com/ROCm/AMDMIGraphX#amd-migraphx

# Onnx ExecutionProvider

With ROCm:

Read: https://onnxruntime.ai/docs/execution-providers/ROCm-ExecutionProvider.html

```
providers = [
    'ROCMExecutionProvider',
    'CPUExecutionProvider',
]
```

Option: user_compute_stream

```
providers = [("ROCMExecutionProvider", {"device_id": torch.cuda.current_device(), "user_compute_stream": str(torch.cuda.current_stream().cuda_stream)})]
```

With MiGraphX:

Read: https://onnxruntime.ai/docs/execution-providers/MIGraphX-ExecutionProvider.html

```
providers = [
    'MIGraphXExecutionProvider',
    'CPUExecutionProvider',
]
```

# DeepSpeed

https://cloudblogs.microsoft.com/opensource/2022/03/21/supporting-efficient-large-model-training-on-amd-instinct-gpus-with-deepspeed/

# tensorflow

AMD ROCm is upstreamed into the TensorFlow github repository. Pre-built wheels are hosted on pipy.org

The latest version can be installed with this command:

> pip install tensorflow-rocm

# cupy

> pip install cupy

Note: Installation from source may offer better support

# spacy

With pytorch & cupy installed first, run:

> pip install spacy

# ollama

Ollama detects AMD GPUs automatically on installation:

![amdgpu_ollama](https://github.com/eliranwong/freegenius/assets/25262722/c985062b-da23-4879-8d55-76b16e1017f3)

# llama.cpp

The author managed to installed llama.cpp with 

> CMAKE_ARGS="-DLLAMA_CLBLAST=on" pip install llama-cpp-python

Alternately,

Use hipBLAS (ROCm) as backend:

> sudo apt install libc6-dev libstdc++-12-dev

> CMAKE_ARGS="-DLLAMA_HIPBLAS=on" pip install llama-cpp-python

Use Vulkan as backend:

> CMAKE_ARGS="-DLLAMA_VULKAN=on" pip install llama-cpp-python

Read more at: https://llama-cpp-python.readthedocs.io/en/stable/

# freegenius

1. Create and activate a virtual environment, e.g.

> mkdir ~/apps

> cd ~/apps

> python3 -m venv freegenius

> source ~/apps/freegenius/bin/activate

2. Install pytorch

Check required versions at https://github.com/eliranwong/freegenius/blob/main/package/freegenius/requirements.txt

> pip install torch==2.3.0 torchvision==0.18.0 torchaudio==2.3.0 --index-url https://download.pytorch.org/whl/rocm6.0 --no-cache-dir

3. Install llama.cpp

Check required versions at https://github.com/eliranwong/freegenius/blob/main/package/freegenius/requirements.txt

> CMAKE_ARGS="-DLLAMA_CLBLAST=on" pip install llama-cpp-python[server]==0.2.69

4. Workaround [an issue regarding tensor_split feature](https://github.com/abetlen/llama-cpp-python/issues/1166)

![amdgpu_llamacpp](https://github.com/eliranwong/freegenius/assets/25262722/6d227573-eef9-49ea-9239-59cae140a8d2)

Edit the file "llama_cpp.py", in this case, located in '~/apps/freegenius/lib/python3.10/site-packages/llama_cpp'

Edit the file manually: '~/apps/freegenius/lib/python3.10/site-packages/llama_cpp/llama_cpp.py'

Change:

from

```
LLAMA_MAX_DEVICES = _lib.llama_max_devices()
```

to

```
#LLAMA_MAX_DEVICES = _lib.llama_max_devices()
LLAMA_MAX_DEVICES = 2
```

5. Install FreeGenius

> pip install freegenius

6. Edit FreeGenius config.py

In this case, manually edit '~/apps/freegenius/lib/python3.10/site-packages/freegenius/config.py'

```
llamacppMainModel_n_gpu_layers = -1
llamacppMainModel_additional_model_options = {'tensor_split': [0.5, 0.5]}
llamacppChatModel_n_gpu_layers = -1
llamacppChatModel_additional_model_options = {'tensor_split': [0.5, 0.5]}
llamacppVisionModel_n_gpu_layers = -1
llamacppVisionModel_additional_model_options = {'tensor_split': [0.5, 0.5]}
llamacppMainModel_additional_server_options = '--tensor_split=0.5,0.5'
llamacppVisionModel_additional_server_options = '--tensor_split=0.5,0.5'
ollamaMainModel_additional_options = {"num_gpu": 20000}
ollamaChatModel_additional_options = {"num_gpu": 20000}
```

7. Run FreeGenius

> freegenius

# Performance Optimization

For performance optimization, you may read:

https://huggingface.co/docs/optimum/main/en/amd/amdgpu/overview

https://github.com/nktice/AMD-AI/blob/main/performance-tuning.md

# Easy-diffusion

1. Download

> wget https://github.com/cmdr2/stable-diffusion-ui/releases/latest/download/Easy-Diffusion-Linux.zip

2. Unzip

> unzip Easy-Diffusion-Linux.zip

3. First attempt to install (expect an error):

> cd easy-diffusion

> ./start.sh

4. Fix this error when encountered:

```
  File ".../easy-diffusion/installer_files/env/lib/python3.8/site-packages/clip/clip.py", line 6, in <module>
    from pkg_resources import packaging
ImportError: cannot import name 'packaging' from 'pkg_resources' (.../easy-diffusion/installer_files/env/lib/python3.8/site-packages/pkg_resources/__init__.py)
```

Press any key to continue and edit line 6 of the file '.../easy-diffusion/installer_files/env/lib/python3.8/site-packages/clip/clip.py' manually and changed it to:

> import packaging

5. Run the setup again

> ./start.sh

6. Launch the UI

> open http://localhost:9000 

# Xorg issue

If you use Xorg instead of Wayland and have the issue where the mouse cursor is invisible, you can try to create the file /etc/X11/xorg.conf.d/99-modesetting.conf with the following content:

```
Section "Device"
      Identifier "modesetting"
      Driver "modesetting"
EndSection
```

# DMIGraphX

https://github.com/ROCm/AMDMIGraphX

> sudo apt update && sudo apt install -y migraphx

# Vulkan

Install vulkan to use vulkan backend for some applications, e.g. llama.cpp.

To install:

> wget -qO - https://packages.lunarg.com/lunarg-signing-key-pub.asc | sudo apt-key add -

> sudo wget -qO /etc/apt/sources.list.d/lunarg-vulkan-jammy.list https://packages.lunarg.com/vulkan/lunarg-vulkan-jammy.list

> sudo apt update -y

> sudo apt install -y vulkan-sdk

Alternately,

> sudo apt install vulkan-amdgpu vulkan-tools vulkan-validationlayers vulkan-validationlayers-dev

# To verify the installation, use the command below:

> apt list --installed | grep vulkan

> vulkaninfo

(check the path of $VULKAN_SDK)

e.g.

> locate explicit_layer.d # /usr/share/vulkan/explicit_layer.d

To set related Vulkan SDK environment variables:

> export VULKAN_SDK=/usr/share/vulkan

> export VK_LAYER_PATH=$VULKAN_SDK/explicit_layer.d

Reference: https://github.com/ggerganov/llama.cpp#vulkan

# CUDA-compatible Alternative

https://github.com/vosen/ZLUDA

Current known issues of ZLUDA: https://github.com/vosen/ZLUDA#known-issues

# References

https://rocm.docs.amd.com/en/docs-5.7.1/deploy/linux/os-native/install.html

https://huggingface.co/amd

https://huggingface.co/docs/optimum/main/en/amd/amdgpu/overview

https://medium.com/@damngoodtech/amd-rocm-pytorch-and-ai-on-ubuntu-the-rules-of-the-jungle-24a7ab280b17

https://askubuntu.com/questions/1451506/how-to-make-ubuntu-22-04-work-with-a-radeon-rx-7900-xtx

https://medium.vaningelgem.be/installing-pytorch-rocm-on-ubuntu-mantic-23-10-3da0f84c65d9

https://medium.com/@gotagando/how-to-install-rocm-5-7-1-for-7900-xtx-a84099917358

https://github.com/nktice/AMD-AI

https://medium.com/@topandroidapps.zooparty/install-and-run-llama-cpp-with-rocm-5-7-on-ubuntu-22-04-530987b8a835

https://medium.com/@yash9439/run-any-llm-on-distributed-multiple-gpus-locally-using-llama-cpp-2ff478a0dc3c

https://github.com/ggerganov/llama.cpp/issues/3051

https://github.com/ggerganov/llama.cpp/discussions/5138

https://github.com/ggerganov/llama.cpp/pull/1607

https://www.reddit.com/r/LocalLLaMA/comments/1anzmfe/multigpu_mixing_amdnvidia_with_llamacpp/

https://community.amd.com/t5/ai/amd-extends-support-for-pytorch-machine-learning-development-on/ba-p/637756

https://rocm.blogs.amd.com/artificial-intelligence/llama2-lora/README.html

https://rocm.docs.amd.com/projects/install-on-linux/en/develop/how-to/3rd-party/pytorch-install.html

https://rocm.docs.amd.com/projects/radeon/en/latest/docs/install/install-onnx.html

https://rocm.docs.amd.com/projects/radeon/en/latest/docs/install/install-migraphx.html

https://rocm.blogs.amd.com/artificial-intelligence/llm-inference-optimize/README.html

https://onnxruntime.ai/docs/execution-providers/ROCm-ExecutionProvider.html

https://onnxruntime.ai/docs/execution-providers/MIGraphX-ExecutionProvider.html

https://onnxruntime.ai/docs/execution-providers/Vitis-AI-ExecutionProvider.html

https://huggingface.co/docs/optimum/onnxruntime/usage_guides/amdgpu

https://github.com/ROCm/onnxruntime

https://rocm.docs.amd.com/projects/radeon/en/latest/docs/compatibility.html

https://huggingface.co/blog/huggingface-and-optimum-amd
