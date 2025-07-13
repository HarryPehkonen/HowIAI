# BUILD_SYSTEM.md

Modern CMake best practices and patterns for maintainable, portable C++ projects.

## Core CMake Principles

### Modern CMake Standards
- **Minimum version**: Use CMake 3.10+ for FetchContent and modern features
- **Target-based**: Always work with targets, not global variables
- **Interface propagation**: Use `PUBLIC`, `PRIVATE`, `INTERFACE` appropriately
- **Generator expressions**: Use for conditional compilation flags
- **Out-of-source builds**: Never build in source directory

### Project Structure
```cmake
cmake_minimum_required(VERSION 3.10 FATAL_ERROR)

project(ProjectName VERSION 0.1.0 LANGUAGES CXX)

# Set standards early
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
```

## Dependency Management

### FetchContent for External Libraries
**Use FetchContent for header-only and small dependencies:**

```cmake
include(FetchContent)

# CLI11 for command-line parsing
FetchContent_Declare(
  CLI11
  GIT_REPOSITORY https://github.com/CLIUtils/CLI11.git
  GIT_TAG        v2.4.2  # Always pin to specific versions
)

# UTF8-CPP for UTF-8 processing
FetchContent_Declare(
  utf8cpp
  GIT_REPOSITORY https://github.com/nemtrif/utfcpp.git
  GIT_TAG        v4.0.5  # Pin versions for reproducible builds
)

FetchContent_MakeAvailable(CLI11 utf8cpp)
```

**Best Practices:**
- Pin to specific tags/commits for reproducible builds
- Group related FetchContent declarations
- Document why each dependency is needed
- Prefer header-only libraries to reduce build complexity

### Package-based Dependencies
**For system libraries, use find_package:**

```cmake
# For testing framework
find_package(GTest CONFIG REQUIRED)

# For optional dependencies
find_package(Boost OPTIONAL_COMPONENTS filesystem)
if(Boost_FOUND)
    target_link_libraries(myapp PRIVATE Boost::filesystem)
endif()
```

## Target Configuration

### Library Targets
```cmake
# Create library target
add_library(myproject_core STATIC 
    src/core.cpp
    src/utils.cpp
)

# Set include directories
target_include_directories(myproject_core 
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include
    PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/src
)

# Link dependencies
target_link_libraries(myproject_core 
    PUBLIC utf8cpp
    PRIVATE other_internal_lib
)
```

### Executable Targets
```cmake
# Create executable
add_executable(myproject src/main.cpp)

# Link against library and external dependencies
target_link_libraries(myproject 
    PRIVATE myproject_core 
    PRIVATE CLI11::CLI11
)
```

### Visibility Keywords
- **PUBLIC**: Interface used by both target and consumers
- **PRIVATE**: Internal implementation details
- **INTERFACE**: Header-only libraries, pure interface targets

## Build Configuration

### Development Tools Integration
```cmake
# Generate compile_commands.json for clang-tidy
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Output directory configuration
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})

# Enable debugging symbols in Debug builds
if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    target_compile_options(myproject PRIVATE -g)
endif()
```

### Compiler-Specific Settings
```cmake
# Warning levels
if(MSVC)
    target_compile_options(myproject PRIVATE /W4)
else()
    target_compile_options(myproject PRIVATE -Wall -Wextra -Wpedantic)
endif()

# Platform-specific definitions
if(WIN32)
    target_compile_definitions(myproject PRIVATE PLATFORM_WINDOWS)
elseif(UNIX)
    target_compile_definitions(myproject PRIVATE PLATFORM_UNIX)
endif()
```

## Testing Integration

### Google Test Setup
```cmake
enable_testing()

# Find or fetch Google Test
find_package(GTest CONFIG REQUIRED)

# Create test executable
add_executable(myproject_tests 
    tests/test_main.cpp
    tests/test_core.cpp
)

# Link test dependencies
target_link_libraries(myproject_tests 
    PRIVATE GTest::gtest_main 
    PRIVATE myproject_core
)

# Auto-discover tests
include(GoogleTest)
gtest_discover_tests(myproject_tests)
```

### Custom Test Additions
```cmake
# Add custom integration test
add_test(
    NAME IntegrationTest_LineByLine 
    COMMAND ${CMAKE_SOURCE_DIR}/tests/integration/test_line_by_line.sh
)

# Set test properties
set_tests_properties(IntegrationTest_LineByLine PROPERTIES
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
    TIMEOUT 30
)
```

## File Organization

### Directory Structure
```
project/
├── CMakeLists.txt          # Root build config
├── src/
│   ├── CMakeLists.txt      # Source targets
│   ├── main.cpp
│   ├── core.cpp
│   └── core.h
├── tests/
│   ├── CMakeLists.txt      # Test configuration
│   ├── test_main.cpp
│   └── integration/
│       └── test_script.sh
├── include/                # Public headers (if library)
└── docs/                   # Documentation
```

### Subdirectory CMakeLists.txt

**src/CMakeLists.txt:**
```cmake
# Define library target
add_library(${PROJECT_NAME}_core STATIC 
    core.cpp
)

# Configure target
target_include_directories(${PROJECT_NAME}_core 
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}
)

target_link_libraries(${PROJECT_NAME}_core 
    PUBLIC utf8cpp
)

# Define executable
add_executable(${PROJECT_NAME} main.cpp)
target_link_libraries(${PROJECT_NAME} 
    PRIVATE ${PROJECT_NAME}_core 
    PRIVATE CLI11::CLI11
)

# Installation rules
install(TARGETS ${PROJECT_NAME} DESTINATION bin)
```

