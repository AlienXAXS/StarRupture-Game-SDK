cmake_minimum_required(VERSION 3.21)
project(SDKBuildTest LANGUAGES CXX)

if(NOT DEFINED SDK_ROOT)
    message(FATAL_ERROR "SDK_ROOT must point to the generated SDK directory")
endif()

file(TO_CMAKE_PATH "${SDK_ROOT}" SDK_ROOT)

foreach(SDK_SOURCE
    "${SDK_ROOT}/SDK/Basic.cpp"
    "${SDK_ROOT}/SDK/Engine_functions.cpp"
    "${SDK_ROOT}/SDK/CoreUObject_functions.cpp"
)
    if(NOT EXISTS "${SDK_SOURCE}")
        message(FATAL_ERROR "Required source file not found: ${SDK_SOURCE}")
    endif()
endforeach()

add_library(SDKBuildTest SHARED
    TestMain.cpp
    "${SDK_ROOT}/SDK/Basic.cpp"
    "${SDK_ROOT}/SDK/Engine_functions.cpp"
    "${SDK_ROOT}/SDK/CoreUObject_functions.cpp"
)

target_include_directories(SDKBuildTest PRIVATE "${SDK_ROOT}")
target_compile_features(SDKBuildTest PRIVATE cxx_std_23)
set_target_properties(SDKBuildTest PROPERTIES
    CXX_STANDARD 23
    CXX_STANDARD_REQUIRED YES
    CXX_EXTENSIONS NO
)

if(CMAKE_CXX_COMPILER_ID MATCHES "Clang")
    target_compile_options(SDKBuildTest PRIVATE
        -Wno-invalid-offsetof
        -Wno-c++11-narrowing
    )
endif()
