# New! The Speed of Running OpenAI GPT OSS 120B on Dual AMD RX 7900 XTX (48GB in total)

Official document states that OpenAI GPT OSS 120B requires 80GB GPU to run, but I am testing it with dual AMD GPUs (dual RX 7900 XTX) with 48GB in total.  Watch the video for the decent result.

https://youtu.be/slcmpmW-H5Y

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

### iGPU setup instead?

For iGPU setup instead, please visit https://github.com/eliranwong/AMD_iGPU_AI_Setup

# Hardware Configurations for Multi-GPUs

* PCIe® slots connected to the GPU must have identical PCIe lane width or bifurcation settings, and support PCIe 3.0 Atomics.

* Only use PCIe slots connected by the CPU and to avoid PCIe slots connected via chipset. Refer to product-specific motherboard documentation for PCIe electrical configuration.

* Ensure the PSU has sufficient wattage to support multiple GPUs.

* Enable either iGPU or Discrete GPU

Read more at: https://rocm.docs.amd.com/projects/radeon/en/latest/docs/install/native_linux/mgpu.html

# Select Ubuntu and Kernel Versions

Read https://rocm.docs.amd.com/projects/install-on-linux/en/latest/reference/system-requirements.html#supported-distributions

If you need to install an additional kernel, read:

https://github.com/eliranwong/MultiAMDGPU_AIDev_Ubuntu/blob/main/Install_Ubuntu_Kernel.md

## Tested Device

> cat /etc/os-release

```
PRETTY_NAME="Ubuntu 24.04.2 LTS"
NAME="Ubuntu"
VERSION_ID="24.04"
VERSION="24.04.2 LTS (Noble Numbat)"
VERSION_CODENAME=noble
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=noble
LOGO=ubuntu-logo
```

> uname -srmv

```
Linux 6.11.0-17-generic #17~24.04.2-Ubuntu SMP PREEMPT_DYNAMIC Mon Jan 20 22:48:29 UTC 2 x86_64
```

# Add User to Groups for GPU Access

Add current user to groups 'render' and 'video'

> sudo usermod -a -G render,video $LOGNAME

To add all future users to the video and render groups by default, run the following commands:

```
echo 'ADD_EXTRA_GROUPS=1' | sudo tee -a /etc/adduser.conf
echo 'EXTRA_GROUPS=video' | sudo tee -a /etc/adduser.conf
echo 'EXTRA_GROUPS=render' | sudo tee -a /etc/adduser.conf
```

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

# Install ROCM 6.4.2

Version 6.4.2 is preferred, as it officaillly supports AMD Radeon™ 7000 series GPUs:

Read more at: https://rocm.docs.amd.com/projects/radeon/en/latest/docs/install/native_linux/howto_native_linux.html

## Uninstall Old Copies

```
amdgpu-install --uninstall
sudo apt remove --purge amdgpu-install
```

## Install via package amdgpu-install<br>

```
sudo apt update
sudo apt install -y libstdc++-12-dev
wget https://repo.radeon.com/amdgpu-install/6.4.2/ubuntu/noble/amdgpu-install_6.4.60402-1_all.deb
sudo apt install ./amdgpu-install_6.4.60402-1_all.deb
sudo amdgpu-install --usecase=graphics,multimedia,rocm,rocmdev,rocmdevtools,lrt,opencl,openclsdk,hip,hiplibsdk,openmpsdk,mllib,mlsdk --no-dkms -y
```

For more options of use cases, read https://rocm.docs.amd.com/projects/install-on-linux/en/latest/how-to/amdgpu-install.html#use-cases

