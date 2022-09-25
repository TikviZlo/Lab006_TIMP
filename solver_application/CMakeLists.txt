cmake_minimum_required(VERSION 3.10)

option(BUILD_TESTS "Build tests" OFF)
option(COVERALLS "Check coverage" OFF)

project(solver_app)

include_directories("../formatter_lib")
include_directories("../formatter_ex_lib")
include_directories("../solver_lib")

add_library(formatter STATIC "../formatter_lib/formatter.cpp")
add_library(formatter_ex STATIC "../formatter_ex_lib/formatter_ex.cpp")
add_library(solver STATIC "../solver_lib/solver.cpp")

add_executable(solver_app "../solver_application/equation.cpp")

target_link_libraries(solver_app formatter_ex formatter solver)

include("../CPackConfig.cmake")

if (COVERALLS)
    SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fprofile-arcs -ftest-coverage")
    SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fprofile-arcs -ftest-coverage")
    SET(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -fprofile-arcs -ftest-coverage")
endif ()
