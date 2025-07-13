# TESTING_STANDARDS.md

Comprehensive testing standards and practices for reliable, maintainable C++ projects.

## Testing Philosophy

### Layered Testing Approach
1. **Unit Tests**: Fast, isolated tests of individual functions/classes
2. **Integration Tests**: End-to-end testing of complete workflows
3. **Performance Tests**: Basic regression detection for critical paths
4. **Error Path Testing**: Validation of error handling and edge cases

### Quality Metrics
- **Coverage Target**: 90%+ line coverage for production code
- **Test Isolation**: Each test runs independently and repeatably
- **Fast Feedback**: Unit test suite completes in < 5 seconds
- **Clear Diagnostics**: Failed tests provide actionable error messages

## Unit Testing with Google Test

### Test Framework Setup
```cmake
# CMakeLists.txt test configuration
find_package(GTest CONFIG REQUIRED)

add_executable(project_tests test_main.cpp)
target_link_libraries(project_tests 
    PRIVATE GTest::gtest_main 
    PRIVATE project_core
)

include(GoogleTest)
gtest_discover_tests(project_tests)
```

### Test Organization Patterns

#### Test Fixture Structure
```cpp
#include "gtest/gtest.h"
#include "../src/core.h"

// Test fixture for shared setup/teardown
class CoreFunctionsTest : public ::testing::Test {
protected:
    void SetUp() override {
        // Common setup code
    }
    
    void TearDown() override {
        // Common cleanup code
    }
    
    // Shared test data
    std::string sample_text = "Hello 👋 World!";
};

TEST_F(CoreFunctionsTest, RemovesEmojiCorrectly) {
    auto [result, count] = removeEmojis(sample_text);
    ASSERT_EQ(result, "Hello   World!");
    ASSERT_EQ(count, 1);
}
```

#### Descriptive Test Names
```cpp
// Good: Clearly describes what is being tested
TEST_F(RemoveEmojisTest, HandlesEmptyString) {
    ASSERT_EQ(removeEmojis("").first, "");
}

TEST_F(RemoveEmojisTest, RemovesSingleEmoji) {
    ASSERT_EQ(removeEmojis("Hello 👋 World!").first, "Hello   World!");
}

TEST_F(RemoveEmojisTest, HandlesEmojisWithDifferentByteLengths) {
    // Test 4-byte emoji (🚀) and 3-byte emoji (✅)
    ASSERT_EQ(removeEmojis("🚀Test✅").first, " Test ");
}

TEST_F(RemoveEmojisTest, DoesNotRemoveNonEmojis) {
    std::string non_emojis = "abcABC123!@#$%^&*()_+-=[]{}|;:'\",./<>?`~\n\t\r ";
    ASSERT_EQ(removeEmojis(non_emojis).first, non_emojis);
}
```

### Edge Case Testing Categories

#### Boundary Conditions
```cpp
TEST_F(RemoveEmojisTest, HandlesEmptyString) {
    ASSERT_EQ(removeEmojis("").first, "");
}

TEST_F(RemoveEmojisTest, HandlesWhitespaceOnly) {
    ASSERT_EQ(removeEmojis("   ").first, "   ");
}

TEST_F(RemoveEmojisTest, HandlesVeryLongStrings) {
    std::string long_text(10000, 'a');
    auto result = removeEmojis(long_text);
    ASSERT_EQ(result.first.length(), 10000);
    ASSERT_EQ(result.second, 0);  // No emojis removed
}
```

#### Malformed Input Handling
```cpp
TEST_F(RemoveEmojisTest, HandlesMalformedUTF8) {
    // Test string with invalid UTF-8 sequences
    std::string malformed = "Hello\x80\x81World";
    auto [result, count] = removeEmojis(malformed);
    
    // Should handle gracefully, replacing invalid sequences
    ASSERT_TRUE(result.find('?') != std::string::npos || 
                result == "HelloWorld");  // Either replacement or graceful skip
}

TEST_F(RemoveEmojisTest, HandlesPartialEmojiSequences) {
    // Test incomplete multi-byte sequences
    std::string partial = "Hello\xF0\x9F";  // Incomplete 4-byte sequence
    auto result = removeEmojis(partial);
    ASSERT_FALSE(result.first.empty());  // Should not crash
}
```

#### Complex Emoji Sequences
```cpp
TEST_F(RemoveEmojisTest, HandlesComplexEmojiSequences) {
    // Multi-codepoint emojis like family groups or flag sequences
    std::string complex = "👨‍👩‍👧‍👦 family";  // Family emoji with ZWJ sequences
    auto [result, count] = removeEmojis(complex);
    
    // Should remove the entire emoji sequence as one unit
    ASSERT_TRUE(result.find("family") != std::string::npos);
    ASSERT_GT(count, 0);
}
```

### Error Path Testing
```cpp
// Test file I/O error conditions
TEST_F(FileOperationsTest, HandlesNonExistentFile) {
    // This would be tested in integration tests since it involves file system
    std::string nonexistent = "/path/that/does/not/exist.txt";
    // Test should verify error handling, not crash
}

