# PROJECT_STRUCTURE.md

Guidelines for organizing C++ projects for maintainability, scalability, and team collaboration.

## Standard Directory Layout

### Recommended Project Structure
```
project_name/
├── CMakeLists.txt                 # Root build configuration
├── README.md                      # Project overview and usage
├── LICENSE                        # License information
├── CHANGELOG.md                   # Version history and changes
├── .gitignore                     # Git ignore patterns
├── .clang-format                  # Code formatting rules
├── .clang-tidy                    # Static analysis configuration
├── CLAUDE.md                      # Claude Code integration guide
├── CODE_QUALITY.md               # Development guidelines
├── LINTING_PROCESS.md             # Linting setup and workflow
├── BUILD_SYSTEM.md               # CMake best practices
├── TESTING_STANDARDS.md          # Testing guidelines
├── PROJECT_STRUCTURE.md          # This file
├── 
├── src/                           # Source code
│   ├── CMakeLists.txt            # Source build config
│   ├── main.cpp                  # Main executable entry point
│   ├── core.h                    # Core functionality header
│   ├── core.cpp                  # Core functionality implementation
│   └── data/                     # Data files and resources
│       └── emoji_data.h          # Large data files
├── 
├── include/                       # Public headers (for libraries)
│   └── project_name/             # Namespaced include directory
│       └── public_api.h          # Public interface
├── 
├── tests/                         # Test suite
│   ├── CMakeLists.txt            # Test build configuration
│   ├── test_main.cpp             # Unit tests entry point
│   ├── integration/              # Integration tests
│   │   └── test_end_to_end.sh   # Shell-based integration tests
│   └── data/                     # Test data files
│       └── sample_inputs.txt     # Test input files
├── 
├── docs/                          # Documentation
│   ├── api.md                    # API documentation
│   ├── examples/                 # Usage examples
│   └── design/                   # Design documents
├── 
├── scripts/                       # Build and utility scripts
│   ├── build.sh                  # Build script
│   ├── test.sh                   # Test runner script
│   └── format.sh                 # Code formatting script
├── 
├── tools/                         # Development tools
│   └── generate_data.py          # Code/data generation scripts
├── 
└── build/                         # Build output (git-ignored)
    ├── nej                       # Compiled executable
    ├── nej_tests                 # Test executable
    └── compile_commands.json     # For clang-tidy
```

## File Organization Principles

### Source Code Organization
- **Single responsibility**: Each file should have a clear, focused purpose
- **Interface separation**: Keep headers (.h) lean with declarations only
- **Implementation hiding**: Put implementation details in .cpp files
- **Logical grouping**: Group related functionality together
- **Dependency minimization**: Reduce header dependencies with forward declarations

### Naming Conventions
```
Files:
├── snake_case.cpp/.h             # Source files
├── PascalCase.hpp               # C++ library headers (optional style)
├── lowercase.md                 # Documentation files
└── UPPERCASE.md                # Project directives and guidelines

Directories:
├── lowercase/                   # Standard directories
├── snake_case/                  # Multi-word directories
└── no-hyphens/                 # Avoid hyphens in directory names
```

## Nej Project Analysis

### What Works Well
```
Nej/                              # Clean, focused structure
├── src/                          # Clear source organization
│   ├── main.cpp        (167L)   # Reasonable main file size
│   ├── core.cpp        (83L)    # Well-sized implementation
│   ├── core.h          (22L)    # Lean header
│   └── emoji_data.h    (4801L)  # Large data isolated
├── tests/
│   ├── test_main.cpp             # Comprehensive unit tests
│   └── integration/              # Shell-based integration tests
└── CMakeLists.txt               # Modern CMake setup
```

### Key Lessons
- **Separate large data**: Keep generated/large files like `emoji_data.h` separate
- **Modular design**: Core logic separated from CLI interface (`core.cpp` vs `main.cpp`)
- **Comprehensive testing**: Both unit tests and integration tests
- **Tool integration**: `.clang-format`, `.clang-tidy` for code quality

## Scaling Patterns

### Small Projects (< 1000 lines)
```
project/
├── CMakeLists.txt
├── README.md
├── src/
│   ├── main.cpp
│   └── utils.h                   # Single header for utilities
└── tests/
    └── test_main.cpp
```

### Medium Projects (1000-10000 lines)
```
project/
├── CMakeLists.txt
├── src/
│   ├── CMakeLists.txt
│   ├── main.cpp
│   ├── core/                     # Core functionality module
│   │   ├── engine.cpp
│   │   └── engine.h
│   ├── io/                       # File I/O module
│   │   ├── file_handler.cpp
│   │   └── file_handler.h
│   └── utils/                    # Utility functions
│       ├── string_utils.cpp
│       └── string_utils.h
├── include/                      # Public API
│   └── project/
│       └── api.h
└── tests/
    ├── test_core.cpp
    ├── test_io.cpp
    └── integration/
```

### Large Projects (> 10000 lines)
```
project/
├── CMakeLists.txt
├── libs/                         # Internal libraries
│   ├── common/                   # Shared utilities
│   ├── data_processing/          # Domain-specific libraries
│   └── file_io/
├── apps/                         # Multiple applications
│   ├── cli_tool/
│   └── gui_app/
├── include/                      # Public API
│   └── project/
├── tests/
│   ├── unit/                     # Unit tests by module
│   ├── integration/              # Integration tests
│   └── performance/              # Performance tests
└── docs/
    ├── api/                      # Generated API docs
    └── guides/                   # User guides
```

## Header Organization