**tests/CMakeLists.txt:**
```cmake
find_package(GTest CONFIG REQUIRED)

add_executable(${PROJECT_NAME}_tests test_main.cpp)
target_link_libraries(${PROJECT_NAME}_tests 
    PRIVATE GTest::gtest_main 
    PRIVATE ${PROJECT_NAME}_core
)

include(GoogleTest)
gtest_discover_tests(${PROJECT_NAME}_tests)
```

## Build Types and Configurations

### Standard Build Types
```bash
# Debug build - development and debugging
cmake -B build-debug -DCMAKE_BUILD_TYPE=Debug -S .

# Release build - optimized production code
cmake -B build-release -DCMAKE_BUILD_TYPE=Release -S .

# RelWithDebInfo - optimized with debug symbols
cmake -B build-reldbg -DCMAKE_BUILD_TYPE=RelWithDebInfo -S .
```

### Custom Build Options
```cmake
# Project-specific options
option(ENABLE_TESTING "Build and run tests" ON)
option(ENABLE_BENCHMARKS "Build performance benchmarks" OFF)

# Conditional compilation
if(ENABLE_TESTING)
    enable_testing()
    add_subdirectory(tests)
endif()

if(ENABLE_BENCHMARKS)
    add_subdirectory(benchmarks)
endif()
```

## Installation and Packaging

### Basic Installation
```cmake
# Install executable
install(TARGETS myproject 
    RUNTIME DESTINATION bin
)

# Install headers (for libraries)
install(DIRECTORY include/ 
    DESTINATION include
    FILES_MATCHING PATTERN "*.h"
)

# Install documentation
install(FILES README.md LICENSE 
    DESTINATION share/doc/myproject
)
```

### CPack Integration
```cmake
# Package configuration
set(CPACK_PACKAGE_NAME "MyProject")
set(CPACK_PACKAGE_VERSION ${PROJECT_VERSION})
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "Brief project description")

# Platform-specific packaging
if(WIN32)
    set(CPACK_GENERATOR "ZIP")
elseif(APPLE)
    set(CPACK_GENERATOR "DragNDrop")
else()
    set(CPACK_GENERATOR "TGZ")
endif()

include(CPack)
```

## Common Patterns from Nej Project

### Multi-Target Project
```cmake
# Core library (reusable)
add_library(nej_core STATIC core.cpp)
target_include_directories(nej_core PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(nej_core PUBLIC utf8cpp)

# Main executable
add_executable(nej main.cpp)
target_link_libraries(nej PRIVATE nej_core CLI11::CLI11)

# Test executable
add_executable(nej_tests test_main.cpp)
target_link_libraries(nej_tests PRIVATE GTest::gtest_main nej_core)
```

### Large Data Files
```cmake
# For large auto-generated files like emoji_data.h
# Keep them in separate headers and include only where needed
# Consider generating at build time if data changes frequently

add_custom_command(OUTPUT emoji_data.h
    COMMAND python3 ${CMAKE_SOURCE_DIR}/generate_emoji_header.py
    DEPENDS ${CMAKE_SOURCE_DIR}/emoji_data.json
    COMMENT "Generating emoji data header"
)

add_custom_target(generate_emoji_data DEPENDS emoji_data.h)
add_dependencies(nej_core generate_emoji_data)
```

## Best Practices Summary

### Do's
✅ **Use FetchContent** for external dependencies
✅ **Pin dependency versions** for reproducible builds  
✅ **Separate libraries and executables** into different targets
✅ **Use target-based linking** with visibility keywords
✅ **Enable CMAKE_EXPORT_COMPILE_COMMANDS** for tooling
✅ **Structure with subdirectories** for organization
✅ **Use modern CMake features** (target_*, interface propagation)

### Don'ts  
❌ **Don't use global variables** like `include_directories()`
❌ **Don't hardcode paths** - use CMake variables and functions
❌ **Don't forget to set C++ standard** requirements
❌ **Don't mix generator expressions** with string concatenation  
❌ **Don't ignore target visibility** (PUBLIC/PRIVATE/INTERFACE)
❌ **Don't build in source directory**
❌ **Don't use file(GLOB)** for source files - list explicitly

## Troubleshooting Common Issues

### Dependency Problems
```bash
# Clear CMake cache when changing dependencies
rm -rf build/
cmake -B build -S .

# Debug FetchContent issues
cmake -B build -S . --debug-find
```

### Cross-Platform Issues
```cmake
# Handle path separators correctly
file(TO_CMAKE_PATH "${USER_PATH}" NORMALIZED_PATH)

# Use generator expressions for platform differences
target_compile_definitions(myapp PRIVATE 
    $<$<PLATFORM_ID:Windows>:PLATFORM_WINDOWS>
    $<$<PLATFORM_ID:Linux>:PLATFORM_LINUX>
)
```

### Build Performance
```cmake
# Parallel builds
cmake --build build --parallel $(nproc)

# Unity builds for faster compilation (CMake 3.16+)
set_target_properties(myproject PROPERTIES UNITY_BUILD ON)
```

This build system approach ensures maintainable, portable, and efficient C++ projects with modern CMake practices.