TEST_F(BinaryDetectionTest, CorrectlyIdentifiesBinaryFiles) {
    // Create test file with null bytes
    std::vector<char> binary_data = {'H', 'e', 'l', 'l', 'o', '\0', 'W', 'o', 'r', 'l', 'd'};
    
    // Write to temporary file and test detection
    // (Implementation would use temp file creation)
    
    ASSERT_TRUE(isBinary(temp_binary_file));
}
```

## Integration Testing

### Shell Script Integration Tests
```bash
#!/bin/bash
# tests/integration/test_line_by_line.sh

set -euo pipefail

# Test setup
TEST_DIR=$(mktemp -d -t nej_test_XXXXXX)
INPUT_FILE="${TEST_DIR}/input.txt"
NEJ_BIN="../../build/nej"

# Create test input
cat <<EOF > "${INPUT_FILE}"
Hello world!
This line has an emoji: 👋
Another line without.
And one more with multiple: ✨🐛📝
Final line.
EOF

# Test 1: Dry Run Mode
echo -n "Test 1: Dry Run Mode... "
DRY_RUN_OUTPUT=$("${NEJ_BIN}" --dry-run "${INPUT_FILE}" 2>&1)
EXPECTED_OUTPUT="File: \"${INPUT_FILE}\", Emojis removed: 4"

if [[ "${DRY_RUN_OUTPUT}" == "${EXPECTED_OUTPUT}" ]]; then
    echo "PASS"
else
    echo "FAIL"
    echo "Expected: '${EXPECTED_OUTPUT}'"
    echo "Actual:   '${DRY_RUN_OUTPUT}'"
    exit 1
fi

# Test 2: In-Place Modification
echo -n "Test 2: In-Place Modification... "
"${NEJ_BIN}" -i.bak "${INPUT_FILE}"

EXPECTED_CONTENT=$(cat <<EOF
Hello world!
This line has an emoji:  
Another line without.
And one more with multiple:    
Final line.
EOF
)

ACTUAL_CONTENT=$(cat "${INPUT_FILE}")
if [[ "${ACTUAL_CONTENT}" == "${EXPECTED_CONTENT}" ]]; then
    echo "PASS"
else
    echo "FAIL"
    exit 1
fi

# Cleanup
rm -rf "${TEST_DIR}"
echo "All integration tests passed."
```

### CMake Integration Test Registration
```cmake
# Add integration test to CTest
add_test(
    NAME IntegrationTest_LineByLine 
    COMMAND ${CMAKE_SOURCE_DIR}/tests/integration/test_line_by_line.sh
)

# Set working directory and timeout
set_tests_properties(IntegrationTest_LineByLine PROPERTIES
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
    TIMEOUT 30
)
```

## Test Data Management

### Structured Test Data
```cpp
// Create reusable test data sets
class EmojiTestData {
public:
    static std::vector<std::pair<std::string, std::string>> getTestCases() {
        return {
            {"Hello 👋 World!", "Hello   World!"},
            {"✨🐛📝", "   "},
            {"No emojis here", "No emojis here"},
            {"Mixed 👋 content 🌍 here", "Mixed   content   here"}
        };
    }
    
    static std::vector<std::string> getBinaryFilePatterns() {
        return {
            std::string("Hello\x00World"),  // Null byte
            std::string("\xFF\xFE"),         // BOM marker
            std::string("\x7F\x45\x4C\x46") // ELF header
        };
    }
};

// Parameterized tests for systematic coverage
class EmojiRemovalParameterizedTest : 
    public ::testing::TestWithParam<std::pair<std::string, std::string>> {};

TEST_P(EmojiRemovalParameterizedTest, ProcessesCorrectly) {
    auto [input, expected] = GetParam();
    auto [result, count] = removeEmojis(input);
    ASSERT_EQ(result, expected);
}

INSTANTIATE_TEST_SUITE_P(
    EmojiTestCases,
    EmojiRemovalParameterizedTest,
    ::testing::ValuesIn(EmojiTestData::getTestCases())
);
```

### File-Based Test Resources
```cpp
// Load test data from files
class FileTestData {
    static std::string loadTestFile(const std::string& filename) {
        std::ifstream file(std::string("tests/data/") + filename);
        return std::string((std::istreambuf_iterator<char>(file)),
                          std::istreambuf_iterator<char>());
    }
    
public:
    static std::string getLargeEmojiText() {
        return loadTestFile("large_emoji_sample.txt");
    }
    
