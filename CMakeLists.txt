# CMake 프로그램의 최소 버전
cmake_minimum_required(VERSION 3.11)

# 프로젝트 정보
project(
  claujson_cmake_test
  VERSION 0.0.1
  DESCRIPTION "claujson_cmake_test"
  LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17) # C++17
set(CMAKE_BUILD_TYPE "Release")

set(PROGRAM_NAME claujson_cmake_test)

set(ROOT .)

set(LIB_DIR ${ROOT})

set(SRC_DIR ${ROOT})

set(INCLUDE_DIR ${ROOT})

# build setting.
add_executable(${PROGRAM_NAME} ${SRC_DIR}/main.cpp)

include(FetchContent)
FetchContent_Declare(
  claujson
  GIT_REPOSITORY "https://github.com/vztpv/scj3"
  GIT_TAG "0.11.12"
  )
FetchContent_MakeAvailable(claujson)

include(FetchContent)
FetchContent_Declare(
  Mimalloc
  GIT_REPOSITORY "https://github.com/microsoft/mimalloc"
  GIT_TAG "v2.0.9"
  )
FetchContent_MakeAvailable(Mimalloc)


# header..
target_include_directories(${PROGRAM_NAME} PRIVATE ${INCLUDE_DIR})

if (UNIX) 
	target_compile_options(${PROGRAM_NAME} PRIVATE -m64 -march=native -pthread  -Wno-narrowing  -O2)
elseif (MSVC)
	target_compile_definitions(${PROGRAM_NAME} PRIVATE
        "$<$<CONFIG:Release>:"
            "NDEBUG"
        ">"
        "_CONSOLE;"
        "UNICODE;"
        "_UNICODE"
    )
	
	if("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x64")
        target_compile_options(${PROGRAM_NAME} PRIVATE
            $<$<CONFIG:Release>:
                /std:c++17;
                /Oi;
                /Gy
            >
            /permissive-;
            /sdl;
            /W3;
            ${DEFAULT_CXX_DEBUG_INFORMATION_FORMAT};
            ${DEFAULT_CXX_EXCEPTION_HANDLING}
        )
    endif()
    if("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x64")
        target_link_options(${PROGRAM_NAME} PRIVATE
            $<$<CONFIG:Release>:
                /OPT:REF;
                /OPT:ICF
            >
            /DEBUG;
            /SUBSYSTEM:CONSOLE
        )
    endif()
endif()


if (UNIX)
	target_link_libraries(${PROGRAM_NAME} PRIVATE claujson mimalloc pthread)	
  
elseif (MSVC)
	target_link_libraries(${PROGRAM_NAME} PRIVATE claujson mimalloc-static)
	
endif()
