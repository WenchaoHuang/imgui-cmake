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

# Set C++ standard
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

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

# Enable ImPlot extension (optional)
option(IMGUI_EXT_IMPLOT "Enable ImPlot extension" OFF)

# Search order for ImPlot:
# 1. Check if ImPlot source files are located in the parent directory under "../implot" (recommended).
# 2. If not found, check the current directory.
# 3. If neither exists, require the user to manually specify IMPLOT_SOURCE_DIR.

if(IMGUI_EXT_IMPLOT)
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

if(CMAKE_SYSTEM_NAME STREQUAL "Windows")
	option(IMGUI_BACKEND_DX9			"Enable DirectX9 backend"		OFF)
	option(IMGUI_BACKEND_DX10			"Enable DirectX10 backend"		OFF)
	option(IMGUI_BACKEND_DX11			"Enable DirectX11 backend"		OFF)
	option(IMGUI_BACKEND_DX12			"Enable DirectX12 backend"		OFF)
	option(IMGUI_BACKEND_WIN32			"Enable Win32 backend"			OFF)
endif()

if(CMAKE_SYSTEM_NAME MATCHES "Android|Linux|Darwin|Windows")
	option(IMGUI_BACKEND_VULKAN			"Enable Vulkan backend"			OFF)
	option(IMGUI_BACKEND_OPENGL3		"Enable OpenGL3 backend"		OFF)
endif()

if(CMAKE_SYSTEM_NAME MATCHES "Linux|Darwin|Windows")
	option(IMGUI_BACKEND_OPENGL2		"Enable OpenGL2 backend"		OFF)
	option(IMGUI_BACKEND_ALLEGRO5		"Enable Allegro5 backend"		OFF)
	option(IMGUI_BACKEND_GLFW			"Enable GLFW backend"			OFF)
	option(IMGUI_BACKEND_GLUT			"Enable GLUT backend"			OFF)
	option(IMGUI_BACKEND_SDL2			"Enable SDL2 backend"			OFF)
	option(IMGUI_BACKEND_SDL3			"Enable SDL3 backend"			OFF)
	option(IMGUI_BACKEND_SDLGPU3		"Enable SDLGPU3 backend"		OFF)
	option(IMGUI_BACKEND_SDLRENDERER2	"Enable SDLRenderer2 backend"	OFF)
	option(IMGUI_BACKEND_SDLRENDERER3	"Enable SDLRenderer3 backend"	OFF)
endif()

if(CMAKE_SYSTEM_NAME STREQUAL "Darwin")
	option(IMGUI_BACKEND_OSX			"Enable OSX backend"			OFF)
	option(IMGUI_BACKEND_METAL			"Enable Metal backend"			OFF)
endif()

if(CMAKE_SYSTEM_NAME MATCHES "Android")
	option(IMGUI_BACKEND_ANDROID		"Enable Android backend"		OFF)
endif()

if(CMAKE_SYSTEM_NAME STREQUAL "Web")
	option(IMGUI_BACKEND_WGPU			"Enable WebGPU backend"			OFF)
endif()

################################################################################
#############################   Collect sources    #############################
################################################################################

# Demo window (optional)
option(IMGUI_ENABLE_DEMO_WINDOW	"Enable demo window" OFF)

# Core headers
set(IMGUI_HEADERS
	${IMGUI_SOURCE_DIR}/imgui.h
	${IMGUI_SOURCE_DIR}/imconfig.h
	${IMGUI_SOURCE_DIR}/imgui_internal.h
)

# Core sources
set(IMGUI_SOURCES
	${IMGUI_SOURCE_DIR}/imgui.cpp
	${IMGUI_SOURCE_DIR}/imgui_draw.cpp
	${IMGUI_SOURCE_DIR}/imgui_tables.cpp
	${IMGUI_SOURCE_DIR}/imgui_widgets.cpp
)

