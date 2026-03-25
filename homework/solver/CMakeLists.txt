cmake_minimum_required(VERSION 3.10)
project(solver_app)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_executable(solver_app equation.cpp)

target_include_directories(solver_app PRIVATE 
    ../solver_lib
    ../formatter_lib
    ../formatter_ex
)

target_link_libraries(solver_app solver formatter_ex formatter)
