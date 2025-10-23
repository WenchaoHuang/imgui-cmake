# CMake support for Dear ImGui

This project provides a single [CMakeLists.txt](./CMakeLists.txt) for building the [Dear ImGui](https://github.com/ocornut/imgui) library, a bloat-free immediate mode graphical user interface library for C++. It supports optional integration with [ImPlot](https://github.com/epezent/implot) for plotting capabilities and various rendering and platform backends (e.g., DirectX, Vulkan, OpenGL, GLFW, SDL, etc.). The library is built as a static library and includes optional example applications to demonstrate its usage.

To minimize changes to existing build systems, imgui-cmake is solely responsible for building and dose ***NOT*** manage source code (e.g., via submodules), which is the primary distinction from other similar libraries.

## Supported Build Approaches
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
If ImPlot is enabled (`IMGUI_ENABLE_IMPLOT=ON`), the same approaches apply for locating ImPlot source files.


## Usage


## License
This library is distributed under the terms of the [MIT License](LICENSE).
