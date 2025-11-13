cmake_minimum_required(VERSION 3.31)
project(si5351-cpp)
set(CMAKE_CXX_STANDARD 17)
enable_testing()

set(SI5351_LIB_DIR ${CMAKE_CURRENT_SOURCE_DIR})
set(SI5351_LIB_SOURCE_DIR ${SI5351_LIB_DIR}/src)

file(GLOB_RECURSE SI5351_FILES ${SI5351_LIB_SOURCE_DIR}/*.cpp)
message(STATUS "SI5351 source dir: ${SI5351_LIB_SOURCE_DIR}")
message(STATUS "SI5351_FILES found: ${SI5351_FILES}")

add_library(si5351-cpp STATIC ${SI5351_FILES})

target_include_directories(si5351-cpp PUBLIC
  ${SI5351_LIB_SOURCE_DIR}
  ${FLUX_CPP}/src
)
target_link_libraries(si5351-cpp PRIVATE flux-cpp)

if(ENABLE_COVERAGE AND (CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CX_COMPILER_ID MATCHES "Clang"))
  target_compile_options(si5351-cpp PRIVATE --coverage -O0 -g)
  target_link_options(si5351-cpp PRIVATE --coverage)
endif()