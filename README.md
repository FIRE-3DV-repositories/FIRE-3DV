# FIRE-3DV
![davinci robot](fire3dv.png "FIRE-3DV")
## Setup
### Assets
[Assets Repo](https://github.com/FIRE3D-repositories/src_sample_assets/tree/main/glTF)
- move assets into a new "assets" folder in the FIRE3D directory

### Vulkan setup
```
wget -qO- https://packages.lunarg.com/lunarg-signing-key-pub.asc | sudo tee /etc/apt/trusted.gpg.d/lunarg.asc
sudo wget -qO /etc/apt/sources.list.d/lunarg-vulkan-jammy.list http://packages.lunarg.com/vulkan/lunarg-vulkan-jammy.list
sudo apt update
sudo apt install vulkan-sdk
sudo apt install vulkan-validationlayers-dev spirv-tools
```

### Double-check Vulkan is working
```
vkcube
vulkaninfo --summary
```

### Get SDL2 
```
sudo apt-get install libsdl2-dev
```

### Compile shaders
- download glslc at https://github.com/google/shaderc/blob/main/downloads.md
- copy to /usr/local/bin

(from build directory)
```
glslc ../shaders/gradient_color.comp --target-env=vulkan1.3 -O -o ../shaders/gradient_color.comp.spv
glslc ../shaders/sky.comp --target-env=vulkan1.3 -O -o ../shaders/sky.comp.spv
glslc ../shaders/pbr.frag --target-env=vulkan1.3 -O -o ../shaders/pbr.frag.spv 
glslc ../shaders/pbr.vert --target-env=vulkan1.3 -O -o ../shaders/pbr.vert.spv 
glslc ../shaders/post_process.vert --target-env=vulkan1.3 -O -o ../shaders/post_process.vert.spv
glslc ../shaders/post_process.frag --target-env=vulkan1.3 -O -o ../shaders/post_process.frag.spv
```
### Run the engine
(from FIRE3D directory)
```
mkdir build
cd build
cmake ..
make
./FIRE3D
```

