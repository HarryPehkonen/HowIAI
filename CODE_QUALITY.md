# CODE_QUALITY.md

Comprehensive guidelines for writing high-quality, maintainable C++ code across all future projects.

## Core Principles

### Memory Safety & Resource Management (RAII)
- **Always use RAII**: Resource Acquisition Is Initialization is the cornerstone of safe C++
- **Smart pointers**: Use `std::unique_ptr`, `std::shared_ptr` for dynamic memory; avoid raw `new`/`delete`
- **Standard containers**: Prefer `std::string`, `std::vector`, `std::array` over C-style arrays
- **File handles**: Use `std::ifstream`/`std::ofstream` with automatic destructors
- **Exception safety**: Write exception-safe code that cleans up resources automatically

### Modern C++ Standards
- **Use C++17 minimum**: Leverage structured bindings, `std::filesystem`, `std::optional`
- **Trailing return types**: Use `auto function() -> return_type` for consistency and readability
- **Range-based loops**: Prefer `for (const auto& item : container)` over index-based loops
- **Auto keyword**: Use judiciously for complex iterator types, not for simple types
- **Const correctness**: Mark everything `const` that can be `const`

### Function Design
- **Single Responsibility**: Each function should do one thing well
- **Small functions**: Aim for 20-50 lines maximum; clang-tidy enforces 50-line limit
- **Pure functions**: Prefer functions without side effects when possible
- **Clear signatures**: Use meaningful parameter names and return types
- **Error handling**: Return `std::pair<result, error_count>` or use exceptions consistently

#### Examples from Nej Codebase:
```cpp
// Good: Clear signature, single responsibility, structured binding return
auto removeEmojis(const std::string& text) -> std::pair<std::string, int>;

// Good: RAII file handling
auto isBinary(const fs::path& file_path) -> bool {
    std::ifstream file(file_path, std::ios::binary);  // RAII - auto-closes
    if (!file.is_open()) {
        return false;
    }
    // ... rest of function
}
```

### Naming Conventions
- **Descriptive names**: `removeEmojis` not `process`, `backup_path` not `bp`
- **Consistency**: Choose camelCase, snake_case, or PascalCase and stick to it project-wide
- **Constants**: Use `UPPER_CASE` for compile-time constants
- **Private members**: Use trailing underscore `member_` or `m_member` prefix consistently
- **Boolean functions**: Use `is`, `has`, `can` prefixes (`isBinary`, `hasEmojis`)

### Code Organization & Modularity
- **Header/implementation separation**: Keep headers lean with declarations only
- **Logical grouping**: Group related functionality in classes/namespaces
- **Minimal dependencies**: Reduce coupling between modules
- **Clear interfaces**: Hide implementation details, expose minimal public API
- **Namespace usage**: Use namespaces to prevent name collisions

#### File Structure Example:
```
src/
├── core.h          // Core declarations (22 lines)
├── core.cpp        // Core implementation (83 lines)  
├── main.cpp        // CLI interface (167 lines)
└── emoji_data.h    // Large data file (4801 lines - auto-generated)
```

### Error Handling Strategy
- **Consistent approach**: Choose exceptions, error codes, or optional returns project-wide
- **Informative messages**: Provide actionable error information
- **Graceful degradation**: Handle errors without crashing when possible
- **Input validation**: Validate all external inputs (files, command line args)

#### Examples from Nej:
```cpp
// Good: Structured error handling with meaningful messages
if (!fs::exists(file_path)) {
    std::cerr << "Error: File not found: " << file_path << '\n';
    continue;  // Graceful degradation
}

// Good: Exception handling in UTF-8 processing
try {
    uint32_t code_point = utf8::next(it, end);
    utf8::append(code_point, std::back_inserter(result));
} catch (const utf8::exception&) {
    result += '?';  // Graceful fallback
    ++it;
}
```

## Code Quality Enforcement

### Automated Tools Integration
1. **clang-format**: Enforce consistent formatting automatically
2. **clang-tidy**: Static analysis for bugs, performance, readability
3. **Google Test**: Comprehensive unit testing
4. **Integration tests**: End-to-end validation with shell scripts
5. **CMake**: Modern build system with dependency management

### Documentation Standards
- **Focus on "why"**: Explain reasoning and design decisions, not obvious code
- **Public API docs**: Document all public functions, parameters, return values
- **Inline comments**: For complex algorithms or non-obvious logic only
- **README**: Comprehensive usage examples and architecture overview

