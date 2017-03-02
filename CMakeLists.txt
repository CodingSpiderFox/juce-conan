#The MIT License (MIT)
#
#Copyright (c) 2015 Teragon Audio
#Copyright (c) 2017 CodingSpiderFox
#
#Permission is hereby granted, free of charge, to any person obtaining a copy
#of this software and associated documentation files (the "Software"), to deal
#in the Software without restriction, including without limitation the rights
#to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
#copies of the Software, and to permit persons to whom the Software is
#furnished to do so, subject to the following conditions:
#
#The above copyright notice and this permission notice shall be included in all
#copies or substantial portions of the Software.
#
#THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
#IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
#FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
#AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
#LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
#OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
#SOFTWARE.

cmake_minimum_required(VERSION 2.8)
project(JUCE)


# TODO: Document and split off to submodule

# Set the output destination
set(plugin_BUILDS_DIR ${PROJECT_SOURCE_DIR}/Builds)

# TODO: Add support for Windows
if(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
  set(plugin_BUILD_DIR ${plugin_BUILDS_DIR}/MacOSX)
  set(plugin_OUTPUT ${plugin_BUILDS_DIR}/build/${CMAKE_PROJECT_NAME}.vst)
  set(plugin_BUILDER xcodebuild)
  set(CMAKE_SKIP_BUILD_RPATH TRUE)
elseif(${CMAKE_SYSTEM_NAME} MATCHES "Linux")
  set(plugin_BUILD_DIR ${plugin_BUILDS_DIR}/Linux)
  set(plugin_OUTPUT ${plugin_BUILDS_DIR}/build/${CMAKE_PROJECT_NAME}.so)
  set(plugin_BUILDER make -j8)
endif()

# Create a custom target that will build the generated project from Introjucer
set(main_TARGET ${CMAKE_PROJECT_NAME})
add_custom_target(${main_TARGET} ALL
                  COMMAND ${plugin_BUILDER}
                  WORKING_DIRECTORY
                  ${plugin_BUILD_DIR}
                  COMMENT "Running external builder")

# Paths to Juce sources
set(juce_SOURCE_DIR ${PROJECT_SOURCE_DIR}/JUCE/modules)


# Also glob the Juce sources so they are visible in the IDE and we get
# help with auto-completion etc.
file(GLOB_RECURSE juce_SOURCES
     RELATIVE
     ${CMAKE_CURRENT_SOURCE_DIR}
     "${juce_SOURCE_DIR}/*.h"
	 "${juce_SOURCE_DIR}/*.cpp"
     "${juce_SOURCE_DIR}/*.mm"	 
)

# Help some IDEs with #include completion
include_directories(${juce_SOURCE_DIR}/**)


add_library(jucelib_consumer SHARED
            ${juce_SOURCES}
)