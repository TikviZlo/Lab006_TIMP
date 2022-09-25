cmake_minimum_required(VERSION 3.10) 

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)
 
project(formatter)
 
add_library(formatter STATIC formatter.cpp formatter.h)