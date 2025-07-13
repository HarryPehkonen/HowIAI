# C++ Project Template

A comprehensive template for modern C++ projects with best practices, documentation, and development workflows.

## Overview

This template provides a complete foundation for C++ projects, including:

- **Modern CMake build system** with FetchContent dependency management
- **Code quality tools** with clang-format and clang-tidy configurations
- **Testing framework** setup with Google Test and integration tests
- **Documentation standards** and project organization guidelines
- **Development workflows** for iteration and maintenance

## Getting Started

### 1. Clone and Customize
```bash
# Copy this template to your new project
cp -r HowIAI/ your-project-name/
cd your-project-name/

# Update project name in CMakeLists.txt
# Update README.md with your project details
```

### 2. Set Up Build Environment
```bash
# Create build directory
mkdir build && cd build

# Configure with CMake
cmake -DCMAKE_BUILD_TYPE=Debug ..

# Build project
make -j$(nproc)
```

### 3. Run Tests
```bash
# Run unit tests
./build/your_project_tests

# Run integration tests (if available)
cd tests/integration && ./test_script.sh
```

## Template Structure

### Core Documentation
- [`BUILD_SYSTEM.md`](BUILD_SYSTEM.md) - Modern CMake patterns and best practices
- [`PROJECT_STRUCTURE.md`](PROJECT_STRUCTURE.md) - Directory organization and file structure guidelines
- [`CODE_QUALITY.md`](CODE_QUALITY.md) - Development standards and code quality practices
- [`LINTING_PROCESS.md`](LINTING_PROCESS.md) - Static analysis and code formatting workflow
- [`TESTING_STANDARDS.md`](TESTING_STANDARDS.md) - Testing practices and framework usage

### Project Development Processes
- [`REWRITE_PROCESS.md`](REWRITE_PROCESS.md) - Systematic approach for project rewrites and refactoring
- [`ITERATION_LOG.md`](ITERATION_LOG.md) - Decision tracking and development history

## Key Features

### Modern C++ Standards
- C++17 minimum with modern CMake (3.10+)
- Target-based dependency management
- Comprehensive compiler warning configurations
- Cross-platform build support

### Development Tools Integration
- Clang-format for consistent code styling
- Clang-tidy for static analysis
- Google Test for unit and integration testing
- Generate compile_commands.json for IDE support

### Scalable Architecture
- Modular project structure that scales from small to large projects
- Clear separation between libraries and executables
- Header organization with forward declarations
- Dependency management with FetchContent

## Usage Patterns

### Small Projects (< 1000 lines)
Copy the basic structure and remove unused directories:
```
src/main.cpp + src/utils.h + tests/test_main.cpp
```

### Medium Projects (1-10k lines)
Use the full template structure with module organization:
```
src/core/ + src/io/ + src/utils/ + include/project/
```

### Large Projects (> 10k lines)
Extend with additional patterns from PROJECT_STRUCTURE.md:
```
libs/ + apps/ + extensive testing hierarchy
```

## Customization

1. **Project Name**: Update `CMakeLists.txt` project name and target names
2. **Dependencies**: Modify FetchContent declarations in `CMakeLists.txt`
3. **Code Style**: Adjust `.clang-format` and `.clang-tidy` configurations
4. **Documentation**: Update this README and other .md files for your project

## Development Workflow

### Initial Setup
1. Copy template and customize project settings
2. Set up dependencies and build configuration
3. Implement core functionality with tests
4. Configure CI/CD and documentation

### Iteration Process
- Use `REWRITE_METHODOLOGY.md` for major architectural changes
- Track decisions and progress in `ITERATION_LOG.md`
- Maintain code quality with linting and testing standards
- Document lessons learned for future iterations

## Best Practices

This template embodies modern C++ development practices:

- **Target-based CMake** with proper visibility keywords
- **Minimal header dependencies** with forward declarations
- **Comprehensive testing** with unit and integration tests
- **Consistent code style** with automated formatting
- **Clear documentation** at project and code levels

## Contributing

When using this template:

1. Follow the guidelines in `CODE_QUALITY.md`
2. Run linting and tests before commits
3. Update documentation as you extend the template
4. Share improvements back to the template repository

## License

This template is provided under the terms specified in the `LICENSE` file.