### Header Structure Pattern
```cpp
#ifndef PROJECT_MODULE_H
#define PROJECT_MODULE_H

// System includes first
#include <algorithm>
#include <string>
#include <vector>

// Third-party includes
#include <CLI/CLI.hpp>
#include <utf8.h>

// Project includes (use forward declarations when possible)
#include "project/other_module.h"

// Forward declarations
class ComplexClass;
struct InternalData;

// Constants and enums
enum class ProcessingMode {
    Strict,
    Permissive
};

// Function declarations
auto processData(const std::string& input) -> std::pair<std::string, int>;

// Class declarations
class DataProcessor {
public:
    explicit DataProcessor(ProcessingMode mode);
    
    auto process(const std::string& data) -> std::string;
    
private:
    ProcessingMode mode_;
    // Implementation details hidden
};

#endif // PROJECT_MODULE_H
```

### Include Management
```cpp
// core.h - Minimal includes in header
#include <cstdint>
#include <filesystem>
#include <string>
#include <vector>

// Forward declarations to avoid includes
namespace std {
    template<typename T> class set;
}

// core.cpp - Full includes in implementation
#include "core.h"

#include <algorithm>    // For std::find_if
#include <fstream>      // For file operations
#include <iterator>     // For std::back_inserter
#include <set>          // Now we can use std::set
#include <utility>      // For std::pair

#include "emoji_data.h" // Internal data
```

## Build System Organization

### CMake File Structure
```cmake
# Root CMakeLists.txt - Project configuration
cmake_minimum_required(VERSION 3.10 FATAL_ERROR)
project(ProjectName VERSION 1.0.0 LANGUAGES CXX)

# Global settings
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Dependencies
include(FetchContent)
# ... dependency declarations

# Subdirectories
add_subdirectory(src)
enable_testing()
add_subdirectory(tests)

# Optional components
option(BUILD_DOCS "Build documentation" OFF)
if(BUILD_DOCS)
    add_subdirectory(docs)
endif()
```

```cmake
# src/CMakeLists.txt - Source configuration
# Core library (reusable)
add_library(${PROJECT_NAME}_core STATIC
    core.cpp
)

target_include_directories(${PROJECT_NAME}_core
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}
)

target_link_libraries(${PROJECT_NAME}_core
    PUBLIC utf8cpp
)

# Main executable
add_executable(${PROJECT_NAME} main.cpp)
target_link_libraries(${PROJECT_NAME}
    PRIVATE ${PROJECT_NAME}_core
    PRIVATE CLI11::CLI11
)

install(TARGETS ${PROJECT_NAME} DESTINATION bin)
```

## Documentation Organization

### Essential Documentation Files
- **README.md**: Project overview, quick start, basic usage
- **CHANGELOG.md**: Version history and breaking changes
- **CONTRIBUTING.md**: Development setup and contribution guidelines
- **API.md**: Detailed API documentation
- **EXAMPLES.md**: Usage examples and tutorials

### Documentation Structure
```
docs/
├── README.md                     # Overview and quick start
├── user_guide/                   # User-facing documentation
│   ├── installation.md
│   ├── usage.md
│   └── troubleshooting.md
├── developer_guide/              # Developer documentation
│   ├── architecture.md
│   ├── building.md
│   └── testing.md
├── api/                          # Generated API documentation
│   └── doxygen_output/
└── examples/                     # Code examples
    ├── basic_usage.cpp
    └── advanced_features.cpp
```

## Version Control Organization

### .gitignore Essentials
```gitignore
# Build artifacts
build/
build-*/
*.o
*.so
*.dll
*.exe

# IDE files
.vscode/
.idea/
*.swp
*.swo

# OS files
.DS_Store
Thumbs.db

# CMake generated files
CMakeCache.txt
CMakeFiles/
compile_commands.json
```

### Git Workflow Structure
```
├── main                          # Production-ready code
├── develop                       # Integration branch
├── feature/feature-name          # Feature development
├── hotfix/critical-fix           # Critical bug fixes
└── release/v1.0.0               # Release preparation
```

## Dependency Management

### External Dependencies
```
external/                         # Git submodules or manual deps
├── CLI11/                       # Command line parsing
├── utf8cpp/                     # UTF-8 processing
└── googletest/                  # Testing framework
```

### Internal Dependencies
```
src/
├── common/                       # Shared utilities
│   ├── string_utils.h
│   └── file_utils.h
├── data_processing/              # Core algorithms
│   └── emoji_processor.h
└── cli/                         # User interface
    └── command_handler.h
```

## Quality Assurance Structure

### Code Quality Files
```
├── .clang-format                # Formatting configuration
├── .clang-tidy                  # Static analysis rules
├── .editorconfig               # Editor configuration
├── CODE_QUALITY.md            # Development guidelines
├── LINTING_PROCESS.md          # Linting workflow
└── TESTING_STANDARDS.md       # Testing practices
```

### CI/CD Configuration
```
.github/
├── workflows/
│   ├── ci.yml                  # Continuous integration
│   ├── release.yml             # Release automation
│   └── docs.yml                # Documentation generation
└── ISSUE_TEMPLATE.md           # Issue reporting template
```

## Best Practices Summary

### Project Organization Do's
✅ **Keep related files together** in logical directories
✅ **Separate interface from implementation** (headers vs source)
✅ **Use consistent naming conventions** throughout the project
✅ **Isolate large generated files** (like `emoji_data.h`)
✅ **Provide clear documentation** at all levels
✅ **Use standard directory names** (`src`, `tests`, `docs`)
✅ **Configure quality tools** from the start

### Project Organization Don'ts
❌ **Don't mix different concerns** in the same directory
❌ **Don't create deep nested hierarchies** without clear purpose
❌ **Don't ignore build artifacts** in version control
❌ **Don't forget platform-specific considerations**
❌ **Don't skip documentation** for "obvious" code
❌ **Don't hardcode paths** or platform assumptions

This structure ensures projects remain maintainable and scalable as they grow from prototypes to production systems.