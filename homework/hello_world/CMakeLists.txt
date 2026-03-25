cmake_minimum_required(VERSION 3.10)
project(hello_world)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_executable(hello_world hello_world.cpp)

target_include_directories(hello_world PRIVATE 
    ../formatter_lib
    ../formatter_ex
)

target_link_libraries(hello_world formatter_ex formatter)
