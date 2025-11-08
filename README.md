# CMake support for Dear ImGui

This project provides a single [CMakeLists.txt](./CMakeLists.txt) for building the [Dear ImGui](https://github.com/ocornut/imgui) as a static library.
It supports optional integration with [ImPlot](https://github.com/epezent/implot) for plotting capabilities and various rendering and platform backends (e.g., DirectX, Vulkan, OpenGL, GLFW, SDL, etc.).

Unlike other integrations, **imgui-cmake** focuses solely on building and does **NOT** manage source code (e.g., no submodules). 
This makes it easy to integrate into existing projects without altering repository structure.

The Example project can be found at [imgui-cmake-examples](https://github.com/WenchaoHuang/imgui-cmake-examples.git).


## Clone the Repository
```
    git clone https://github.com/WenchaoHuang/imgui-cmake.git
```


## Build Approaches
The `CMakeLists.txt` supports three ways to locate the ImGui source files:
1. **Place at the same level as the ImGui directory (Recommended)**:
    ```
     <parent-directory>/
     ├── ...
     ├── glfw/                  # GLFW directory (optional)
     ├── imgui/                 # ImGui directory (contains imgui.h)
     ├── implot/                # ImPlot directory (contains implot.h)
     └── imgui-cmake/           # This project
         └── CMakeLists.txt
    ```
2. **Copy CMakeLists.txt to ImGui Directory**:
     ```
     <imgui-directory>/
     ├── ...
     ├── imgui.h               # ImGui source (required)
     ├── implot.h              # ImPlot source (optional)
     └── CMakeLists.txt        # This project
     ```
3. **Manually Specify ImGui Directory**:
    ```
    cmake -DIMGUI_SOURCE_DIR=/path/to/imgui
    ```
If ImPlot is enabled (`IMGUI_EXT_IMPLOT=ON`), the same approaches apply for locating ImPlot source files.


## Example Usage
1. Add as a Subdirectory
    ```cmake
    add_subdirectory(imgui-cmake)
    target_link_libraries(MyApp PRIVATE ImGui::ImGui)
    ```
2. Enable Backends
    ```bash
    cmake -DIMGUI_BACKEND_WIN32=ON
    cmake -DIMGUI_BACKEND_VULKAN=ON
    cmake -DIMGUI_BUILD_EXAMPLES=ON
    cmake --build .
    ```
3. Integrate in Code
   ```cpp
   #include <imgui.h>
   
   #ifdef IMGUI_BACKEND_WIN32
       #include <backends/imgui_impl_win32.h>
   #endif
   
   #ifdef IMGUI_BACKEND_VULKAN
       #include <backends/imgui_impl_vulkan.h>
   #endif
   ```


## Backend Supports

| Backend Option | Windows | Linux | macOS | Android | Web (Emscripten) |
|----------------|----------|--------|--------|----------|------------------|
| `IMGUI_BACKEND_WIN32`         | ✅ | ❌ | ❌ | ❌ | ❌ |
| `IMGUI_BACKEND_DX9`           | ✅ | ❌ | ❌ | ❌ | ❌ |
| `IMGUI_BACKEND_DX10`          | ✅ | ❌ | ❌ | ❌ | ❌ |
| `IMGUI_BACKEND_DX11`          | ✅ | ❌ | ❌ | ❌ | ❌ |
| `IMGUI_BACKEND_DX12`          | ✅ | ❌ | ❌ | ❌ | ❌ |
| `IMGUI_BACKEND_OPENGL2`       | ✅ | ✅ | ✅ | ❌ | ❌ |
| `IMGUI_BACKEND_OPENGL3`       | ✅ | ✅ | ✅ | ❌ | ❌ |
| `IMGUI_BACKEND_GLUT`          | ✅ | ✅ | ✅ | ❌ | ❌ |
| `IMGUI_BACKEND_VULKAN`        | ✅ | ✅ | ✅ | ❌ | ❌ |
| `IMGUI_BACKEND_GLFW`          | ✅ | ✅ | ✅ | ❌ | ❌ |
| `IMGUI_BACKEND_SDL2`          | ✅ | ✅ | ✅ | ❌ | ❌ |
| `IMGUI_BACKEND_SDL3`          | ✅ | ✅ | ✅ | ❌ | ❌ |
| `IMGUI_BACKEND_SDLGPU3`       | ✅ | ✅ | ✅ | ❌ | ❌ |
| `IMGUI_BACKEND_SDLRENDERER2`  | ✅ | ✅ | ✅ | ❌ | ❌ |
| `IMGUI_BACKEND_SDLRENDERER3`  | ✅ | ✅ | ✅ | ❌ | ❌ |
| `IMGUI_BACKEND_ALLEGRO5`      | ✅ | ✅ | ✅ | ❌ | ❌ |
| `IMGUI_BACKEND_OSX`           | ❌ | ❌ | ✅ | ❌ | ❌ |
| `IMGUI_BACKEND_METAL`         | ❌ | ❌ | ✅ | ❌ | ❌ |
| `IMGUI_BACKEND_ANDROID`       | ❌ | ❌ | ❌ | ✅ | ❌ |
| `IMGUI_BACKEND_WGPU`          | ❌ | ❌ | ❌ | ❌ | ✅ |

### Additional Include Path Requirements

Some **platform backends** rely on external libraries that are **not bundled** with Dear ImGui.  
When these backends are enabled, you must manually specify the corresponding include directories.

| Backend                  | CMake Variable           | Header Location                                |
|--------------------------|--------------------------|------------------------------------------------|
| `IMGUI_BACKEND_SDL2`     | `IMGUI_SDL2_INCLUDE`     | `${IMGUI_SDL2_INCLUDE}/SDL.h`                  |
| `IMGUI_BACKEND_SDL3`     | `IMGUI_SDL3_INCLUDE`     | `${IMGUI_SDL3_INCLUDE}/SDL3/SDL.h`             |
| `IMGUI_BACKEND_GLFW`     | `IMGUI_GLFW_INCLUDE`     | `${IMGUI_GLFW_INCLUDE}/GLFW/glfw3.h`           |
| `IMGUI_BACKEND_GLUT`     | `IMGUI_GLUT_INCLUDE`     | `${IMGUI_GLUT_INCLUDE}/GL/freeglut.h`          |
| `IMGUI_BACKEND_ALLEGRO5` | `IMGUI_ALLEGRO5_INCLUDE` | `${IMGUI_ALLEGRO5_INCLUDE}/allegro5/allegro.h` |


## License
This project is distributed under the terms of the [MIT License](LICENSE).