# ImPlot sources
if(IMGUI_EXT_IMPLOT)
    list(APPEND IMGUI_HEADERS
        ${IMPLOT_SOURCE_DIR}/implot.h
        ${IMPLOT_SOURCE_DIR}/implot_internal.h
    )
    list(APPEND IMGUI_SOURCES
        ${IMPLOT_SOURCE_DIR}/implot.cpp
        ${IMPLOT_SOURCE_DIR}/implot_items.cpp
    )
	if(IMGUI_ENABLE_DEMO_WINDOW)
		list(APPEND IMGUI_SOURCES "${IMPLOT_SOURCE_DIR}/implot_demo.cpp")
	endif()
endif()

if(IMGUI_ENABLE_DEMO_WINDOW OR IMGUI_BUILD_EXAMPLES)
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/imgui_demo.cpp")
endif()

if(IMGUI_BACKEND_DX9)
	message(STATUS "ImGui: DirectX 9 backend enabled")
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_dx9.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_dx9.cpp")
endif()

if(IMGUI_BACKEND_DX10)
	message(STATUS "ImGui: DirectX 10 backend enabled")
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_dx10.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_dx10.cpp")
endif()

if(IMGUI_BACKEND_DX11)
	message(STATUS "ImGui: DirectX 11 backend enabled")
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_dx11.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_dx11.cpp")
endif()

if(IMGUI_BACKEND_DX12)
	message(STATUS "ImGui: DirectX 12 backend enabled")
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_dx12.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_dx12.cpp")
endif()

if(IMGUI_BACKEND_WGPU)
	message(STATUS "ImGui: Web GPU backend enabled")
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_wgpu.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_wgpu.cpp")
endif()

if(IMGUI_BACKEND_METAL)
	message(STATUS "ImGui: Metal backend enabled")
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_metal.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_metal.mm")
endif()

if(IMGUI_BACKEND_ANDROID)
	message(STATUS "ImGui: Android backend enabled")
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_android.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_android.cpp")
endif()

if(IMGUI_BACKEND_VULKAN)
	message(STATUS "ImGui: Vulkan backend enabled")
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_vulkan.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_vulkan.cpp")
endif()

if(IMGUI_BACKEND_OPENGL2)
	message(STATUS "ImGui: OpenGL2 backend enabled")
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_opengl2.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_opengl2.cpp")
endif()

if(IMGUI_BACKEND_OPENGL3)
	message(STATUS "ImGui: OpenGL3 backend enabled")
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_opengl3.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_opengl3.cpp")
endif()

if(IMGUI_BACKEND_ALLEGRO5)
	message(STATUS "ImGui: Allegro5 backend enabled")
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_allegro5.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_allegro5.cpp")
endif()

if(IMGUI_BACKEND_OSX)
	message(STATUS "ImGui: OSX backend enabled")
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_osx.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_osx.mm")
endif()

if(IMGUI_BACKEND_GLFW)
	message(STATUS "ImGui: GLFW backend enabled")
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_glfw.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_glfw.cpp")
endif()

if(IMGUI_BACKEND_GLUT)
	message(STATUS "ImGui: GLUT backend enabled")
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_glut.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_glut.cpp")
endif()

if(IMGUI_BACKEND_WIN32)
	message(STATUS "ImGui: Win32 backend enabled")
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_win32.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_win32.cpp")
endif()

if(IMGUI_BACKEND_SDL2)
	message(STATUS "ImGui: SDL2 backend enabled")
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_sdl2.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_sdl2.cpp")
endif()

if(IMGUI_BACKEND_SDL3)
	message(STATUS "ImGui: SDL3 backend enabled")
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_sdl3.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_sdl3.cpp")
endif()

if(IMGUI_BACKEND_SDLGPU3)
	message(STATUS "ImGui: SDLGPU3 backend enabled")
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_sdlgpu3.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_sdlgpu3.cpp")
endif()

if(IMGUI_BACKEND_SDLRENDERER2)
	message(STATUS "ImGui: SDLRenderer2 backend enabled")
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_sdlrenderer2.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_sdlrenderer2.cpp")
endif()

