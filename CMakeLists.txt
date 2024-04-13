cmake_minimum_required(VERSION 3.20)  # 所需要的cmake最低版本

set(PROJECT_NAME, "BuriedPoint")  # 即让 PROJECT_NAME = "BuriedPoint"

project(${PROJECT_NAME}VERSION1.1.1.1)  # 在根目录的CMakeLists需要有这个，定义项目名称和版本号   ！！！注意：project()不能有空格

option(BUILD_BURIED_SHARED_LIBS "build shared library" ON)  # 添加一个构建选项，用于指定是否构建动态库，默认为开启状态

option(BUILD_BURIED_STATIC_LIBS "build static library" ON)  # 添加一个构建选项，用于指定是否构建静态库，默认为开启状态

option(BUILD_BURIED_EXAMPLES "build examples" OFF) # 添加一个构建选项，用于指定是否构建示例，默认为关闭状态

option(BUILD_BURIED_TEST "build unittest" OFF)  # 添加一个构建选项，用于指定是否构建单元测试，默认为关闭状态

option(BUILD_BURIED_FOR_MT "build for /MT" OFF) # 添加一个构建选项，用于指定是否为 /MT 模式构建，默认为关闭状态

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /MP") # 设置 C++ 编译器的标志，这里是启用多核编译。
# """将 /MP 这个多核编译选项添加到 C++ 编译器的原有选项中，从而在使用 Visual Studio 编译器时启用多核编译，提高项目的编译速度"""

if(BUILD_BURIED_FOR_MT)
    set(CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG} /MTd")
    set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /MTd")
    set(CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE} /MT")
    set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /MT")
endif()

set(LIBS ${LIBS} winmm iphlpapi ws2_32 dbghelp)

# generate pdb file
set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /Zi")
set(CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE} /Zi")
set(CMAKE_SHARED_LINKER_FLAGS_RELEASE
    "${CMAKE_EXE_LINKER_FLAGS_RELEASE} /DEBUG /OPT:REF /OPT:ICF")

set(INCDIRS . lib ${CMAKE_BINARY_DIR})
include_directories(${INCDIRS})

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /std:c++20")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} /std:c11")

message(STATUS "CMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}")
message(STATUS "CMAKE_SYSTEM_NAME=${CMAKE_SYSTEM_NAME}")

if("${CMAKE_BUILD_TYPE}" STREQUAL "Debug")
    add_definitions(-DDEBUG)
else()
    add_definitions(-DNDEBUG)
endif()

add_subdirectory(src)

if(BUILD_BURIED_EXAMPLES)
    add_subdirectory(examples)
endif()

# googletest
if(BUILD_BURIED_TEST)
    include_directories(
        googletest/googletest
        googletest/googletest/include)
    add_subdirectory(googletest)
    add_subdirectory(tests)
endif()