    static std::string getUnicodeEdgeCases() {
        return loadTestFile("unicode_edge_cases.txt");
    }
};
```

## Performance Testing

### Basic Performance Regression Tests
```cpp
#include <chrono>

TEST_F(PerformanceTest, ProcessesLargeFileReasonably) {
    // Create large test input
    std::string large_input;
    large_input.reserve(1000000);  // 1MB
    
    for (int i = 0; i < 100000; ++i) {
        large_input += "Hello 👋 World! ";
    }
    
    auto start = std::chrono::high_resolution_clock::now();
    auto [result, count] = removeEmojis(large_input);
    auto end = std::chrono::high_resolution_clock::now();
    
    auto duration = std::chrono::duration_cast<std::chrono::milliseconds>(end - start);
    
    // Should process 1MB in reasonable time (adjust threshold based on requirements)
    ASSERT_LT(duration.count(), 1000);  // Less than 1 second
    ASSERT_EQ(count, 100000);  // Correct emoji count
}
```

### Memory Usage Testing
```cpp
TEST_F(PerformanceTest, DoesNotLeakMemory) {
    // Measure baseline memory
    size_t initial_memory = getCurrentMemoryUsage();
    
    // Perform many operations
    for (int i = 0; i < 10000; ++i) {
        auto [result, count] = removeEmojis("Test 👋 string 🌍");
        // Result goes out of scope - should be cleaned up
    }
    
    size_t final_memory = getCurrentMemoryUsage();
    
    // Memory usage should not grow significantly
    ASSERT_LT(final_memory - initial_memory, 1024 * 1024);  // Less than 1MB growth
}
```

## Test Execution and Reporting

### Running Test Suites
```bash
# All tests (unit + integration)
cd build && ctest

# Unit tests only
cd build && ./project_tests

# Integration tests only
cd tests/integration && ./test_line_by_line.sh

# Specific test filter
cd build && ./project_tests --gtest_filter=RemoveEmojisTest.*

# Verbose output
cd build && ctest --verbose

# Parallel execution
cd build && ctest --parallel 4
```

### Continuous Integration Configuration
```yaml
# Example GitHub Actions workflow
name: Tests
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    
    - name: Install dependencies
      run: |
        sudo apt-get update
        sudo apt-get install -y cmake build-essential
        
    - name: Configure CMake
      run: cmake -B build -DCMAKE_BUILD_TYPE=Debug
      
    - name: Build
      run: cmake --build build
      
    - name: Run tests
      run: cd build && ctest --output-on-failure
```

## Test Coverage Analysis

### Coverage Tools Integration
```bash
# Using gcov/lcov for coverage analysis
cmake -B build-coverage -DCMAKE_BUILD_TYPE=Debug -DCMAKE_CXX_FLAGS="--coverage"
cmake --build build-coverage
cd build-coverage && ctest
lcov --capture --directory . --output-file coverage.info
genhtml coverage.info --output-directory coverage_html
```

### Coverage Exclusions
```cpp
// Mark auto-generated or trivial code for coverage exclusion
// LCOV_EXCL_START
auto generatedFunction() -> int {
    // Auto-generated code that doesn't need coverage
    return 42;
}
// LCOV_EXCL_STOP

// Single line exclusion
throw std::runtime_error("This should never happen"); // LCOV_EXCL_LINE
```

## Best Practices Summary

### Test Writing Guidelines
✅ **Use descriptive test names** that explain what is being tested
✅ **Test edge cases and error conditions** systematically
✅ **Keep tests simple and focused** - one concept per test
✅ **Use test fixtures** for shared setup/teardown
✅ **Parameterize similar tests** to avoid duplication
✅ **Test both success and failure paths**
✅ **Include performance regression tests**

### Test Organization
✅ **Separate unit and integration tests** clearly
✅ **Group related tests** in test fixtures or test suites
✅ **Use consistent naming conventions**
✅ **Keep test data organized** and reusable
✅ **Document complex test scenarios**

### Maintenance Practices
✅ **Run tests frequently** during development
✅ **Keep tests green** - fix failing tests immediately
✅ **Update tests** when changing functionality
✅ **Review test coverage** regularly
✅ **Clean up test code** with same standards as production code

This testing approach ensures reliable software delivery with comprehensive validation of functionality, error handling, and performance characteristics.