### Performance Considerations
- **Avoid premature optimization**: Correctness and maintainability first
- **Profile before optimizing**: Use data to guide optimization decisions
- **Algorithm complexity**: Choose appropriate data structures and algorithms
- **Memory efficiency**: Minimize allocations, reuse containers when possible

#### Performance Examples:
```cpp
// Good: Reserve space to avoid reallocations
std::string result;
result.reserve(text.size());  // Estimate based on input size

// Good: Minimize string copying
const auto& [processed_line, removed_count] = removeEmojis(line);  // Structured binding
```

## Security & Safety

### Input Validation
- **File existence**: Always check before opening files
- **Binary detection**: Validate file types before processing
- **UTF-8 validation**: Handle malformed sequences gracefully
- **Path sanitization**: Use `std::filesystem` for safe path operations

### Safe File Operations
- **Atomic operations**: Use temporary files for in-place editing
- **Backup creation**: Never overwrite without backup
- **Cross-platform paths**: Use `std::filesystem::path` consistently
- **Race condition prevention**: Use PID + timestamp for unique temporary files

#### Example from Nej improvements:
```cpp
// Safe temporary file creation
auto timestamp = std::chrono::high_resolution_clock::now().time_since_epoch().count();
pid_t pid = getpid();
std::string temp_filename = file_path.filename().string() + "." + 
                           std::to_string(pid) + "." + std::to_string(timestamp) + ".nej_tmp";
temp_file_path = file_path.parent_path() / temp_filename;  // Same directory to avoid cross-device issues
```

## Testing Requirements

### Unit Testing
- **Test coverage**: Aim for 90%+ line coverage
- **Edge cases**: Test empty inputs, malformed data, boundary conditions
- **Test names**: Descriptive test names that explain what is being tested
- **Test isolation**: Each test should be independent and repeatable

### Integration Testing
- **End-to-end**: Test complete workflows including file I/O
- **Error scenarios**: Test error handling paths
- **Platform testing**: Validate on target platforms
- **Performance testing**: Basic performance regression detection

### Example Test Structure:
```cpp
TEST_F(RemoveEmojisTest, HandlesEmojisWithDifferentByteLengths) {
    // Test with 4-byte emoji (🚀) and 3-byte emoji (✅)
    ASSERT_EQ(removeEmojis("🚀Test✅").first, " Test ");
}
```

## Common Pitfalls to Avoid

### Memory Management
- ❌ Raw pointers for ownership
- ❌ Manual memory management
- ❌ Resource leaks in exception paths
- ❌ Dangling references/pointers

### Performance
- ❌ Unnecessary string copying
- ❌ O(n²) algorithms where O(n) exists
- ❌ Repeated expensive operations in loops
- ❌ Not reserving container capacity

### Error Handling
- ❌ Ignoring return values
- ❌ Silent failures
- ❌ Catching and ignoring exceptions
- ❌ Inconsistent error handling strategies

### Code Organization
- ❌ God classes/functions
- ❌ Circular dependencies
- ❌ Header-only implementations (except templates)
- ❌ Global state and singletons

## Readability & Maintainability

### Code Structure
- **Logical flow**: Organize code in natural reading order
- **Consistent indentation**: Use clang-format to enforce
- **Line length**: Respect 100-character limit
- **Whitespace**: Use consistently for grouping related code

### Variable Scope
- **Minimize scope**: Declare variables close to first use
- **Const by default**: Make variables const unless they must change
- **Initialization**: Initialize all variables at declaration
- **RAII objects**: Prefer stack allocation over heap when possible

### Function Parameters
- **Pass by const reference**: For non-trivial types that won't be copied
- **Pass by value**: For small types (int, bool, pointers)
- **Return by value**: Let compiler optimize (RVO/move semantics)
- **Parameter order**: Input parameters before output parameters

## Complexity Management

### Cyclomatic Complexity
- **Function complexity**: Keep below 15 (clang-tidy enforced)
- **Nested loops**: Avoid deep nesting; extract inner loops to functions
- **Condition chains**: Use early returns to reduce nesting
- **Switch statements**: Prefer over long if-else chains

### Cognitive Load
- **Clear data flow**: Make data transformations obvious
- **Meaningful abstractions**: Create abstractions that reduce mental overhead
- **Consistent patterns**: Use same patterns for similar problems
- **Progressive disclosure**: Start simple, add complexity only when needed

## Conclusion

These guidelines are derived from real-world experience implementing the Nej project and represent battle-tested practices for creating maintainable, safe, and efficient C++ code. They should be adapted to specific project needs while maintaining the core principles of safety, clarity, and maintainability.

**Remember**: The goal is code that is easy to read, modify, and debug by other developers (including your future self).