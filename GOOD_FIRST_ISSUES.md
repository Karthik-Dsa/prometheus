# Good First Issues for Prometheus Contributors

Welcome to Prometheus! This guide helps first-time contributors find beginner-friendly issues and contribution opportunities.

## Quick Start

Before diving in, please:
1. Read [CONTRIBUTING.md](CONTRIBUTING.md) - Required reading for all contributors
2. Check the [`low-hanging-fruit`](https://github.com/prometheus/prometheus/issues?q=is%3Aissue+is%3Aopen+label%3A%22low+hanging+fruit%22) label on GitHub
3. Join the community on [IRC](https://prometheus.io/community) for quick help
4. Sign the [DCO](https://github.com/probot/dco#how-it-works) for all commits

## Build & Test Locally

```bash
# Build Prometheus
go build ./cmd/prometheus/

# Run tests
make test

# Run linters
make lint
```

---

## 🎯 Beginner-Friendly Contribution Categories

### 1. 📝 Documentation & Examples

**Why Great for Beginners**: No complex code changes, immediate value, easy to verify.

**Opportunities**:
- Fix typos or grammatical errors in documentation
- Improve clarity of existing docs (make them easier to understand)
- Add missing examples to `documentation/examples/`
- Update outdated screenshots or diagrams
- Improve README files in subdirectories (discovery, tsdb, etc.)
- Add code comments to complex functions
- Write godoc comments for exported functions

**Example Files to Improve**:
```
docs/getting_started.md
docs/configuration/
documentation/examples/
*/README.md files in component directories
```

**Tips**:
- Look for sections that confused you when learning
- Ask yourself: "Would this be clearer with an example?"
- Check if configuration options are documented

---

### 2. ✅ Tests & Test Coverage

**Why Great for Beginners**: Clear patterns, isolated changes, immediate feedback.

**Opportunities**:
- Add missing test cases for existing functions
- Improve test coverage in under-tested packages
- Add edge case tests (empty strings, nil values, boundary conditions)
- Write table-driven tests for functions with multiple scenarios
- Add benchmark tests for performance-critical code
- Improve test error messages to be more descriptive

**Example Test Patterns**:
```go
func TestFunctionName(t *testing.T) {
    tests := []struct {
        name     string
        input    string
        expected string
    }{
        {name: "empty string", input: "", expected: ""},
        {name: "normal case", input: "test", expected: "test"},
    }
    
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            result := FunctionName(tt.input)
            require.Equal(t, tt.expected, result)
        })
    }
}
```

**Where to Add Tests**:
```
promql/parser/*_test.go
util/*_test.go
config/*_test.go
```

---

### 3. 💬 Error Messages & User Experience

**Why Great for Beginners**: Improves user experience, self-contained changes.

**Opportunities**:
- Make error messages more descriptive and actionable
- Add suggestions to error messages (e.g., "did you mean X?")
- Improve validation error messages in configuration parsing
- Add better context to parser errors
- Make warning messages more informative

**Example Improvements**:
```go
// Before
return errors.New("invalid value")

// After
return fmt.Errorf("invalid value %q: must be between 1 and 100", value)
```

**Files to Explore**:
```
config/config.go - Configuration validation
promql/parser/parse.go - Parser errors
*/validation.go - Various validation logic
```

---

### 4. 🔧 Utility Functions & Helper Code

**Why Great for Beginners**: Small, focused, easily testable.

**Opportunities**:
- Add helper functions to reduce code duplication
- Improve string formatting utilities
- Add convenience methods to existing types
- Refactor repeated code into reusable functions
- Add validation helpers

**Example Areas**:
```
util/strutil/ - String utilities
util/testutil/ - Test helpers
model/labels/ - Label manipulation
```

---

### 5. 🐛 Bug Fixes (Low Complexity)

**Why Great for Beginners**: Real impact, clear problem statement.

**Look For**:
- Off-by-one errors
- Incorrect default values
- Missing nil checks
- Resource leaks in tests
- Race conditions in test code
- Incorrect error handling

**Finding Bugs**:
- Check issues labeled `bug` and `low-hanging-fruit`
- Run tests with race detector: `go test -race ./...`
- Run static analysis tools: `make lint`

---

### 6. 🎨 Code Quality Improvements

**Why Great for Beginners**: Learn best practices, gradual improvements.

**Opportunities**:
- Simplify complex conditional logic
- Extract magic numbers into named constants
- Improve variable naming for clarity
- Add TODO comments for future improvements
- Fix linter warnings
- Remove unused code or variables

**Example**:
```go
// Before
if x > 100 && x < 1000 {
    // ...
}

// After
const (
    minValue = 100
    maxValue = 1000
)
if x > minValue && x < maxValue {
    // ...
}
```

---

## 🎓 Learning Path for Contributors

### Level 1: Documentation & Comments (Start Here!)
- Fix typos in documentation
- Add comments to confusing code
- Improve README files

### Level 2: Tests & Validation
- Add missing test cases
- Improve test coverage
- Write validation tests

### Level 3: Small Bug Fixes
- Fix issues labeled `low-hanging-fruit`
- Improve error messages
- Add helper functions

### Level 4: Feature Enhancements
- Add new utility functions
- Implement feature requests (with maintainer guidance)
- Improve existing features

---

## 📋 Finding Issues

### GitHub Labels to Watch
- [`low-hanging-fruit`](https://github.com/prometheus/prometheus/issues?q=is%3Aissue+is%3Aopen+label%3A%22low+hanging+fruit%22) - Beginner-friendly issues
- `good first issue` - Explicitly marked for newcomers
- `help wanted` - Maintainers looking for contributors
- `documentation` - Documentation improvements
- `bug` - Bug fixes (filter by difficulty)

### Areas to Explore
1. **Documentation**: `docs/`, `documentation/`, README files
2. **Examples**: `documentation/examples/`
3. **Tests**: `*_test.go` files throughout the codebase
4. **Utilities**: `util/` directory
5. **Configuration**: `config/` directory

---

## 💡 Tips for Success

### Before You Start
✅ **Claim the issue**: Comment on the GitHub issue to avoid duplicate work  
✅ **Ask questions**: Unclear requirements? Ask maintainers or on IRC  
✅ **Start small**: Your first PR should be tiny and focused  
✅ **Read existing code**: Understand the patterns before changing them  

### While Working
✅ **Follow existing patterns**: Match the style of surrounding code  
✅ **Write tests**: Add tests for your changes when applicable  
✅ **Keep it minimal**: Smallest possible change to fix the issue  
✅ **Test locally**: Run `make test` and `make lint` before submitting  

### Submitting Your PR
✅ **Reference the issue**: Use "Fixes #issue-number" in your PR description  
✅ **Sign your commits**: Required for DCO compliance  
✅ **One commit per logical change**: Keep commits focused  
✅ **Respond to feedback**: Be receptive to review comments  

---

## 🔍 Example First Contributions

### Example 1: Fix a Typo
**Difficulty**: ⭐ Very Easy  
**Files**: Any `.md` file  
**Impact**: Improves documentation quality  

### Example 2: Add a Test Case
**Difficulty**: ⭐⭐ Easy  
**Files**: `*_test.go`  
**Impact**: Increases code coverage and reliability  

### Example 3: Improve an Error Message
**Difficulty**: ⭐⭐ Easy  
**Files**: Various (config, parser, etc.)  
**Impact**: Better user experience  

### Example 4: Add Helper Function
**Difficulty**: ⭐⭐⭐ Medium  
**Files**: `util/` directory  
**Impact**: Reduces code duplication  

### Example 5: Fix a Small Bug
**Difficulty**: ⭐⭐⭐ Medium  
**Files**: Varies based on bug  
**Impact**: Direct bug fix  

---

## 📚 Useful Resources

- **Main Documentation**: https://prometheus.io/docs/
- **Go Code Review Comments**: https://go.dev/wiki/CodeReviewComments
- **Prometheus Community**: https://prometheus.io/community/
- **IRC Channel**: #prometheus-dev on irc.libera.chat
- **Mailing List**: https://groups.google.com/forum/#!forum/prometheus-developers

---

## ❓ Common Questions

### Q: I found a typo. Do I need to create an issue first?
**A**: For trivial fixes (typos, formatting), you can submit a PR directly. For anything more substantial, create an issue or discuss on IRC first.

### Q: How long should I wait for review?
**A**: Maintainers volunteer their time. Be patient. If no response after a week, politely ping on the PR or ask on IRC.

### Q: My PR was rejected. What should I do?
**A**: Don't be discouraged! Ask for clarification, understand the feedback, and either revise your approach or try a different issue.

### Q: Can I work on multiple issues at once?
**A**: Best to focus on one issue at a time, especially as a beginner. This makes reviews easier and faster.

### Q: What if I can't finish an issue I claimed?
**A**: That's okay! Comment on the issue letting others know you're stepping away so someone else can pick it up.

---

## 🎉 Welcome to the Prometheus Community!

Remember: Every expert contributor started as a beginner. Don't be afraid to ask questions, make mistakes, and learn. The Prometheus community is here to help you succeed.

**Your first contribution matters** - whether it's fixing a typo or adding a test, you're making Prometheus better for everyone.

Happy contributing! 🚀
