project(Liberty C)
cmake_minimum_required(VERSION 3.2.2)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_CURRENT_SOURCE_DIR}/cmake_modules)

find_package(BISON 3.0.4 REQUIRED)
find_package(FLEX 2.5.35 REQUIRED)
find_package(GPERF REQUIRED)

add_definitions(-DVERSION="2.5")
add_subdirectory(src)
