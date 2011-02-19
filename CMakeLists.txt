cmake_minimum_required(VERSION 2.4)

project(OISB)

set(OISB_OIS_INCLUDE_DIR "" CACHE PATH "")
set(OISB_OIS_LIB "" CACHE PATH "")

FILE(GLOB sources   src/*.cpp)
FILE(GLOB includes  include/*.h)

# general flags
add_definitions(-D_LIB -D_CRT_SECURE_NO_WARNINGS -DOISB_STATIC_LIB)

include_directories(include)
include_directories(${OISB_OIS_INCLUDE_DIR})

# oisb library
add_library(oisb STATIC ${sources} ${sources_win})

# oisb simple demo
include_directories(demo/simple)
add_executable(oisb_console demos/simple/OISBConsole.cpp demos/simple/CommandLine.rc)
target_link_libraries(oisb_console oisb ${OISB_OIS_LIB})
