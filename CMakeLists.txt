cmake_minimum_required(VERSION 3.15)

project(cpplr LANGUAGES CXX VERSION 0.0.1)

option(CPPLR_WALL                                 OFF)
option(CPPLR_WERROR                               OFF)
option(CPPLR_ENABLE_DETAILED_CONCEPTS_DIAGNOSTICS OFF)
option(CPPLR_ENABLE_TESTING                       OFF)
option(CPPLR_ENABLE_STATIC_STDLIB                 OFF)

if(DEFINED CPPLR_STDLIB AND NOT CPPLR_STDLIB STREQUAL "default")
    if(CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -stdlib=${CPPLR_STDLIB}")
    else()
        message(FATAL_ERROR "CPPLR_STDLIB must be \"default\" unless using Clang")
    endif()
endif()

if(DEFINED CPPLR_STDLIB_PATH AND EXISTS ${CPPLR_STDLIB_PATH})
    if(CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
        set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -L ${CPPLR_STDLIB_PATH}")
        set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -rpath ${CPPLR_STDLIB_PATH}")
    elseif(CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wl,-rpath -Wl,${CPPLR_STDLIB_PATH}")
    else()
        message(SEND_ERROR  "option   : CPPLR_STDLIB_PATH")
        message(SEND_ERROR  "compiler : ${CMAKE_CXX_COMPILER_ID}")
        message(FATAL_ERROR "setting the Standard Library link path on this compiler unsupported")
    endif()
endif()

if(DEFINED CPPLR_STDLIB_INCLUDE AND EXISTS ${CPPLR_STDLIB_INCLUDE})
    if(
        CMAKE_CXX_COMPILER_ID STREQUAL "Clang" OR
        CMAKE_CXX_COMPILER_ID STREQUAL "GNU"
    )
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -isystem ${CPPLR_STDLIB_INCLUDE}")
    else()
        message(SEND_ERROR  "option   : CPPLR_STDLIB_INCLUDE")
        message(SEND_ERROR  "compiler : ${CMAKE_CXX_COMPILER_ID}")
        message(FATAL_ERROR "setting the Standard Library link path on this compiler unsupported")
    endif()
endif()

if(CPPLR_ENABLE_STATIC_STDLIB)
    if(CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -static")
    else()
        message(FATAL_ERROR "CPPLR_ENABLE_STATIC_STDLIB must be \"OFF\" unless using Clang")
    endif()
endif()

# handle enable-all-warnings
if(CPPLR_ENABLE_WALL)
    if(
        CMAKE_CXX_COMPILER_ID STREQUAL "Clang" OR
        CMAKE_CXX_COMPILER_ID STREQUAL "GNU"
    )
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra")
    else()
        message(SEND_ERROR  "option   : CPPLR_ENABLE_WALL")
        message(SEND_ERROR  "compiler : ${CMAKE_CXX_COMPILER_ID}")
        message(FATAL_ERROR "enabling all warning on this compiler unsupported")
    endif()
endif()

# handle treat-warnings-as-errors
if(CPPLR_ENABLE_WERROR)
    if(
        CMAKE_CXX_COMPILER_ID STREQUAL "Clang" OR
        CMAKE_CXX_COMPILER_ID STREQUAL "GNU"
    )
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Werror")
    else()
        message(SEND_ERROR  "option   : CPPLR_ENABLE_WERROR")
        message(SEND_ERROR  "compiler : ${CMAKE_CXX_COMPILER_ID}")
        message(FATAL_ERROR "warnings as errors on this compiler unsupported")
    endif()
endif()

# handle detailed-concepts-diagnostics
if(CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    if(CPPLR_ENABLE_DETAILED_CONCEPTS_DIAGNOSTICS)
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fconcepts-diagnostics-depth=9")
    endif()
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
    if(CPPLR_ENABLE_DETAILED_CONCEPTS_DIAGNOSTICS)
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -ftemplate-backtrace-limit=0")
    endif()
else()
    if(CPPLR_ENABLE_DETAILED_CONCEPTS_DIAGNOSTICS)
        message(SEND_ERROR  "option   : CPPLR_ENABLE_DETAILED_CONCEPTS_DIAGNOSTICS")
        message(SEND_ERROR  "compiler : ${CMAKE_CXX_COMPILER_ID}")
        message(FATAL_ERROR "concepts diagnostics on this compiler unsupported")
    endif()
endif()

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

message(STATUS "CPPLR_WALL                 : ${CPPLR_WALL}")
message(STATUS "CPPLR_WERROR               : ${CPPLR_WERROR}")
message(STATUS "CPPLR_ENABLE_TESTING       : ${CPPLR_ENABLE_TESTING}")
message(STATUS "CPPLR_STDLIB               : ${CPPLR_STDLIB}")
message(STATUS "CPPLR_STDLIB_PATH          : ${CPPLR_STDLIB_PATH}")
message(STATUS "CPPLR_STDLIB_INCLUDE       : ${CPPLR_STDLIB_INCLUDE}")
message(STATUS "CPPLR_ENABLE_STATIC_STDLIB : ${CPPLR_ENABLE_STATIC_STDLIB}")
