# Contributing to AStockD

Thank you for your interest in contributing to AStockD! We welcome contributions from the community and are grateful for your support.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [How Can I Contribute?](#how-can-i-contribute)
- [Reporting Bugs](#reporting-bugs)
- [Suggesting Features](#suggesting-features)
- [Pull Request Process](#pull-request-process)
- [Development Setup](#development-setup)
- [Coding Standards](#coding-standards)

---

## Code of Conduct

This project and everyone participating in it is governed by our [Code of Conduct](CODE_OF_CONDUCT.md). By participating, you are expected to uphold this code.

---

## How Can I Contribute?

### 🐛 Reporting Bugs

Before creating a bug report, please check existing issues to avoid duplicates.

**How to submit a good bug report:**

1. Use a clear and descriptive title
2. Describe the exact steps to reproduce the problem
3. Provide specific examples (code snippets, API calls)
4. Describe the behavior you observed and what you expected
5. Include environment details (Python version, OS, etc.)
6. Add screenshots or logs if applicable

**Template:**
```markdown
**Description:** Brief description of the bug

**Steps to Reproduce:**
1. Step one
2. Step two
3. ...

**Expected Behavior:** What should happen

**Actual Behavior:** What actually happens

**Environment:**
- Python version:
- OS:
- AStockD version:

**Additional Context:** Any other relevant information
```

---

### 💡 Suggesting Features

We welcome feature suggestions! Please:

1. Check if the feature has already been requested
2. Provide a clear use case
3. Explain why this feature would be useful to most users
4. Consider how it fits with the project's goals

**Template:**
```markdown
**Feature Description:** Clear description of the feature

**Use Case:** Why you need this feature

**Proposed Solution:** How you envision it working

**Alternatives:** Other approaches you've considered
```

---

## Pull Request Process

### Before You Start

1. **Fork the repository** and create your branch from `main`
2. **Check existing PRs** to avoid duplicate work
3. **Open an issue first** for major changes to discuss the approach

### Making Changes

1. **Create a feature branch:**
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. **Make your changes** following our coding standards

3. **Write tests** for new functionality

4. **Update documentation** if you're changing APIs or adding features

5. **Test your changes:**
   ```bash
   # Run tests
   pytest tests/
   
   # Check code style
   flake8 .
   black --check .
   ```

### Submitting the PR

1. **Commit your changes** with clear, descriptive messages:
   ```bash
   git commit -m "feat: add natural language query caching"
   ```

2. **Push to your fork:**
   ```bash
   git push origin feature/your-feature-name
   ```

3. **Create a Pull Request** with:
   - A clear title and description
   - Reference to related issues (e.g., "Fixes #123")
   - Description of changes and why they're needed
   - Screenshots or examples if applicable

4. **Wait for review** - maintainers will review your PR and may request changes

### PR Requirements

- [ ] Code follows the project's style guidelines
- [ ] Self-review completed
- [ ] Comments added for complex logic
- [ ] Documentation updated (if applicable)
- [ ] Tests added/updated (if applicable)
- [ ] No breaking changes (or clearly documented)
- [ ] Commit messages follow conventional commits

---

## Development Setup

### Prerequisites

- Python 3.8 or higher
- pip or poetry
- Git

### Setup Instructions

1. **Clone your fork:**
   ```bash
   git clone https://github.com/YOUR_USERNAME/AStockD.git
   cd AStockD
   ```

2. **Create a virtual environment:**
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   pip install -r requirements-dev.txt  # Development dependencies
   ```

4. **Run tests to verify setup:**
   ```bash
   pytest tests/
   ```

### Project Structure

```
AStockD/
├── docs/              # Documentation
│   ├── api-reference.md
│   ├── ai-integration.md
│   ├── features.md
│   ├── quick-start.md
│   └── examples/
├── skills/            # Agent skill definitions
│   └── stock-analysis/
├── openapi.yaml       # OpenAPI spec
└── ...
```

---

## Coding Standards

### Python Style Guide

- Follow [PEP 8](https://pep8.org/)
- Use [Black](https://black.readthedocs.io/) for formatting
- Use [flake8](https://flake8.pycqa.org/) for linting
- Maximum line length: 100 characters

### Code Quality

- Write clear, self-documenting code
- Add docstrings for all public functions and classes
- Keep functions small and focused (single responsibility)
- Use type hints where applicable

**Example:**
```python
def analyze_stock(stock_code: str, features: List[str]) -> Dict[str, Any]:
    """
    Analyze a stock with specified features.
    
    Args:
        stock_code: Stock code (e.g., "600519")
        features: List of feature names to analyze
    
    Returns:
        Dictionary containing analysis results
    
    Raises:
        ValueError: If stock_code is invalid
    """
    # Implementation
    pass
```

### Commit Messages

Follow [Conventional Commits](https://www.conventionalcommits.org/):

- `feat:` New feature
- `fix:` Bug fix
- `docs:` Documentation changes
- `style:` Code style changes (formatting)
- `refactor:` Code refactoring
- `test:` Test additions or changes
- `chore:` Build process or auxiliary tool changes

**Examples:**
```
feat: add stock sentiment analysis endpoint
fix: correct money flow calculation for edge cases
docs: update API reference with new parameters
```

---

## Testing

- Write unit tests for new functionality
- Ensure all tests pass before submitting PR
- Aim for at least 80% code coverage
- Test edge cases and error conditions

```bash
# Run all tests
pytest tests/

# Run with coverage
pytest --cov=src tests/

# Run specific test file
pytest tests/test_analysis.py
```

---

## Documentation

- Update README.md if you're adding user-facing features
- Add/update docstrings for code changes
- Update API documentation in `docs/api-reference.md`
- Add examples in `docs/examples/`

---

## Questions?

- Open an issue with the "question" label
- Check existing issues and discussions
- Review the [documentation](docs/)

---

## Recognition

Contributors will be recognized in our README and release notes. Thank you for making AStockD better! 🎉

---

**Happy Contributing!** 🚀
