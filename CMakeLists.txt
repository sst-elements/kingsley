cmake_minimum_required(VERSION 3.12)
project(kingsley CXX)
include(../CMakeLists.txt)

# add compiler warnings
add_compile_options(-Wall -Wextra -Wpedantic)

add_executable(
    libkingsley.so
    linkControl.cc
    noc_mesh.cc
    linkControl.h
    lru_unit.h
    nocEvents.h
    noc_mesh.h
)

_sst_compile_link(libkingsley.so)