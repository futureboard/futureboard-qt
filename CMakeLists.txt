# Set the minimum CMake version required
cmake_minimum_required(VERSION 3.16)

# Enable vcpkg integration
if(DEFINED ENV{VCPKG_ROOT} AND NOT DEFINED CMAKE_TOOLCHAIN_FILE)
    set(CMAKE_TOOLCHAIN_FILE "$ENV{VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake"
        CACHE STRING "")
endif()

# Project name and version
project(Futureboard VERSION 1.0.0 LANGUAGES CXX)

# Extract version components
string(REGEX MATCH "^([0-9]+)\\.([0-9]+)\\.([0-9]+)" dummy
    ${PROJECT_VERSION})
set(PROJECT_VERSION_MAJOR ${CMAKE_MATCH_1})
set(PROJECT_VERSION_MINOR ${CMAKE_MATCH_2})
set(PROJECT_VERSION_PATCH ${CMAKE_MATCH_3})

# Set C++ standard
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Set Qt prefix path if needed
if(NOT DEFINED CMAKE_PREFIX_PATH AND DEFINED ENV{QTDIR})
    set(CMAKE_PREFIX_PATH "$ENV{QTDIR}")
endif()

# Find required packages
find_package(Qt6 CONFIG REQUIRED COMPONENTS Core Gui Quick Widgets QuickWidgets)

# Source files
file(GLOB_RECURSE SRC_FILES CONFIGURE_DEPENDS
    "src/*.cpp"
    "src/*.hpp"
)

# Find the PortAudio package using vcpkg
find_package(PortAudio CONFIG REQUIRED)

# Resource files
set(RESOURCE_FILES ${CMAKE_SOURCE_DIR}/resources/shared.qrc)

# Configure the .rc file
configure_file(
    ${CMAKE_CURRENT_SOURCE_DIR}/resources/app.rc.in
    ${CMAKE_CURRENT_BINARY_DIR}/resources/app.rc
    @ONLY
)

# Add the executable target and include resource files
add_executable(${PROJECT_NAME} WIN32
    ${SRC_FILES}
    ${RESOURCE_FILES}
    ${CMAKE_CURRENT_BINARY_DIR}/resources/app.rc
)

# Link libraries to the target
target_link_libraries(${PROJECT_NAME}
    PRIVATE
    Qt6::Core
    Qt6::Gui
    Qt6::Quick
    Qt6::Widgets
    Qt6::QuickWidgets
    PortAudio::PortAudio
)

# Set include directories for headers
target_include_directories(${PROJECT_NAME}
    PRIVATE
    src
)

# Enable automatic MOC, RCC, and UIC
set_target_properties(${PROJECT_NAME} PROPERTIES
    AUTOMOC ON
    AUTORCC ON
    AUTOUIC ON
)

# Enable platform-specific options
if(PLATFORM STREQUAL "desktop")
    target_compile_definitions(${PROJECT_NAME} PRIVATE PLATFORM_DESKTOP)
elseif(PLATFORM STREQUAL "mobile")
    target_compile_definitions(${PROJECT_NAME} PRIVATE PLATFORM_MOBILE)
endif()

# Add feature summary
include(FeatureSummary)
feature_summary(WHAT ALL)
