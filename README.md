# C++ Project Template

A comprehensive template for AI-assisted modern C++ project development with established best practices, documentation standards, and development workflows.

## Overview

This template provides a systematic approach for creating high-quality C++ projects with AI assistance. It includes:

- **AI Assistant Guidelines** - Structured reference materials for consistent AI-assisted development
- **Modern C++ Standards** - C++17+ patterns, RAII, modern CMake, and best practices
- **Code Quality Framework** - Automated formatting, linting, and comprehensive testing
- **Development Processes** - Systematic approaches for iteration, refactoring, and project management
- **Scalable Architecture** - Patterns that work from prototypes to production systems

## Getting Started

### 1. Set Up New Project
```bash
# Create new project repository on GitHub with README.md, LICENSE, .gitignore
# Clone to your local machine
git clone https://github.com/yourusername/your-project-name.git
cd your-project-name

# Copy the AI assistant reference materials
cp -r ../HowIAI/AI .
```

### 2. AI-Assisted Planning
1. **Discuss requirements** with your AI assistant
2. **AI creates** `AI/REQUIREMENTS.md` and `AI/TECHNICAL_DETAILS.md`
3. **Review and refine** until requirements are clear
4. **Begin development** with AI referencing all `AI/` materials

### 3. Development Workflow
```bash
# AI assistant will guide you through:
# - Project structure setup
# - CMake configuration  
# - Code implementation following established standards
# - Testing and quality assurance
# - Documentation updates
```

## Template Structure

The `AI/` directory contains all reference materials for AI assistants:

### AI/GUIDE.md
Central navigation hub that explains the workflow, document purposes, and includes a development checklist for consistent quality.

### AI/SETUP_AND_CONFIGURATION/
- **PROJECT_STRUCTURE.md** - Directory organization, file naming, scaling patterns
- **BUILD_SYSTEM.md** - Modern CMake patterns, dependency management, build configuration  
- **LINTING_PROCESS.md** - Code quality enforcement, clang-tidy setup, systematic improvement

### AI/DEVELOPMENT_STANDARDS/
- **CODE_QUALITY.md** - C++ best practices, memory safety, function design, naming conventions
- **TESTING_STANDARDS.md** - Unit testing, integration testing, test organization, coverage requirements

### AI/PROJECT_MANAGEMENT/
- **REWRITE_PROCESS.md** - When and how to approach major refactoring or architectural changes
- **ITERATION_LOG.md** - Systematic tracking of decisions, lessons learned, and project evolution

### AI/Project-Specific Documents (AI-Generated)
- **REQUIREMENTS.md** - Project goals, user needs, acceptance criteria (created by AI)
- **TECHNICAL_DETAILS.md** - Architecture decisions, tech stack, implementation approach (created by AI)

## Key Benefits

### AI-Assisted Development
- **Structured guidance** for AI assistants with comprehensive reference materials
- **Consistent quality** through established standards and automated checking
- **Systematic documentation** of decisions and lessons learned
- **Proven patterns** from real-world C++ project experience

### Modern C++ Excellence
- **C++17+ standards** with modern CMake (3.10+) and target-based dependency management
- **Automated quality assurance** with clang-format and clang-tidy integration
- **Comprehensive testing** with Google Test and integration test patterns
- **Cross-platform support** with proper build configuration

### Scalable Process
- **Small to large projects** - Patterns that scale with project complexity
- **Team collaboration** - Clear documentation and established workflows
- **Knowledge preservation** - Systematic tracking of what works and what doesn't
- **Continuous improvement** - Built-in processes for iteration and refinement

## Usage Philosophy

This template is designed for **AI-human collaborative development** where:

1. **Humans provide vision and requirements**
2. **AI implements using established patterns and standards**  
3. **Both parties reference comprehensive guidelines**
4. **Quality is maintained through systematic processes**
5. **Knowledge is captured for future iterations**

The `AI/` directory serves as a comprehensive reference library that ensures consistent, high-quality development regardless of project complexity.

## License

This template is provided under the terms specified in the `LICENSE` file.