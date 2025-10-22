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

# Search order:
# 1. Check if ImGui source files are located in the current directory.
# 2. If not found, check the parent directory under "../imgui".
# 3. If neither exists, require the user to manually specify IMGUI_SOURCE_DIR.

if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/imgui.h")
	set(IMGUI_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}" CACHE PATH "Path to the ImGui source directory")
elseif(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/../imgui/imgui.h")
	set(IMGUI_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../imgui" CACHE PATH "Path to the ImGui source directory")
else()
	set(IMGUI_SOURCE_DIR "" CACHE PATH "Path to the ImGui source directory")
endif()

if(NOT EXISTS "${IMGUI_SOURCE_DIR}/imgui.h")
	message(FATAL_ERROR "Invalid IMGUI_SOURCE_DIR: ${IMGUI_SOURCE_DIR}. Please set it to a valid ImGui source directory.")
endif()

message(STATUS "ImGui source directory: ${IMGUI_SOURCE_DIR}")

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

################################################################################
#############################   Create library    ##############################
################################################################################

# Create the static library
add_library(${TARGET_NAME} STATIC ${IMGUI_HEADERS} ${IMGUI_SOURCES})

# Public include directory
target_include_directories(${TARGET_NAME} PUBLIC ${IMGUI_SOURCE_DIR})