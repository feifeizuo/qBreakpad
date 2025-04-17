cmake_minimum_required(VERSION 3.14)

project(qBreakpad VERSION 0.4.0 LANGUAGES CXX)

# Set C++11 standard which was used in original qmake project
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Define breakpad server (from original config.pri)
add_definitions(-DCALIPER)

# Windows platform specific settings
if(WIN32)
    # Define Unicode for Windows API
    add_definitions(-DUNICODE -D_UNICODE)
    
    # Disable deprecation warnings for standard C functions
    add_definitions(-D_CRT_SECURE_NO_DEPRECATE -D_CRT_NONSTDC_NO_DEPRECATE)
endif()

# Options for configuring the build
option(BUILD_SHARED_LIBS "Build shared libraries" OFF)
option(QBREAKPAD_BUILD_DEMO "Build the demo applications" ON)
option(QBREAKPAD_BUILD_TESTS "Build the test applications" OFF)
option(QBREAKPAD_INSTALL "Generate install target" ON)
option(QBREAKPAD_USE_INSTALLED_TARGETS "Use installed targets instead of project ones when not built as main project" ON)

# Find Qt packages
find_package(Qt5 COMPONENTS Core Network REQUIRED)

# Define breakpad path
set(BREAKPAD_PATH ${CMAKE_CURRENT_SOURCE_DIR}/third_party/breakpad/src)

# Add subdirectories
add_subdirectory(handler)

# Demo and tests options have been moved above

if(QBREAKPAD_BUILD_DEMO)
    add_subdirectory(demo)
endif()

if(QBREAKPAD_BUILD_TESTS)
    add_subdirectory(tests)
endif()

# Generate a qBreakpadTargets.cmake file for the build tree
export(EXPORT qBreakpadTargets
    FILE "${CMAKE_CURRENT_BINARY_DIR}/qBreakpadTargets.cmake"
    NAMESPACE qBreakpad::
)

# Register the build tree for use from the build directory
export(PACKAGE qBreakpad)

# Installation configuration
if(QBREAKPAD_INSTALL)
    include(GNUInstallDirs)
    include(CMakePackageConfigHelpers)
    
    # Configure version file
    write_basic_package_version_file(
        "${CMAKE_CURRENT_BINARY_DIR}/qBreakpadConfigVersion.cmake"
        VERSION ${PROJECT_VERSION}
        COMPATIBILITY SameMajorVersion
    )

    # Configure and install package files
    configure_package_config_file(
        "${CMAKE_CURRENT_SOURCE_DIR}/cmake/qBreakpadConfig.cmake.in"
        "${CMAKE_CURRENT_BINARY_DIR}/qBreakpadConfig.cmake"
        INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/qBreakpad
    )

    # Install CMake configuration files
    install(FILES
        "${CMAKE_CURRENT_BINARY_DIR}/qBreakpadConfig.cmake"
        "${CMAKE_CURRENT_BINARY_DIR}/qBreakpadConfigVersion.cmake"
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/qBreakpad
    )

    # Install targets
    install(EXPORT qBreakpadTargets
        FILE qBreakpadTargets.cmake
        NAMESPACE qBreakpad::
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/qBreakpad
    )
endif()
