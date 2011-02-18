cmake_minimum_required(VERSION 2.4)

project(OISB)

set(OISB_OIS_INCLUDE_DIR "" CACHE PATH "")

FILE(GLOB sources   src/*.cpp)
FILE(GLOB includes  include/*.h)

# general flags
add_definitions("-D_LIB -D_CRT_SECURE_NO_WARNINGS -DOISB_NONCLIENT_BUILD")

include_directories(include)
include_directories(${OISB_OIS_INCLUDE_DIR})


# platform
add_library(oisb STATIC ${sources} ${sources_win})
target_link_libraries(oisb)
