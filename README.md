[![CPack_action](https://github.com/TikviZlo/Lab006_TIMP/actions/workflows/actions.yml/badge.svg)](https://github.com/TikviZlo/Lab006_TIMP/actions/workflows/actions.yml)

## 1. Клонируем репозиторий из Лабораторной работы №3 и убираем всё ненужное.

## 2. Немного корректируем equations.cpp, чтобы работали все остальные файлы:

```
#include <iostream>

#include "formatter_ex.h"
#include "solver.h"

int main()
{
    float a = 0;
    float b = 0;
    float c = 0;

    std::cin >> a >> b >> c;

    float x1 = 0;
    float x2 = 0;

    try
    {
        solve(a, b, c, x1, x2);

        formatter(std::cout, "x1 = " + std::to_string(x1));
        formatter(std::cout, "x2 = " + std::to_string(x2));
    }
    catch (const std::logic_error& ex)
    {
        formatter(std::cout, ex.what());
    }

    return 0;
}
```

## 3. Переписываем CMakeLists в solver_application под данную лабораторную работу:

```
cmake_minimum_required(VERSION 3.10)

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
```

## 4. Пишем CPackConfig.cmake:

```
install(TARGETS solver_app)
include(InstallRequiredSystemLibraries)

set(CPACK_PACKAGE_VERSION_MAJOR \${PRINT_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR \${PRINT_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH \${PRINT_VERSION_PATCH})
set(CPACK_PACKAGE_VERSION_TWEAK \${PRINT_VERSION_TWEAK})
set(CPACK_PACKAGE_VERSION \${PRINT_VERSION})

set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "static C++ library for printing")

set(CPACK_RESOURCE_FILE_README \${CMAKE_CURRENT_SOURCE_DIR}/../README.md)

set(CPACK_RPM_PACKAGE_NAME "solver_app")
set(CPACK_RPM_PACKAGE_GROUP "solver")
set(CPACK_RPM_PACKAGE_VERSION 1.0.0)
set(CPACK_RPM_PACKAGE_RELEASE 1)

set(CPACK_DEBIAN_PACKAGE_NAME "libsolver_app-dev")
set(CPACK_DEBIAN_PACKAGE_PREDEPENDS "cmake >= 3.10")
set(CPACK_DEBIAN_PACKAGE_VERSION 1.0.0)
set(CPACK_DEBIAN_PACKAGE_MAINTAINER "TikviZlo")
set(CPACK_DEBIAN_PACKAGE_RELEASE 1)

set(CPACK_ARCHIVE_FILE_NAME "solver_app")

include(CPack)
```

## 5. И, наконец, пишем actions.yml для этой лабораторной:

```name: CPack_action

on:
  push:
    branches: [ master ]
  pull_request:
    branches: [ master ]

jobs:
  Build:
    runs-on: ubuntu-latest
    
    steps:
    - name: checkout
      uses: actions/checkout@v3
      
    - name: Creating directory for future binary and source code
      run: |
        mkdir artifacts
    
    - name: Build a *solver* application
      run: |
        cmake -H. -B_build
        cmake --build _build --target package
        cd _build
        cpack -G "TGZ"
        cpack -G "DEB"
        cpack -G "RPM"
        cpack -G "ZIP"
        mv *.tar.gz ../../artifacts
        mv *.deb ../../artifacts
        mv *.rpm ../../artifacts
        mv *.zip ../../artifacts
      shell: bash
      working-directory: solver_application

    - name: Upload_artifacts
      uses: actions/upload-artifact@v3
      with:
        name: artifact
        path: artifacts

```
