cmake_minimum_required(VERSION 3.15)
project(sample LANGUAGES C CXX)

option(CMAKE_BUILD_TYPE "build type" Debug)

# 编译设置
add_compile_definitions(HAVE_CONFIG_H HANDLE_MULTIBYTE)

# readline库
file(GLOB READLINE_SOURCES 
    readline/*.c
    readline/*.h
)

# list(REMOVE_ITEM READLINE_SOURCES
#     "${CMAKE_CURRENT_SOURCE_DIR}/readline/emacs_keymap.c"
#     "${CMAKE_CURRENT_SOURCE_DIR}/readline/vi_keymap.c"
# )

add_library(readline STATIC ${READLINE_SOURCES})
target_include_directories(readline PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/compat
    ${CMAKE_CURRENT_SOURCE_DIR}/readline
    ${CMAKE_CURRENT_SOURCE_DIR}
)
target_compile_definitions(readline PRIVATE BUILD_READLINE)

# compat库
file(GLOB COMPAT_SOURCES 
    compat/*.c
    compat/*.h
)

add_library(compat STATIC ${COMPAT_SOURCES})
target_include_directories(compat PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/compat
    ${CMAKE_CURRENT_SOURCE_DIR}/readline
)

if(NOT MSVC)
    target_compile_options(compat PRIVATE -Werror)
endif()

# c_sample可执行文件
add_executable(c_sample "c_sample.c")
target_link_libraries(c_sample PRIVATE compat readline)
target_include_directories(c_sample PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/compat
    ${CMAKE_CURRENT_SOURCE_DIR}/readline
    ${CMAKE_CURRENT_SOURCE_DIR}
)
target_compile_options(c_sample PRIVATE /utf-8)
