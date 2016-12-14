# Copyright Louis Dionne 2016
# Distributed under the Boost Software License, Version 1.0.

cmake_minimum_required(VERSION 3.7)
list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR})

# TODO: Set these as interface properties when supported
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED YES)


#=============================================================================
# Setup required packages
#=============================================================================
include(ExternalProject)
ExternalProject_Add(install-Hana EXCLUDE_FROM_ALL 1
    URL https://github.com/boostorg/hana/archive/develop.zip
    TIMEOUT 120
    PREFIX "${CMAKE_CURRENT_BINARY_DIR}/dependencies/hana"
    CONFIGURE_COMMAND "" # Disable configure step
    BUILD_COMMAND ""     # Disable build step
    INSTALL_COMMAND ""   # Disable install step
    TEST_COMMAND ""      # Disable test step
    UPDATE_COMMAND ""    # Disable source work-tree update
)
ExternalProject_Get_Property(install-Hana SOURCE_DIR)
add_library(hana INTERFACE)
target_include_directories(hana INTERFACE ${SOURCE_DIR}/include)

ExternalProject_Add(install-CallableTraits EXCLUDE_FROM_ALL 1
    URL https://github.com/badair/callable_traits/archive/develop.zip
    TIMEOUT 120
    PREFIX "${CMAKE_CURRENT_BINARY_DIR}/dependencies/callable_traits"
    CONFIGURE_COMMAND "" # Disable configure step
    BUILD_COMMAND ""     # Disable build step
    INSTALL_COMMAND ""   # Disable install step
    TEST_COMMAND ""      # Disable test step
    UPDATE_COMMAND ""    # Disable source work-tree update
)
ExternalProject_Get_Property(install-CallableTraits SOURCE_DIR)
add_library(callable_traits INTERFACE)
target_include_directories(callable_traits INTERFACE ${SOURCE_DIR}/include)

ExternalProject_Add(install-te EXCLUDE_FROM_ALL 1
    URL https://github.com/ldionne/te/archive/develop.zip
    TIMEOUT 120
    PREFIX "${CMAKE_CURRENT_BINARY_DIR}/dependencies/te"
    CONFIGURE_COMMAND "" # Disable configure step
    BUILD_COMMAND ""     # Disable build step
    INSTALL_COMMAND ""   # Disable install step
    TEST_COMMAND ""      # Disable test step
    UPDATE_COMMAND ""    # Disable source work-tree update
)
ExternalProject_Get_Property(install-te SOURCE_DIR)
add_library(te INTERFACE)
target_include_directories(te INTERFACE ${SOURCE_DIR}/include)
target_link_libraries(te INTERFACE hana callable_traits)

add_custom_target(install-dependencies DEPENDS install-Hana install-CallableTraits install-te)


#=============================================================================
# Setup code samples
#=============================================================================
enable_testing()
add_custom_target(samples)

file(GLOB CODE_SAMPLES code/*.cpp)
foreach(_file IN LISTS CODE_SAMPLES)
    file(RELATIVE_PATH _target ${CMAKE_CURRENT_SOURCE_DIR}/code ${_file})
    string(REPLACE ".cpp" "" _target "${_target}")
    add_executable(sample.${_target} "${_file}")
    target_compile_options(sample.${_target} PRIVATE -O3)
    add_dependencies(samples sample.${_target})
    add_test(sample.${_target} sample.${_target})
endforeach()

add_custom_target(check ALL
    COMMAND ${CMAKE_CTEST_COMMAND} --output-on-failure
    DEPENDS samples
    COMMENT "Build all the samples and then run them.")