if(IMGUI_BACKEND_SDLRENDERER3)
	message(STATUS "ImGui: SDLRenderer3 backend enabled")
	list(APPEND IMGUI_HEADERS "${IMGUI_SOURCE_DIR}/backends/imgui_impl_sdlrenderer3.h")
	list(APPEND IMGUI_SOURCES "${IMGUI_SOURCE_DIR}/backends/imgui_impl_sdlrenderer3.cpp")
endif()

################################################################################
#############################   Create library    ##############################
################################################################################

# Create the static library
add_library(${TARGET_NAME} STATIC ${IMGUI_HEADERS} ${IMGUI_SOURCES})
add_library(ImGui::ImGui ALIAS ${TARGET_NAME})

# Include directories
target_include_directories(${TARGET_NAME} PUBLIC ${IMGUI_SOURCE_DIR})

# ImPlot directory
if(IMGUI_EXT_IMPLOT)
	target_include_directories(${TARGET_NAME} PUBLIC ${IMPLOT_SOURCE_DIR})
endif()

# OpenGL library
if(IMGUI_BACKEND_OPENGL2 OR IMGUI_BACKEND_OPENGL3)
	find_package(OpenGL REQUIRED)
	target_link_libraries(${TARGET_NAME} PRIVATE OpenGL::GL)
endif()

# Vulkan library
if(IMGUI_BACKEND_VULKAN)
	find_package(Vulkan REQUIRED)
	target_link_libraries(${TARGET_NAME} PRIVATE Vulkan::Vulkan)
endif()

# GLFW include
if(IMGUI_BACKEND_GLFW)
	# GLFW already added by parent object
	if(TARGET glfw)
		target_link_libraries(${TARGET_NAME} PRIVATE glfw)
	else()
		set(IMGUI_GLFW_INCLUDE "GLFW_INCLUDE_NOT_FOUND" CACHE PATH "Path to the GLFW include directory")
		if(NOT EXISTS "${IMGUI_GLFW_INCLUDE}/GLFW/glfw3.h")
			message(FATAL_ERROR "GLFW not found. Please set IMGUI_GLFW_INCLUDE to a valid include directory.")
		else()
			target_include_directories(${TARGET_NAME} PRIVATE "${IMGUI_GLFW_INCLUDE}")
		endif()
	endif()
endif()

# GLUT include
if(IMGUI_BACKEND_GLUT)
	set(IMGUI_GLUT_INCLUDE "GLUT_INCLUDE_NOT_FOUND" CACHE PATH "Path to the freeglut include directory")
	if(NOT EXISTS "${IMGUI_GLUT_INCLUDE}/GL/freeglut.h")
		message(FATAL_ERROR "GLUT not found. Please set IMGUI_GLUT_INCLUDE to a valid include directory.")
	else()
		target_include_directories(${TARGET_NAME} PRIVATE "${IMGUI_GLUT_INCLUDE}")
	endif()
endif()

# SDL2 include
if(IMGUI_BACKEND_SDL2 OR IMGUI_BACKEND_SDLRENDERER2)
	set(IMGUI_SDL2_INCLUDE "SDL2_INCLUDE_NOT_FOUND" CACHE PATH "Path to the SDL2 include directory")
	if(NOT EXISTS "${IMGUI_SDL2_INCLUDE}/SDL.h")
		message(FATAL_ERROR "SDL2 not found. Please set IMGUI_SDL2_INCLUDE to a valid include directory.")
	else()
		target_include_directories(${TARGET_NAME} PRIVATE "${IMGUI_SDL2_INCLUDE}")
	endif()
endif()

# SDL3 include
if(IMGUI_BACKEND_SDL3 OR IMGUI_BACKEND_SDLGPU3 OR IMGUI_BACKEND_SDLRENDERER3)
	set(IMGUI_SDL3_INCLUDE "SDL3_INCLUDE_NOT_FOUND" CACHE PATH "Path to the SDL3 include directory")
	if(NOT EXISTS "${IMGUI_SDL3_INCLUDE}/SDL3/SDL.h")
		message(FATAL_ERROR "SDL3 not found. Please set IMGUI_SDL3_INCLUDE to a valid include directory.")
	else()
		target_include_directories(${TARGET_NAME} PRIVATE "${IMGUI_SDL3_INCLUDE}")
	endif()
