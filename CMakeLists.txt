cmake_minimum_required(VERSION 3.14 FATAL_ERROR)

set(CMAKE_C_COMPILER clang)
# set (CMAKE_C_FLAGS "-m32")
set(CMAKE_C_STANDARD 99)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Build type.
if(NOT CMAKE_BUILD_TYPE)
  message(STATUS "CMAKE_BUILD_TYPE not specified, default is 'Debug'")
  set(CMAKE_BUILD_TYPE "Debug" CACHE STRING "Choose the type of build" FORCE)
else()
  message(STATUS "CMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}")
endif()
if(CMAKE_BUILD_TYPE MATCHES Debug)
  set(DEBUG 1)
else()
  set(DEBUG 0)
endif()

project(dwm-win32
  VERSION 0.0.50
  DESCRIPTION "Dynamic Window Manager for Win32"
  LANGUAGES C)

add_executable(${PROJECT_NAME} WIN32
  src/dwm-win32.c)

target_link_libraries(${PROJECT_NAME} lua)

configure_file(
  "${PROJECT_SOURCE_DIR}/config.h.in"
  "${PROJECT_SOURCE_DIR}/src/config.h"
)

if (NOT MSVC)
  target_compile_options(${PROJECT_NAME} PRIVATE
    -Wall
    -Wextra
    # -Werror
    -Wpedantic
  )
endif()

set(lua_SOURCE_DIR "${PROJECT_SOURCE_DIR}/extern/lua/src")
FILE(GLOB lua_sources ${lua_SOURCE_DIR}/*.c)
list(REMOVE_ITEM lua_sources "${lua_SOURCE_DIR}/lua.c" "${lua_SOURCE_DIR}/luac.c")
add_library(lua STATIC ${lua_sources})

target_include_directories(lua
  PUBLIC
  $<BUILD_INTERFACE:${lua_SOURCE_DIR}>
)