To install ROCm inside a container, read: [Read https://github.com/eliranwong/incus_container_gui_setup](https://github.com/eliranwong/incus_container_gui_setup/blob/main/ubuntu_22.04_LTS_rocm_6.1.3_tested.md)

## Modify Grub to Avoid a Known Hang Issue

Issue: https://rocm.docs.amd.com/projects/install-on-linux/en/latest/how-to/native-install/install-faq.html#issue-5-application-hangs-on-multi-gpu-systems

Solution: Add "iommu=pt" to GRUB_CMDLINE_LINUX_DEFAULT in /etc/default/grub. 

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

To verify, run:

> rocminfo

Read https://rocm.docs.amd.com/projects/radeon/en/latest/docs/install/native_linux/install-radeon.html#post-install-verification-checks

# Fix Xorg issue

If you use Xorg instead of Wayland and have the issue where the mouse cursor is invisible, you can try to create the file /etc/X11/xorg.conf.d/99-modesetting.conf with the following content:

> sudo nano /etc/X11/xorg.conf.d/99-modesetting.conf

```
Section "Device"
      Identifier "modesetting"
      Driver "modesetting"
EndSection
```

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

**Configure Vulkan to use AMD graphics card** [optional]:

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

Look for information like:

```
*******                  
Agent 2                  
*******                  
  Name:                    gfx1100                            
  Uuid:                    GPU-b54ca445df90862b               
  Marketing Name:          Radeon RX 7900 XTX                 
  Vendor Name:             AMD 
  Feature:                 KERNEL_DISPATCH                    
  Profile:                 BASE_PROFILE                       
  Float Round Mode:        NEAR                               
  Max Queue Number:        128(0x80)                          
  Queue Min Size:          64(0x40)                           
  Queue Max Size:          131072(0x20000)                    
  Queue Type:              MULTI                              
  Node:                    1 

*******                  
Agent 3                  
*******                  
  Name:                    gfx1100                            
  Uuid:                    GPU-2ff163adb661d5fb               
  Marketing Name:          Radeon RX 7900 XTX                 
  Vendor Name:             AMD        
  Feature:                 KERNEL_DISPATCH                    
  Profile:                 BASE_PROFILE                       
  Float Round Mode:        NEAR                               
  Max Queue Number:        128(0x80)                          
  Queue Min Size:          64(0x40)                           
  Queue Max Size:          131072(0x20000)                    
  Queue Type:              MULTI                              
  Node:                    2
```

In this case, there are two GPUs, which are referred as device 0 and 1 later.

# Environment Variables

Modify the values to suit your cases.

The following examples assume:

* ROCm version 6.4.2 installed

* No integrated GPU

* Two AMD RX 7900 XTX installed.

Note: You may run `rocm-smi` to find the mapping information of node numbers to devices numbers.

## Overview

I use my case as an example:

Remarks:
* The following settings assumes `/opt/rocm` points to `/opt/rocm-6.4.2`.
* Modify the values of ROCR_VISIBLE_DEVICES to your own ones.

```
# rocm
export GFX_ARCH=gfx1100
export HCC_AMDGPU_TARGET=gfx1100
export CUPY_INSTALL_USE_HIP=1
export ROCM_VERSION=6.4
export ROCM_HOME=/opt/rocm
export LD_LIBRARY_PATH=/usr/include/vulkan:/opt/rocm/include:/opt/rocm/lib:/opt/rocm/lib/llvm/lib:/opt/rocm/lib/migraphx/lib:$LD_LIBRARY_PATH
export PATH=/home/eliran/.local/bin:/opt/rocm/bin:/opt/rocm/llvm/bin:$PATH
export HSA_OVERRIDE_GFX_VERSION=11.0.0
export ROCR_VISIBLE_DEVICES=GPU-b54ca445df90862b,GPU-2ff163adb661d5fb
export GPU_DEVICE_ORDINAL=0,1
export HIP_VISIBLE_DEVICES=0,1
export CUDA_VISIBLE_DEVICES=0,1
export LLAMA_HIPLAS=0,1
export DRI_PRIME=1
export OMP_DEFAULT_DEVICE=1
# vulkan
export GGML_VULKAN_DEVICE=0,1
export GGML_VK_VISIBLE_DEVICES=0,1
export VULKAN_SDK=/usr/share/vulkan
export VK_LAYER_PATH=$VULKAN_SDK/explicit_layer.d
```

ROCM_HOME - tells AI libraries where ROCM is stored; typically somewhere in /opt, e.g.:

> export ROCM_HOME=/opt/rocm

<details><summary>Explanation</summary>

`ROCM_HOME` is an environment variable in Linux that is used to specify the location of the ROCm (Radeon Open Compute) software on your system. ROCm is a set of open-source libraries and tools that are used to create high performance, machine learning applications on AMD GPUs.

When you install ROCm, it is typically installed in a directory under `/opt`. For example, if you installed version 6.1.3 of ROCm, it might be installed in `/opt/rocm`.

The `export` command in Linux is used to set environment variables. So, when you run the command `export ROCM_HOME=/opt/rocm`, you are telling the system "Whenever I refer to `ROCM_HOME`, I actually mean `/opt/rocm`".

This is useful because many AI libraries that use ROCm will look for the `ROCM_HOME` environment variable to know where to find the ROCm software. By setting `ROCM_HOME`, you ensure that these libraries can find and use ROCm correctly.

</details>

LD_LIBRARY_PATH - loader library path; typically this is set to $ROCM_HOME/lib. An indication you’re missing this flag is if you import pytorch and see an error like undefined reference to...

> export LD_LIBRARY_PATH=/opt/rocm/lib:$LD_LIBRARY_PATH

> export PATH=/opt/rocm/bin:/opt/rocm/opencl/bin:$PATH

<details><summary>Explanation</summary>

`LD_LIBRARY_PATH` is an environment variable that specifies a list of directories where the dynamic linker should look for dynamically linked libraries. When a program is launched, the dynamic linker checks the `LD_LIBRARY_PATH` to find the libraries that the program needs to run.

In this example, `LD_LIBRARY_PATH` is being set to include the `/opt/rocm/lib` directory. This is likely where the ROCm (Radeon Open Compute) libraries are installed. If you're trying to use PyTorch and it's built with ROCm support, it will need to know where these libraries are. If `LD_LIBRARY_PATH` doesn't include the ROCm library directory, you might see errors like "undefined reference to..." when you try to import PyTorch.

The command `export LD_LIBRARY_PATH=/opt/rocm/lib:$LD_LIBRARY_PATH` is adding `/opt/rocm/lib` to your existing `LD_LIBRARY_PATH`.

The `PATH` environment variable is similar, but it's used to tell the shell where to look for executable files. The command `export PATH=/opt/rocm/bin:/opt/rocm/opencl/bin:$PATH` is adding the `/opt/rocm/bin` and `/opt/rocm/opencl/bin` directories to your `PATH`. This means that when you type a command, the shell will also look in these directories to find it.

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

Remarks: Set HSA_OVERRIDE_GFX_VERSION=10.3.0 for 680M, and HSA_OVERRIDE_GFX_VERSION=11.0.0 for 780M.

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

- https://rocmdocs.amd.com/projects/HIP/en/develop/how-to/debugging.html#useful-environment-variables

- https://medium.com/@damngoodtech/amd-rocm-pytorch-and-ai-on-ubuntu-the-rules-of-the-jungle-24a7ab280b17

# Install Vulkan Tools

This is optional if you don't use vulkan.

```
sudo apt install vulkan-tools libvulkan-dev vulkan-validationlayers vulkan-validationlayers-dev
```

To verify:

```
vulkaninfo
```

## Check the path of $VULKAN_SDK

e.g.

> locate explicit_layer.d # /usr/share/vulkan/explicit_layer.d

To set related Vulkan SDK environment variables:

> export VULKAN_SDK=/usr/share/vulkan

> export VK_LAYER_PATH=$VULKAN_SDK/explicit_layer.d

Reference: https://github.com/ggerganov/llama.cpp#vulkan

# MIGraphX

Install ROCm before installing MIGraphX.  To install MIGraphX

```
sudo apt update && sudo apt install -y migraphx half
```

To test:

```
/opt/rocm/bin/migraphx-driver perf --test
```

Header files and libraries are installed under /opt/rocm-\<version\>, where \<version\> is the ROCm version.

Read: https://github.com/ROCm/AMDMIGraphX#amd-migraphx

# Set up Python

Use python version 3.10.x, to work with wheel files available at https://repo.radeon.com/rocm/manylinux/rocm-rel-6.1.3/

To install a specific version with pyenv, read https://github.com/eliranwong/MultiAMDGPU_AIDev_Ubuntu/blob/main/ubuntu_desktop/basic.md#pyenv

```
sudo apt update
sudo apt install -y make build-essential python3 python3-setuptools libjpeg-dev python3-pip python3-dev python3-venv libssl-dev libffi-dev libnss3 zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev python3-wheel python3-wheel-whl twine
```

# Python libraries

To make it clean, uninstall old copies, if any

> pip uninstall torch torchaudio torchvision cupy spacy numpy protobuf -y

# Set up a python virtual environment

```
python3 -m venv ai
source ai/bin/activate
pip3 install --upgrade pip wheel twine setuptools
```

# Install Compatible Versions of numpy and protobuf

Run or re-run this line if any conflicts about versions of numpy / protobuf:

```
pip install numpy==1.26.4 protobuf==4.25.3
```

Remarks: protobuf==5.29.1

# Install PyTorch & Triton

Read https://rocm.docs.amd.com/projects/radeon/en/latest/docs/install/native_linux/install-pytorch.html

```
wget https://repo.radeon.com/rocm/manylinux/rocm-rel-6.3.2/torch-2.4.0%2Brocm6.3.2-cp312-cp312-linux_x86_64.whl
wget https://repo.radeon.com/rocm/manylinux/rocm-rel-6.3.2/torchvision-0.19.0%2Brocm6.3.2-cp312-cp312-linux_x86_64.whl
wget https://repo.radeon.com/rocm/manylinux/rocm-rel-6.3.2/pytorch_triton_rocm-3.0.0%2Brocm6.3.2.75cc27c26a-cp312-cp312-linux_x86_64.whl
wget https://repo.radeon.com/rocm/manylinux/rocm-rel-6.3.2/torchaudio-2.4.0%2Brocm6.3.2-cp312-cp312-linux_x86_64.whl
pip3 uninstall torch torchvision pytorch-triton-rocm
pip3 install torch-2.4.0+rocm6.3.2-cp312-cp312-linux_x86_64.whl torchvision-0.19.0+rocm6.3.2-cp312-cp312-linux_x86_64.whl torchaudio-2.4.0+rocm6.3.2-cp312-cp312-linux_x86_64.whl pytorch_triton_rocm-3.0.0+rocm6.3.2.75cc27c26a-cp312-cp312-linux_x86_64.whl
```

To verify:

```
python3 -c 'import torch' 2> /dev/null && echo 'Success' || echo 'Failure'
python3 -c 'import torch; print(torch.cuda.is_available())'
python3 -c "import torch; print(f'device name [0]:', torch.cuda.get_device_name(0))"
python3 -c "import torch; print(f'device name [1]:', torch.cuda.get_device_name(1))"
python3 -m torch.utils.collect_env
```

Alternately, run:

> python3

```
>>> import torch
>>> torch.__version__
'2.1.2+rocm6.1.3'
>>> torch.cuda.is_available()
True
>>> torch.cuda.device_count()
2
>>> torch.cuda.get_device_properties(0).total_memory
25753026560
>>> torch.cuda.get_device_properties(1).total_memory
25753026560
>>> torch.cuda.current_device()
0
>>> torch.cuda.get_device_name(torch.cuda.current_device())
'Radeon RX 7900 XTX'
```

Read more at https://pytorch.org/get-started/locally/#linux-verification

# Install ONNX Runtime

Install `migraphx` FIRST!

```
pip3 uninstall onnxruntime-rocm
pip3 install onnxruntime-rocm -f https://repo.radeon.com/rocm/manylinux/rocm-rel-6.3.2/
```

To verify:

```
python3 -c "import onnxruntime; print(onnxruntime.get_available_providers())"
```

Expected output:

```
['MIGraphXExecutionProvider', 'ROCMExecutionProvider', 'CPUExecutionProvider']
```

# Work with ONNX ExecutionProviders

To use MiGraphX ExecutionProvider:

Read: https://onnxruntime.ai/docs/execution-providers/MIGraphX-ExecutionProvider.html

```
providers = [
    'MIGraphXExecutionProvider',
    'CPUExecutionProvider',
]
```

To use ROCm ExecutionProvider:

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

# Install Tensorflow

```
pip install tf-keras --no-deps
pip3 uninstall tensorflow-rocm
pip3 install https://repo.radeon.com/rocm/manylinux/rocm-rel-6.3.2/tensorflow_rocm-2.17.0-cp312-cp312-manylinux_2_28_x86_64.whl
```

To verify:

```
python3 -c 'import tensorflow' 2> /dev/null && echo 'Success' || echo 'Failure'
```

# Install Flash Attention 2 (pending update)

Links available at: https://github.com/ROCm/flash-attention/releases/

Remarks: The cxx11abi part of the filename indicates whether the package was built with the C++11 ABI (Application Binary Interface) enabled or not. The C++11 ABI is a set of rules that define how different parts of a C++ program interact at the binary level.

# Install Cupy (pending update)

Export required variables, if you haven't:

```
export CUPY_INSTALL_USE_HIP=1
export ROCM_HOME=/opt/rocm
export HCC_AMDGPU_TARGET=gfx1100
```

Install from source:

```
git clone https://github.com/cupy/cupy.git
cd cupy
git checkout rocm-ci-6.1
git submodule update --init
pip install git+https://github.com/ROCmSoftwarePlatform/hipify_torch.git
pip install .
```

To fix cicular import error, run again:

```
pip3 install torch-2.3.0+rocm6.3.2-cp310-cp310-linux_x86_64.whl torchvision-0.18.0+rocm6.3.2-cp310-cp310-linux_x86_64.whl torchaudio-2.5.0+rocm6.3.2-cp310-cp310-linux_x86_64.whl pytorch_triton_rocm-2.3.0+rocm6.3.2.5a02332983-cp310-cp310-linux_x86_64.whl
```

To verify:

```
python3 -c "import cupy; print(cupy.__version__)"
```

# Install Spacy (pending update)

With pytorch & cupy installed first, run:

> pip install spacy

# Install Piper Text-to-Speech

Piper-tts is a good offline tts engine that supports Linux. Its ONNX voice models are small in sizes that runs smooth even without GPUs.

An [issue](https://github.com/rhasspy/piper/issues/483) and a [pull request](https://github.com/rhasspy/piper/pull/512) are created to support piper to accelrate with AMD-GPUs.

Meanwhile, AMD-GPUs users can still workaround the issue with the following setup:

To support ROCm-enabled GPUs via 'ROCMExecutionProvider' or 'MIGraphXExecutionProvider':

1. Install piper-tts

> pip install piper-tts

2. Uninstall onnxruntime

> pip uninstall onnxruntime

3. Re-install onnxruntime-rocm

> pip install --force-reinstall onnxruntime_rocm-1.19.0-cp310-cp310-linux_x86_64.whl

4. Fix numpy and protobuf versions

> pip install numpy==1.26.4 protobuf==4.25.3

To verify:

> python3
```
$ import onnxruntime
$ onnxruntime.get_available_providers()
```

Output:
```
['MIGraphXExecutionProvider', 'ROCMExecutionProvider', 'CPUExecutionProvider']
```

Workaround:

Manually edit the 'load' function in the file ../site-packages/piper/voice.py:

From:

```
providers=["CPUExecutionProvider"]
if not use_cuda
else ["CUDAExecutionProvider"],
```

To:
```
providers=["MIGraphXExecutionProvider"],
```

To upgrade piper-tts, follow the following order:

1. Upgrade piper-tts

> pip install --upgrade piper-tts --no-cache-dir

2. Uninstall onnxruntime

> pip uninstall onnxruntime

3. Install Install onnxruntime-rocm again

> pip install --force-reinstall onnxruntime_rocm-1.19.0-cp310-cp310-linux_x86_64.whl

4. Manually edit the 'load' function in the file ../site-packages/piper/voice.py as described above.

# Check Versions of Installed Packages

Check combination of installed versions up to this point:

> pip list > pip_list.txt

https://github.com/eliranwong/pip_list.txt

# DeepSpeed

https://cloudblogs.microsoft.com/opensource/2022/03/21/supporting-efficient-large-model-training-on-amd-instinct-gpus-with-deepspeed/

# ollama

Standard installation: https://ollama.com/download

> curl -fsSL https://ollama.com/install.sh | sh

Configure Ollama, run:

> sudo nano /etc/systemd/system/ollama.service

Add the following three lines at the end of the [Service] session:

```
Environment="OLLAMA_NUM_PARALLEL=2"
Environment="OLLAMA_MAX_LOADED_MODELS=2"
Environment="OLLAMA_HOST=0.0.0.0"
```

Reload Ollama, run:

> sudo systemctl daemon-reload

> sudo systemctl restart ollama

Add user to group `ollama` for access of Ollama directory:

> sudo usermod -a -G ollama $LOGNAME

> sudo reboot

## VS Code Plugin with Ollama

Install VS code plugin `twinny` by `rjmacarthy`

Download LLMs to work with twinny, e.g.:

```
ollama pull codellama:7b-instruct
ollama pull codellama:7b-code
```

Click "Manage twinny providers" for more options.

# Build llama.cpp that runs ROCm backend

Run in terminal:

```
git clone https://github.com/ggml-org/llama.cpp
mv llama.cpp/ llamacpp_rocm/
cd llamacpp_rocm
HIPCXX="$(hipconfig -l)/clang" HIP_PATH="$(hipconfig -R)" cmake -S . -B build -DGGML_HIP=ON -DAMDGPU_TARGETS=gfx1100 -DCMAKE_BUILD_TYPE=Release && cmake --build build --config Release -- -j $(lscpu | grep -m 1 '^Core(s)' | awk '{print $NF}')
```

Expected lines in the terminal output:

```
...
-- Adding CPU backend variant ggml-cpu: -march=native 
-- The HIP compiler identification is Clang 18.0.0
-- Detecting HIP compiler ABI info
-- Detecting HIP compiler ABI info - done
-- Check for working HIP compiler: /opt/rocm-6.3.2/lib/llvm/bin/clang - skipped
-- Detecting HIP compile features
-- Detecting HIP compile features - done
-- HIP and hipBLAS found
-- Including HIP backend
...
```

# Build llama.cpp that runs Vulkan backend

As an alternative to ROCm backend, you may build a copy of llama.cpp that runs Vulkan backend.

To set up Vulkan driver:

```
sudo apt install -y glslc glslang-tools glslang-dev mesa-vulkan-drivers vulkan-amdgpu vulkan-tools libvulkan-dev vulkan-validationlayers vulkan-utility-libraries-dev
```

To build run:

```
git clone https://github.com/ggml-org/llama.cpp
mv llama.cpp/ llamacpp_vulkan/
cd llamacpp_vulkan
cmake -S . -B build -DGGML_VULKAN=ON  -DCMAKE_BUILD_TYPE=Release && cmake --build build --config Release -- -j $(lscpu | grep -m 1 '^Core(s)' | awk '{print $NF}')
```

Expected lines in the terminal output:

```
...
-- Adding CPU backend variant ggml-cpu: -march=native 
-- Found Vulkan: /usr/lib/x86_64-linux-gnu/libvulkan.so (found version "1.3.275") found components: glslc glslangValidator 
-- Vulkan found
-- GL_KHR_cooperative_matrix supported by glslc
-- GL_NV_cooperative_matrix2 not supported by glslc
-- Including Vulkan backend
...
```

Make sure you set the vulkan-related variables, e.g. https://github.com/eliranwong/MultiAMDGPU_AIDev_Ubuntu#overview

## Alias for launching llama-server with ROCm backend

Run in terminal:

```
cd llamacpp_rocm
echo "alias llamacpp=\"cd /home/$USER/agentmake/models/gguf/ && $(pwd)/build/bin/llama-server --threads $(lscpu | grep -m 1 '^Core(s)' | awk '{print $NF}') -ngl 99 --model\"" >> $HOME/.bashrc
```

Remarks: We add `-ngl 99` in the alias to offload as many layers as available to GPU. Depending on your device hardware, you may need to reduce the value of ngl to load large-sized models.

## Working with Large-size Files

* Adjust the number of layers with `-ngl` to the maximum possible vaule for loading the files on your devices.
* You may want to control the context size and the output tokens too.

For examples:

> ./llama-cli -m ../gguf/command-r-plus.gguf -p "What is machine learning?" --temp 0.0 -ngl 20 -c 2048 -n 2048 -t 24 -ngl 48

> ./llama-cli -m ../gguf/wizardlm2_8x22b.gguf -p "What is machine learning?" --temp 0.0 -ngl 20 -c 2048 -n 2048 -t 24 -ngl 34

## Speed Test: CPU vs CPU+GPUx2

https://github.com/eliranwong/MultiAMDGPU_AIDev_Ubuntu/blob/main/cpu_vs_gpux2.md

## Speed Test: Vulkan vs ROCm

https://github.com/eliranwong/MultiAMDGPU_AIDev_Ubuntu/blob/main/vulkan_vs_rocm.md

## More Benchmark

https://github.com/eliranwong/MultiAMDGPU_AIDev_Ubuntu/blob/main/benchmark.md

# Install Llama-cpp-python Packages

The author managed to installed llama.cpp with 

> CMAKE_ARGS="-DLLAMA_CLBLAST=on" pip install llama-cpp-python

Alternately,

Use hipBLAS (ROCm) as backend:

> sudo apt install libc6-dev libstdc++-12-dev

> CMAKE_ARGS="-DLLAMA_HIPBLAS=on" pip install llama-cpp-python

Use Vulkan as backend:

> CMAKE_ARGS="-DLLAMA_VULKAN=on" pip install llama-cpp-python

Read more at: https://llama-cpp-python.readthedocs.io/en/stable/

## Troubleshoot Tensor Split Issue

[an issue regarding tensor_split feature](https://github.com/abetlen/llama-cpp-python/issues/1166)

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

# Stable-diffusion-cpp-python

```
CMAKE_ARGS="-DCMAKE_C_COMPILER=clang -DCMAKE_CXX_COMPILER=clang++ -DSD_HIPBLAS=ON -DCMAKE_BUILD_TYPE=Release -DAMDGPU_TARGETS=gfx1100" pip install stable-diffusion-cpp-python --no-cache-dir
```

# stable-diffusion-webui

Setup of stable-diffusion-webui is straightforward as follows:

```
sudo apt install google-perftools libgl1
git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui
cd stable-diffusion-webui
python3 -m venv venv
./webui.sh

# Set up an alias [optional]
echo 'alias sdwebui="'$(pwd)'/webui.sh"' >> ~/.bashrc
```

```
open http://127.0.0.1:7860
```

Read more at: https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/Install-and-Run-on-AMD-GPUs

# ComfyUI

[Official setup instructions](https://github.com/comfyanonymous/ComfyUI) works:

```
git clone https://github.com/comfyanonymous/ComfyUI
cd ComfyUI/custom_nodes
git clone https://github.com/ltdrdata/ComfyUI-Manager
cd ..
python3 -m venv venv
source venv/bin/activate
pip install --pre torch torchvision torchaudio --index-url https://download.pytorch.org/whl/nightly/rocm6.3
python3 -m pip install -r requirements.txt  --extra-index-url https://download.pytorch.org/whl/nightly/rocm6.3
python3 -m pip install -r custom_nodes/ComfyUI-Manager/requirements.txt --extra-index-url https://download.pytorch.org/whl/nightly/rocm6.3
python3 main.py

# Set up an alias [optional]
echo 'alias comfyui="'$(pwd)'/venv/bin/python3 '$(pwd)'/main.py"' >> ~/.bashrc
```

```
open http://127.0.0.1:8188
```

# SwarmUI + Flux in GGUF Formats

1. Install SwarmUI

Read the latest instructions at: https://github.com/mcmonkeyprojects/SwarmUI#installing-on-linux

```
wget https://github.com/mcmonkeyprojects/SwarmUI/releases/download/0.6.5-Beta/install-linux.sh -O install-linux.sh
chmod +x install-linux.sh
./install-linux.sh
```
2. Select AMD version during the installation process

![amd_version](https://github.com/user-attachments/assets/2ccc0862-e4e3-4d15-85a7-5d604d74044b)

3. After SwarmUI is installed, stop the server first.

4. Download a gguf file from GGUF Quantized "unet" models repositories, such as Flux Schnell https://huggingface.co/city96/FLUX.1-schnell-gguf/tree/main or Flux Dev https://huggingface.co/city96/FLUX.1-dev-gguf/tree/main

5. Place the donwload file(s) in folder `Models/unet` inside SwarmUI directory

6. Download file `ae.safetensors` from https://huggingface.co/black-forest-labs/FLUX.1-dev/tree/main and place it in `Models/VAE` inside SwarmUI directory

7. Run `./launch-linux.sh` to launch SwarmUI

8. In "Models" tab, edit model metadata of the gguf file and select the correct architecture, e.g. Flux.1 Dev

![edit_model_metadata](https://github.com/user-attachments/assets/fdcde050-476e-4a6c-8d58-0ee41f37ecb3)

8. Enter a prompt and generate an image. Confirm to install GGUF support when prompted.

![install_GGUF_support](https://github.com/user-attachments/assets/97ac8b50-2e84-45e9-a69f-d1d8b7080e84)

Remarks:

* a cfg_scale of 1 is recommended for FLUX
* euler sampling method is recommended for FLUX

9. Set an alias, assuming your current location at SwamUI directory:

> echo 'alias swarmui='$(pwd)'/launch-linux.sh' >> ~/.bashrc

# fabric

Install pipx xsel and ffmpeg to work with fabric:

```
sudo apt install -y pipx xsel ffmpeg
git clone https://github.com/danielmiessler/fabric.git
cd fabric
pipx install .
tee --append $HOME/.bashrc <<EOF
alias pbcopy='xsel -b -i'
alias pbpaste='xsel -b -o'
EOF
fabric --setup
source $HOME/.bashrc
```

# perplexica

Install docker first and run:

```
sudo apt install -y git
git clone https://github.com/ItzCrazyKns/Perplexica.git
cd Perplexica
cp sample.config.toml config.toml
docker compose up -d
open localhost:3000
```

# CLI and Desktop Integration with AgentMake AI

Run in terminal:

```
# optional: navigate to home directory
cd
# install in a virtual environment
python3 -m venv ai
source ai/bin/activate
pip install --upgrade agentmake[genai]
echo ". /home/$USER/ai/bin/activate" >> ~/.bashrc
# To test
ai Hi!
```

## Edit Configurations

To edit configurations or add API keys, run in terminal:

> ai -ec

## Test with Ollama

> ai Hi!

Remarks: Ollama is set as the default backend, so you can use the `ai` or `aic` commands without specifying the backend option. Run `ai -ec` to edit configurations.

## Test with Chat Feature

Use command `aic` with chat features enabled, e.g.:

> aic Tell me a joke.

Close the terminal app and reopen it

> aic Tell me one more.

Chat history is saved locally and recalled even the terminal session is ended.

Become a new conversation with `-n` option, e.g.:

> aic -n Hi!

## Test with Llama.cpp

You can run llama.cpp server with the model files downloaded via Ollama.

To access ollama model files, add user to group `ollama`:

> sudo usermod -a -G ollama $LOGNAME

> sudo reboot

To download a model via Ollama and save a copy of it in `~/agentmake/models/gguf/` by default, e.g.:

> ai --get_model deepseek-r1 -gm llama3.3:70b -gm aya-expanse

To run an instance of llama-server, assuming that you have set up an alias as mentioned [here](https://github.com/eliranwong/MultiAMDGPU_AIDev_Ubuntu#alias-for-launching-llama-server-with-rocm-backend), e.g.:

> llamacpp deepseek-r1.gguf

To run agentmake with llama.cpp, e.g.:

> ai -b llamacpp Hi!

## Test with Perplexica

To list available tools that work with perplexica, run:

> ai -lt | grep perplexica

Expected output:

```
perplexica/openai
perplexica/groq
perplexica/xai
perplexica/googleai
perplexica/anthropic
perplexica/github
```

To use one of them, e.g.:

> ai -t perplexica/github What is AgentMake AI?

## Test with SearXNG

SearXNG is automatically installed with Perplexica, to get real-time information, e.g.:

> ai -t search/searxng Give me news updates in London today.

## Test with Fabric Integration

Assuming fabric patterns are downloaded, e.g.:

> ai What are AI agents? -sys fabric.write_micro_essay -b genai

## Test with Selected Text in Any Applicaitons

First, make sure `xsel` is installed:

> sudo apt install xsel

Launch `Settings` > Keyboard > View and Customise Shortcuts > Custom Shortcuts > +

Fill in content, like below (replace `username` with your `username`: 

```
Name: AgentMake AI
Command: gnome-terminal -- bash -c "/home/username/ai/bin/ai -i -eo -py"
Shift+Ctrl+A
```

![Image](https://github.com/user-attachments/assets/d21fea9a-2288-4e85-96ad-dfbee7ce160d)

Select some text in an application, then press `Shift+Ctrl+A`.

Choose a predefined instruction:

![Image](https://github.com/user-attachments/assets/e4872498-0cef-48e7-a550-55c0c4234929)

Assistant response is automatically copied to clipboard.

Remarks: You can define up to 10 custom instructions for being selected in the dialog, by specifying the values of `CUSTOM_INSTRUCTION_1`, `CUSTOM_INSTRUCTION_2`, `CUSTOM_INSTRUCTION_3`, ... `CUSTOM_INSTRUCTION_10` in AgentMake configurations (run `ai -ec` to edit).

## Test with Tool in Custom instruction

You can specify a tool in a custom instruction by prefixing the tool name with symbol `@`

For example, if you want to extract a Youtube url from the selected text, download the video and convert it into mp3:

Edit configuration:

> ai -ec

Edit the item `CUSTOM_INSTRUCTION_1`:

```
CUSTOM_INSTRUCTION_1="@youtube/download_audio"
```

Try to highlight a text that contains a YouTube url, in any applications, then press `Shift+Ctrl+A`.

## Test with Image Creation with Flux

Requirement: To run the following example, you need to manually download the file `ae.safetensors` from https://huggingface.co/black-forest-labs/FLUX.1-dev and place it in `~/agentmake/models/flux`.

To check available tools, to work with Flux:

> ai -lt | grep flux

```output
images/create_flux_portrait
images/create_flux_landscape
images/create_flux
```

To create an image, e.g.:

> ai -t images/create_flux a cute cat

![Image](https://github.com/user-attachments/assets/441d6ac8-4f2d-449a-a188-616529a595e9)

Remarks: There are `iw`, `ih` and `iss` for adjusting the image output.

## Note about Azure AI Setup

An easy way to deploy AI models via Azure service:

1. Sign in https://ai.azure.com/github
2. All resources > Create New
3. Overview > copy an API key, Azure OpenAI Service and Azure AI inference endpoints

* Use Azure OpenAI Service endpoint for running OpenAI models; the endpoint should look like https://resource_name.openai.azure.com/

* Use Azure AI inference endpoint for running DeepSeek-R1 and Phi-4; the endpoint should look like https://resource_name.services.ai.azure.com/models

To configure AgentMake AI, run:

> ai -ec

## Note about Vertex AI

Make sure the extra package `genai` is installed with the command mentioned above:

> pip install --upgrade "agentmake[genai]"

To configure, run:

> ai -ec

Enter the path of your Google application credentials JSON file as the value of `VERTEXAI_API_KEY`. You need to specify your project ID and service location, in the configurations, as well. e.g.:

```
VERTEXAI_API_KEY=~/agentmake/google_application_credentials.json
VERTEXAI_API_PROJECT_ID=my_project_id
VERTEXAI_API_SERVICE_LOCATION=us-central1
```

To test Gemini 2.0 with Vertex AI, e.g.:

> ai -b vertexai -m gemini-2.0-flash Hi!

## Using other backends and tools

To list all available tools:

> ai -lt

For all options, run:

> ai -h

To edit configurations, run:

> ai -ec

AgentMake AI supports 14 AI backends and 7 agentic components.

Read more at https://github.com/eliranwong/agentmake

# Llama Factory

```
git clone --depth 1 https://github.com/hiyouga/LLaMA-Factory.git
cd LLaMA-Factory
python3 -m venv rocm
source rocm/bin/activate
pip install -e ".[metrics]"
pip uninstall torch triton -y
wget https://repo.radeon.com/rocm/manylinux/rocm-rel-6.3.2/torch-2.3.0%2Brocm6.3.2-cp310-cp310-linux_x86_64.whl
wget https://repo.radeon.com/rocm/manylinux/rocm-rel-6.3.2/torchvision-0.18.0%2Brocm6.3.2-cp310-cp310-linux_x86_64.whl
wget https://repo.radeon.com/rocm/manylinux/rocm-rel-6.3.2/pytorch_triton_rocm-2.3.0%2Brocm6.3.2.5a02332983-cp310-cp310-linux_x86_64.whl
pip3 install torch-2.3.0+rocm6.3.2-cp310-cp310-linux_x86_64.whl torchvision-0.18.0+rocm6.3.2-cp310-cp310-linux_x86_64.whl pytorch_triton_rocm-2.3.0+rocm6.3.2.5a02332983-cp310-cp310-linux_x86_64.whl
pip install --upgrade huggingface_hub
```

To login hugging face:

```
huggingface-cli login
```

To run webui:

> env HIP_VISIBLE_DEVICES=0 llamafactory-cli webui

Note: Llama Factory currently fails to run training when mulitple GPUs are used, e.g.:

> env HIP_VISIBLE_DEVICES=0,1 llamafactory-cli webui


# Performance Optimization

For performance optimization, you may read:

https://huggingface.co/docs/optimum/main/en/amd/amdgpu/overview

https://github.com/nktice/AMD-AI/blob/main/performance-tuning.md

# JAX

https://jax.readthedocs.io/en/latest/developer.html#additional-notes-for-building-a-rocm-jaxlib-for-amd-gpus

https://keras.io/guides/distributed_training_with_jax/

# CUDA-compatible Alternative

https://github.com/vosen/ZLUDA

Current known issues of ZLUDA: https://github.com/vosen/ZLUDA#known-issues

# References

https://rocm.docs.amd.com/projects/install-on-linux/en/latest/index.html

https://rocm.docs.amd.com/en/latest/how-to/rocm-for-ai/index.html
