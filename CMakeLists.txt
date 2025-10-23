# Copyright (c) 2025 Wenchao Huang <physhuangwenchao@gmail.com>
# 
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
# 
# The above copyright notice and this permission notice shall be included in all
# copies or substantial portions of the Software.
# 
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.

cmake_minimum_required(VERSION 3.10)

# Target name
set(TARGET_NAME imgui)

# Project name
project(${TARGET_NAME} LANGUAGES CXX)

################################################################################
#########################   Locate source directory    #########################
################################################################################

# Search order for ImGui:
# 1. Check if ImGui source files are located in the parent directory under "../imgui" (recommended).
# 2. If not found, check the current directory.
# 3. If neither exists, require the user to manually specify IMGUI_SOURCE_DIR.

if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/../imgui/imgui.h")
	set(IMGUI_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../imgui" CACHE PATH "Path to the ImGui source directory")
elseif(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/imgui.h")
	set(IMGUI_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}" CACHE PATH "Path to the ImGui source directory")
else()
	set(IMGUI_SOURCE_DIR "" CACHE PATH "Path to the ImGui source directory")
endif()

if(NOT EXISTS "${IMGUI_SOURCE_DIR}/imgui.h")
	message(FATAL_ERROR "Invalid IMGUI_SOURCE_DIR: ${IMGUI_SOURCE_DIR}. Please set it to a valid ImGui source directory.")
endif()

message(STATUS "ImGui source directory: ${IMGUI_SOURCE_DIR}")

################################################################################
##############################   Locate ImPlot    ##############################
################################################################################

# Enable ImPlot (optional)
option(IMGUI_ENABLE_IMPLOT "Enable ImPlot support" OFF)

# Search order for ImPlot:
# 1. Check if ImPlot source files are located in the parent directory under "../implot" (recommended).
# 2. If not found, check the current directory.
# 3. If neither exists, require the user to manually specify IMPLOT_SOURCE_DIR.

if(IMGUI_ENABLE_IMPLOT)
    if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/../implot/implot.h")
        set(IMPLOT_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../implot" CACHE PATH "Path to the ImPlot source directory")
    elseif(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/implot.h")
        set(IMPLOT_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}" CACHE PATH "Path to the ImPlot source directory")
    else()
        set(IMPLOT_SOURCE_DIR "" CACHE PATH "Path to the ImPlot source directory")
    endif()

    if(NOT EXISTS "${IMPLOT_SOURCE_DIR}/implot.h")
        message(FATAL_ERROR "Invalid IMPLOT_SOURCE_DIR: ${IMPLOT_SOURCE_DIR}. Please set it to a valid ImPlot source directory.")
    endif()

    message(STATUS "ImPlot source directory: ${IMPLOT_SOURCE_DIR}")
endif()

################################################################################
#############################   Backend options    #############################
################################################################################

if(CMAKE_SYSTEM_NAME MATCHES "Windows")
	option(IMGUI_BACKEND_DX9			"Enable DirectX9 backend"		OFF)
	option(IMGUI_BACKEND_DX10			"Enable DirectX10 backend"		OFF)
	option(IMGUI_BACKEND_DX11			"Enable DirectX11 backend"		OFF)
	option(IMGUI_BACKEND_DX12			"Enable DirectX12 backend"		OFF)
	option(IMGUI_BACKEND_WIN32			"Enable Win32 backend"			OFF)
endif()

if(CMAKE_SYSTEM_NAME MATCHES "Linux|Darwin|Windows")
	option(IMGUI_BACKEND_VULKAN			"Enable Vulkan backend"			OFF)
	option(IMGUI_BACKEND_OPENGL2		"Enable OpenGL2 backend"		OFF)
	option(IMGUI_BACKEND_OPENGL3		"Enable OpenGL3 backend"		OFF)
	option(IMGUI_BACKEND_GLFW			"Enable GLFW backend"			OFF)
	option(IMGUI_BACKEND_GLUT			"Enable GLUT backend"			OFF)
	option(IMGUI_BACKEND_SDL2			"Enable SDL2 backend"			OFF)
	option(IMGUI_BACKEND_SDL3			"Enable SDL3 backend"			OFF)
	option(IMGUI_BACKEND_SDLGPU3		"Enable SDLGPU3 backend"		OFF)
	option(IMGUI_BACKEND_SDLRENDERER2	"Enable SDLRenderer2 backend"	OFF)
	option(IMGUI_BACKEND_SDLRENDERER3	"Enable SDLRenderer3 backend"	OFF)
endif()

if(CMAKE_SYSTEM_NAME MATCHES "Darwin")
	option(IMGUI_BACKEND_OSX			"Enable OSX backend"			OFF)
	option(IMGUI_BACKEND_METAL			"Enable Metal backend"			OFF)
endif()

if(CMAKE_SYSTEM_NAME MATCHES "Web")
	option(IMGUI_BACKEND_WGPU			"Enable WebGPU backend"			OFF)
endif()

################################################################################
#############################   Collect sources    #############################
################################################################################

# Core headers
set(IMGUI_HEADERS
	${IMGUI_SOURCE_DIR}/imgui.h
	${IMGUI_SOURCE_DIR}/imconfig.h
	${IMGUI_SOURCE_DIR}/imgui_internal.h
)

# Core sources
set(IMGUI_SOURCES
	${IMGUI_SOURCE_DIR}/imgui.cpp
	${IMGUI_SOURCE_DIR}/imgui_demo.cpp
	${IMGUI_SOURCE_DIR}/imgui_draw.cpp
	${IMGUI_SOURCE_DIR}/imgui_tables.cpp
	${IMGUI_SOURCE_DIR}/imgui_widgets.cpp
)

# ImPlot sources
if(IMGUI_ENABLE_IMPLOT)
    list(APPEND IMGUI_HEADERS
        ${IMPLOT_SOURCE_DIR}/implot.h
        ${IMPLOT_SOURCE_DIR}/implot_internal.h
    )
    list(APPEND IMGUI_SOURCES
        ${IMPLOT_SOURCE_DIR}/implot.cpp
		${IMPLOT_SOURCE_DIR}/implot_demo.cpp
        ${IMPLOT_SOURCE_DIR}/implot_items.cpp
    )
endif()

if(IMGUI_BACKEND_DX9)
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_dx9.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_dx9.cpp")
endif()

if(IMGUI_BACKEND_DX10)
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_dx10.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_dx10.cpp")
endif()

if(IMGUI_BACKEND_DX11)
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_dx11.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_dx11.cpp")
endif()

if(IMGUI_BACKEND_DX12)
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_dx12.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_dx12.cpp")
endif()

if(IMGUI_BACKEND_WGPU)
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_wgpu.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_wgpu.cpp")
endif()

if(IMGUI_BACKEND_METAL)
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_metal.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_metal.mm")
endif()

if(IMGUI_BACKEND_VULKAN)
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_vulkan.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_vulkan.cpp")
endif()

if(IMGUI_BACKEND_OPENGL2)
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_opengl2.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_opengl2.cpp")
endif()

if(IMGUI_BACKEND_OPENGL3)
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_opengl3.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_opengl3.cpp")
endif()

if(IMGUI_BACKEND_OSX)
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_osx.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_osx.mm")
endif()

if(IMGUI_BACKEND_GLFW)
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_glfw.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_glfw.cpp")
endif()

if(IMGUI_BACKEND_GLUT)
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_glut.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_glut.cpp")
endif()

if(IMGUI_BACKEND_WIN32)
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_win32.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_win32.cpp")
endif()

if(IMGUI_BACKEND_SDL2)
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_sdl2.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_sdl2.cpp")
endif()

if(IMGUI_BACKEND_SDL3)
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_sdl3.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_sdl3.cpp")
endif()

if(IMGUI_BACKEND_SDLGPU3)
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_sdlgpu3.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_sdlgpu3.cpp")
endif()

if(IMGUI_BACKEND_SDLRENDERER2)
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_sdlrenderer2.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_sdlrenderer2.cpp")
endif()

if(IMGUI_BACKEND_SDLRENDERER3)
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_sdlrenderer3.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_sdlrenderer3.cpp")
endif()

################################################################################
#############################   Create library    ##############################
################################################################################

# Create the static library
add_library(${TARGET_NAME} STATIC ${IMGUI_HEADERS} ${IMGUI_SOURCES})

# Include directories
target_include_directories(${TARGET_NAME} PUBLIC ${IMGUI_SOURCE_DIR})

if(IMGUI_ENABLE_IMPLOT)
	target_include_directories(${TARGET_NAME} PUBLIC ${IMPLOT_SOURCE_DIR})
endif()

if(IMGUI_BACKEND_VULKAN)
	find_package(Vulkan REQUIRED)
	target_link_libraries(${TARGET_NAME} PRIVATE Vulkan::Vulkan)
endif()

# MSVC settings
if(MSVC)
    target_compile_options(${TARGET_NAME} PRIVATE /MP /W4)
endif()

################################################################################
#############################   Build examples    ##############################
################################################################################

# Add examples (optional)
option(IMGUI_BUILD_EXAMPLES "Build ImGui examples" OFF)

if(IMGUI_BUILD_EXAMPLES)
	set(EXAMPLE_NAME example_null)
	add_executable(${EXAMPLE_NAME} "${IMGUI_SOURCE_DIR}/examples/${EXAMPLE_NAME}/main.cpp")
	target_include_directories(${EXAMPLE_NAME} PRIVATE "${IMGUI_SOURCE_DIR}/backends")
	target_link_libraries(${EXAMPLE_NAME} imgui)

	if(IMGUI_BACKEND_WIN32)
		if(IMGUI_BACKEND_DX9)
			set(EXAMPLE_NAME example_win32_directx9)
			add_executable(${EXAMPLE_NAME} "${IMGUI_SOURCE_DIR}/examples/${EXAMPLE_NAME}/main.cpp")
			target_include_directories(${EXAMPLE_NAME} PRIVATE "${IMGUI_SOURCE_DIR}/backends")
			target_link_libraries(${EXAMPLE_NAME} imgui d3d9)
		endif()

		if(IMGUI_BACKEND_DX10)
			set(EXAMPLE_NAME example_win32_directx10)
			add_executable(${EXAMPLE_NAME} "${IMGUI_SOURCE_DIR}/examples/${EXAMPLE_NAME}/main.cpp")
			target_include_directories(${EXAMPLE_NAME} PRIVATE "${IMGUI_SOURCE_DIR}/backends")
			target_link_libraries(${EXAMPLE_NAME} imgui d3d10)
		endif()

		if(IMGUI_BACKEND_DX11)
			set(EXAMPLE_NAME example_win32_directx11)
			add_executable(${EXAMPLE_NAME} "${IMGUI_SOURCE_DIR}/examples/${EXAMPLE_NAME}/main.cpp")
			target_include_directories(${EXAMPLE_NAME} PRIVATE "${IMGUI_SOURCE_DIR}/backends")
			target_link_libraries(${EXAMPLE_NAME} imgui d3d11)
		endif()

		if(IMGUI_BACKEND_DX12)
			set(EXAMPLE_NAME example_win32_directx12)
			add_executable(${EXAMPLE_NAME} "${IMGUI_SOURCE_DIR}/examples/${EXAMPLE_NAME}/main.cpp")
			target_include_directories(${EXAMPLE_NAME} PRIVATE "${IMGUI_SOURCE_DIR}/backends")
			target_link_libraries(${EXAMPLE_NAME} imgui d3d12 dxgi)
		endif()

		if(IMGUI_BACKEND_VULKAN)
			set(EXAMPLE_NAME example_win32_vulkan)
			add_executable(${EXAMPLE_NAME} "${IMGUI_SOURCE_DIR}/examples/${EXAMPLE_NAME}/main.cpp")
			target_include_directories(${EXAMPLE_NAME} PRIVATE "${IMGUI_SOURCE_DIR}/backends")
			target_link_libraries(${EXAMPLE_NAME} imgui Vulkan::Vulkan)
		endif()
	endif()
endif()