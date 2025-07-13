# AI Assistant Guide

## Development Checklist ⚠️

**After each implementation stage:**
- [ ] Format and lint code (follow LINTING_PROCESS.md)
- [ ] Compile and run tests (see TESTING_STANDARDS.md)
- [ ] Update README.md with any new features or setup steps
- [ ] Document decisions in TECHNICAL_DETAILS.md
- [ ] Note lessons learned for future reference

**Before declaring completion:**
- [ ] All tests pass
- [ ] Code follows quality standards in CODE_QUALITY.md
- [ ] Documentation is current and accurate
- [ ] No compiler warnings
- [ ] User requirements are fully satisfied

## Workflow Overview

This template provides a structured approach for AI-assisted C++ project development. Here's what to expect:

1. **Initial Discussion**: User will discuss project requirements and technical approach with you
2. **Document Creation**: You'll create `REQUIREMENTS.md` and `TECHNICAL_DETAILS.md` in this directory
3. **Development**: Use all documents in this directory as reference during coding
4. **Iteration**: Update documents as the project evolves

## Expected Documents (You Create)

- **REQUIREMENTS.md**: Project goals, user needs, acceptance criteria, functional requirements
- **TECHNICAL_DETAILS.md**: Architecture decisions, tech stack choices, implementation approach, design patterns

## Reference Materials (Template Provided)

### SETUP_AND_CONFIGURATION/
- **PROJECT_STRUCTURE.md**: Directory organization, file naming, scaling patterns
- **BUILD_SYSTEM.md**: Modern CMake patterns, dependency management, build configuration
- **LINTING_PROCESS.md**: Code quality enforcement, clang-tidy setup, systematic fix process

### DEVELOPMENT_STANDARDS/
- **CODE_QUALITY.md**: C++ best practices, memory safety, function design, naming conventions
- **TESTING_STANDARDS.md**: Unit testing, integration testing, test organization, coverage requirements

### PROJECT_MANAGEMENT/
- **REWRITE_PROCESS.md**: When and how to approach major refactoring or architectural changes
- **ITERATION_LOG.md**: Systematic tracking of decisions, lessons learned, and project evolution

## Your Role

- **Reference these guidelines** throughout development to ensure consistency with established patterns
- **Create and maintain** REQUIREMENTS.md and TECHNICAL_DETAILS.md based on user discussions
- **Follow standards** in DEVELOPMENT_STANDARDS/ for all code implementation
- **Document decisions** as they're made to maintain project knowledge
- **Use templates** from PROJECT_MANAGEMENT/ when major changes are needed

## Quick Reference

- **Starting a new project**: Begin with SETUP_AND_CONFIGURATION/ documents
- **Daily development**: Reference CODE_QUALITY.md and TESTING_STANDARDS.md
- **Major architectural changes**: Use REWRITE_PROCESS.md methodology
- **Tracking progress**: Maintain entries in ITERATION_LOG.md format
- **When stuck**: Review similar patterns in the reference materials

This template is designed to accelerate development while maintaining high code quality and comprehensive documentation.