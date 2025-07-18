cmake_minimum_required(VERSION 3.14)

cmake_policy(SET CMP0114 NEW)

project(waywiimote-mouse-driver)

include(ExternalProject)
include(FetchContent)
include(FindPkgConfig)

find_package(PkgConfig)

pkg_search_module(
    evdev REQUIRED 
    IMPORTED_TARGET
    libevdev>=1.12
)
pkg_search_module(
    xwiimote REQUIRED
    IMPORTED_TARGET
    libxwiimote
)

pkg_check_modules(WAYLAND REQUIRED wayland-client wayland-protocols)

# test applications
add_executable(
    mouse-test
        src/testapps/mouse-test.cpp
        src/driver/virtualmouse.hpp
        src/driver/virtualmouse.cpp
)
set_target_properties(mouse-test PROPERTIES CXX_STANDARD 17)
target_link_libraries(mouse-test PkgConfig::evdev PkgConfig::xwiimote)

add_executable(xwiimote-test src/testapps/xwiimote-test.cpp)
set_target_properties(xwiimote-test PROPERTIES CXX_STANDARD 17)
target_link_libraries(xwiimote-test PkgConfig::evdev PkgConfig::xwiimote)

add_executable(test-maths src/testapps/testmaths.cpp src/driver/intlinalg.hpp)
set_target_properties(test-maths PROPERTIES CXX_STANDARD 17)

# Actual mouse driver
ExternalProject_Add(
    sockpp
    URL https://github.com/fpagliughi/sockpp/archive/refs/tags/v1.0.0.tar.gz
    URL_HASH SHA256=818477fae6eb29b9daacef71a3d0c494de9e48f6fd267a7fc5a4c332db211bc5
    CMAKE_ARGS 
        "-DCMAKE_INSTALL_PREFIX=${CMAKE_BINARY_DIR}/sockpp-install"
        -DSOCKPP_BUILD_STATIC=true
        -DSOCKPP_BUILD_SHARED=false
)
ExternalProject_Add_StepTargets(sockpp install)

add_executable(
    waywiimote-mouse-driver 
        src/driver/drivermain.cpp
        src/driver/xdg-output-protocol.h
        src/driver/xdg-output-protocol.c
        src/driver/base.hpp
        src/driver/virtualmouse.hpp
        src/driver/virtualmouse.cpp
        src/driver/intlinalg.hpp
        src/driver/floatlinalg.hpp
        src/driver/controlsocket.hpp
        src/driver/controlsocket.cpp
        src/driver/device.hpp
        src/driver/device.cpp
        src/driver/settings.hpp
        src/driver/settings.cpp
        src/driver/stringtools.hpp
        src/driver/stringtools.cpp
        src/driver/driveroptparse.hpp
        src/driver/driverextra.hpp
        src/driver/driverextra.cpp
        src/driver/filterlayers/base.hpp
        src/driver/filterlayers/buttons.hpp
        src/driver/filterlayers/smoother.hpp
        src/driver/filterlayers/smoother.cpp
        src/driver/filterlayers/unrotate.hpp
        src/driver/filterlayers/predictive.hpp
        src/driver/filterlayers/predictive.cpp
        src/driver/filterlayers/clustering.hpp
        src/driver/filterlayers/clustering.cpp
        src/driver/filterlayers/towedcircle.hpp
        src/driver/filterlayers/towedcircle.cpp
)
set_target_properties(waywiimote-mouse-driver PROPERTIES CXX_STANDARD 17)

add_dependencies(waywiimote-mouse-driver sockpp-install)
target_include_directories(
    waywiimote-mouse-driver
    PUBLIC
        "${CMAKE_BINARY_DIR}/sockpp-install/include/"
        ${CMAKE_CURRENT_BINARY_DIR}
        /usr/include
)
target_link_libraries(
    waywiimote-mouse-driver
        PkgConfig::evdev
        PkgConfig::xwiimote
        ${WAYLAND_LIBRARIES}
        "${CMAKE_BINARY_DIR}/sockpp-install/lib64/libsockpp.a"
)

add_compile_options(waywiimote-mouse-driver -Wall ${WAYLAND_CFLAGS})