endif()

# Allegro5 include
if(IMGUI_BACKEND_ALLEGRO5)
	set(IMGUI_ALLEGRO5_INCLUDE "Allegro5_INCLUDE_NOT_FOUND" CACHE PATH "Path to the Allegro5 include directory")
	if(NOT EXISTS "${IMGUI_ALLEGRO5_INCLUDE}/allegro5/allegro.h")
		message(FATAL_ERROR "Allegro5 not found. Please set IMGUI_ALLEGRO5_INCLUDE to a valid include directory.")
	else()
		target_include_directories(${TARGET_NAME} PRIVATE "${IMGUI_ALLEGRO5_INCLUDE}")
	endif()
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
	# No backend
	set(EXAMPLE_NAME example_null)
	add_executable(${EXAMPLE_NAME} "${IMGUI_SOURCE_DIR}/examples/${EXAMPLE_NAME}/main.cpp")
	target_include_directories(${EXAMPLE_NAME} PRIVATE "${IMGUI_SOURCE_DIR}/backends")
	target_link_libraries(${EXAMPLE_NAME} ImGui::ImGui)

	# Win32 backend
	if(IMGUI_BACKEND_WIN32)
		# Win32 + DX9
		if(IMGUI_BACKEND_DX9)
			set(EXAMPLE_NAME example_win32_directx9)
			add_executable(${EXAMPLE_NAME} "${IMGUI_SOURCE_DIR}/examples/${EXAMPLE_NAME}/main.cpp")
			target_include_directories(${EXAMPLE_NAME} PRIVATE "${IMGUI_SOURCE_DIR}/backends")
			target_link_libraries(${EXAMPLE_NAME} ImGui::ImGui d3d9)
		endif()

		# Win32 + DX10
		if(IMGUI_BACKEND_DX10)
			set(EXAMPLE_NAME example_win32_directx10)
			add_executable(${EXAMPLE_NAME} "${IMGUI_SOURCE_DIR}/examples/${EXAMPLE_NAME}/main.cpp")
			target_include_directories(${EXAMPLE_NAME} PRIVATE "${IMGUI_SOURCE_DIR}/backends")
			target_link_libraries(${EXAMPLE_NAME} ImGui::ImGui d3d10)
		endif()

		# Win32 + DX11
		if(IMGUI_BACKEND_DX11)
			set(EXAMPLE_NAME example_win32_directx11)
			add_executable(${EXAMPLE_NAME} "${IMGUI_SOURCE_DIR}/examples/${EXAMPLE_NAME}/main.cpp")
			target_include_directories(${EXAMPLE_NAME} PRIVATE "${IMGUI_SOURCE_DIR}/backends")
			target_link_libraries(${EXAMPLE_NAME} ImGui::ImGui d3d11)
		endif()

		# Win32 + DX12
		if(IMGUI_BACKEND_DX12)
			set(EXAMPLE_NAME example_win32_directx12)
			add_executable(${EXAMPLE_NAME} "${IMGUI_SOURCE_DIR}/examples/${EXAMPLE_NAME}/main.cpp")
			target_include_directories(${EXAMPLE_NAME} PRIVATE "${IMGUI_SOURCE_DIR}/backends")
			target_link_libraries(${EXAMPLE_NAME} ImGui::ImGui d3d12 dxgi)
		endif()

		# Win32 + Vulkan 
		if(IMGUI_BACKEND_VULKAN)
			set(EXAMPLE_NAME example_win32_vulkan)
			add_executable(${EXAMPLE_NAME} "${IMGUI_SOURCE_DIR}/examples/${EXAMPLE_NAME}/main.cpp")
			target_include_directories(${EXAMPLE_NAME} PRIVATE "${IMGUI_SOURCE_DIR}/backends")
			target_link_libraries(${EXAMPLE_NAME} ImGui::ImGui Vulkan::Vulkan)
		endif()
	endif()

	# GLFW backend
	if(IMGUI_BACKEND_GLFW AND TARGET glfw)
		# GLFW + OpenGL2
		if(IMGUI_BACKEND_OPENGL2)
			set(EXAMPLE_NAME example_glfw_opengl2)
			add_executable(${EXAMPLE_NAME} "${IMGUI_SOURCE_DIR}/examples/${EXAMPLE_NAME}/main.cpp")
			target_include_directories(${EXAMPLE_NAME} PRIVATE "${IMGUI_SOURCE_DIR}/backends")
			target_link_libraries(${EXAMPLE_NAME} ImGui::ImGui glfw ${OPENGL_LIBRARIES})
		endif()

		# GLFW + OpenGL3
		if(IMGUI_BACKEND_OPENGL3)
			set(EXAMPLE_NAME example_glfw_opengl3)
			add_executable(${EXAMPLE_NAME} "${IMGUI_SOURCE_DIR}/examples/${EXAMPLE_NAME}/main.cpp")
			target_include_directories(${EXAMPLE_NAME} PRIVATE "${IMGUI_SOURCE_DIR}/backends")
			target_link_libraries(${EXAMPLE_NAME} ImGui::ImGui glfw ${OPENGL_LIBRARIES})
		endif()

		# GLFW + Vulkan
		if(IMGUI_BACKEND_VULKAN)
			set(EXAMPLE_NAME example_glfw_vulkan)
			add_executable(${EXAMPLE_NAME} "${IMGUI_SOURCE_DIR}/examples/${EXAMPLE_NAME}/main.cpp")
			target_include_directories(${EXAMPLE_NAME} PRIVATE "${IMGUI_SOURCE_DIR}/backends")
			target_link_libraries(${EXAMPLE_NAME} ImGui::ImGui glfw Vulkan::Vulkan)
		endif()

		# GLFW + Metal
		if(IMGUI_BACKEND_METAL)
			set(EXAMPLE_NAME example_glfw_metal)
			find_library(METAL_FRAMEWORK Metal REQUIRED)
			find_library(APPKIT_FRAMEWORK AppKit REQUIRED)
			find_library(METALKIT_FRAMEWORK MetalKit REQUIRED)
			find_library(GAMECONTROLLER_FRAMEWORK GameController REQUIRED)
			add_executable(${EXAMPLE_NAME} "${IMGUI_SOURCE_DIR}/examples/${EXAMPLE_NAME}/main.mm")
			target_include_directories(${EXAMPLE_NAME} PRIVATE "${IMGUI_SOURCE_DIR}/backends")
			target_link_libraries(${EXAMPLE_NAME} ImGui::ImGui glfw ${APPKIT_FRAMEWORK} ${METAL_FRAMEWORK} ${METALKIT_FRAMEWORK} ${GAMECONTROLLER_FRAMEWORK})
		endif()
	endif()
	
	# OSX + Metal
	if(IMGUI_BACKEND_OSX AND IMGUI_BACKEND_METAL)
		set(EXAMPLE_NAME example_apple_metal)
		find_library(METAL_FRAMEWORK Metal REQUIRED)
		find_library(APPKIT_FRAMEWORK AppKit REQUIRED)
		find_library(METALKIT_FRAMEWORK MetalKit REQUIRED)
		find_library(GAMECONTROLLER_FRAMEWORK GameController REQUIRED)
		add_executable(${EXAMPLE_NAME} "${IMGUI_SOURCE_DIR}/examples/${EXAMPLE_NAME}/main.mm")
		target_include_directories(${EXAMPLE_NAME} PRIVATE "${IMGUI_SOURCE_DIR}/backends")
		target_link_libraries(${EXAMPLE_NAME} ImGui::ImGui ${APPKIT_FRAMEWORK} ${METAL_FRAMEWORK} ${METALKIT_FRAMEWORK} ${GAMECONTROLLER_FRAMEWORK})
	endif()
endif()