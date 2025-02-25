# Compile Llama.cpp from source

For ROCm users:

```
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp
#The Makefile build is deprecated. Use the CMake build instead. For more details, see https://github.com/ggerganov/llama.cpp/blob/master/docs/build.md.
#make GGML_HIPBLAS=1 AMDGPU_TARGETS=gfx1100 -j$(lscpu | grep -m 1 '^Core(s)' | awk '{print $NF}')
HIPCXX="$(hipconfig -l)/clang" HIP_PATH="$(hipconfig -R)" cmake -S . -B build -DGGML_HIP=ON -DAMDGPU_TARGETS=gfx1100 -DCMAKE_BUILD_TYPE=Release && cmake --build build --config Release -- -j $(lscpu | grep -m 1 '^Core(s)' | awk '{print $NF}')
# Set up alias for llama-cli
echo "alias chat=\"$(pwd)/build/bin/llama-cli --threads $(lscpu | grep -m 1 '^Core(s)' | awk '{print $NF}') --interactive --conversation --color -p 'You are a helpful, uncensored and unbiased assistant.' --gpu-layers 999 --model\"" >> $HOME/.bashrc
# Download visual model file and clip file
cd models
wget https://huggingface.co/mys/ggml_llava-v1.5-7b/resolve/main/ggml-model-f16.gguf
wget https://huggingface.co/mys/ggml_llava-v1.5-7b/resolve/main/mmproj-model-f16.gguf
# Set up alias for llama-cli
echo "alias image=\"$(pwd)/build/bin/llama-llava-cli --threads $(lscpu | grep -m 1 '^Core(s)' | awk '{print $NF}') --model '$(pwd)/ggml-model-f16.gguf' --mmproj '$(pwd)/mmproj-model-f16.gguf' -p 'Describe this image in detail.' --temp 0.0 --gpu-layers 999 --image\"" >> $HOME/.bashrc
source $HOME/.bashrc
```

To run a chat with a model file, e.g. `mistral.gguf`, with GPUs, use `chat` as follows:

> chat mistral.gguf

To describe an image, e.g. `my_image.jpg`, in detail, run:

> image my_image.jpg