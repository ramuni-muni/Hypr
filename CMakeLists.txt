cmake_minimum_required(VERSION 3.4)

set(CMAKE_CXX_COMPILER "/bin/g++")

project(Hypr 
    VERSION 0.1
    DESCRIPTION "A Modern OOP C++ Window Manager"
)

set(CMAKE_MESSAGE_LOG_LEVEL "STATUS")

message(STATUS "Configuring Hypr!")

add_compile_options(-std=c++17)
add_compile_options(-Wall -Wextra)
find_package(Threads REQUIRED)

find_package(PkgConfig REQUIRED)
pkg_check_modules(deps REQUIRED IMPORTED_TARGET glib-2.0 harfbuzz cairo gtkmm-3.0 xcb-randr xcb-ewmh xcb-xinerama xcb-cursor xcb-keysyms xcb-icccm)

file(GLOB_RECURSE SRCFILES "src/*.cpp")

add_executable(Hypr ${SRCFILES})

IF(CMAKE_BUILD_TYPE MATCHES Debug OR CMAKE_BUILD_TYPE MATCHES DEBUG)
    message(STATUS "Configuring Hypr in Debug with CMake!")
ELSE()
    add_compile_options(-Ofast)
    message(STATUS "Configuring Hypr in Release with CMake!")
ENDIF(CMAKE_BUILD_TYPE MATCHES Debug OR CMAKE_BUILD_TYPE MATCHES DEBUG)

IF(UNIX AND NOT APPLE)
    target_link_libraries(Hypr rt)
ENDIF()

set(CPACK_PROJECT_NAME ${PROJECT_NAME})
set(CPACK_PROJECT_VERSION ${PROJECT_VERSION})
include(CPack)

target_link_libraries(Hypr PkgConfig::deps)

target_link_libraries(Hypr
        xcb
        xcb-ewmh
        xcb-icccm
        xcb-keysyms
        xcb-randr
        xcb-xinerama
        xcb-cursor
        xcb-shape
        xcb-util
        ${CMAKE_THREAD_LIBS_INIT}
)

IF(CMAKE_BUILD_TYPE MATCHES Debug OR CMAKE_BUILD_TYPE MATCHES DEBUG)
    SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -pg -no-pie -fno-builtin")
    SET(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -pg -no-pie -fno-builtin")
    SET(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -pg -no-pie -fno-builtin")
ENDIF(CMAKE_BUILD_TYPE MATCHES Debug OR CMAKE_BUILD_TYPE MATCHES DEBUG)