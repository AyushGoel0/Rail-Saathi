# Contributing to Rail-Saathi

Thank you for your interest in contributing to Rail-Saathi! This document provides guidelines and instructions for contributing to the project.

## 🎯 Ways to Contribute

- **Report bugs** - Found an issue? Let us know!
- **Suggest features** - Have ideas for improvement?
- **Improve documentation** - Help make our docs clearer
- **Submit code** - Fix bugs or add features
- **Review pull requests** - Help review others' code

## 🚀 Getting Started

### 1. Fork and Clone

```bash
# Fork the repository on GitHub, then:
git clone https://github.com/YOUR_USERNAME/Rail-Saathi.git
cd Rail-Saathi
```

### 2. Set Up Development Environment

Follow the [Setup Guide](docs/SETUP.md) to install dependencies and configure your environment.

### 3. Create a Branch

```bash
# Create a new branch for your feature/fix
git checkout -b feature/your-feature-name
# or
git checkout -b fix/bug-description
```

**Branch Naming Convention:**
- `feature/` - New features
- `fix/` - Bug fixes
- `docs/` - Documentation updates
- `refactor/` - Code refactoring
- `test/` - Adding tests

## 📝 Development Workflow

### 1. Make Your Changes

- Write clean, readable code
- Follow existing code style
- Add comments for complex logic
- Update documentation if needed

### 2. Test Your Changes

```bash
# Run the application
flask run

# Test your changes manually
# Ensure no existing functionality is broken
```

### 3. Commit Your Changes

```bash
git add .
git commit -m "Type: Brief description of changes"
```

**Commit Message Format:**
```
Type: Brief description (50 chars or less)

More detailed explanation if needed (wrap at 72 chars)
- What was changed
- Why the change was made
- Any breaking changes

Fixes #123
```

**Commit Types:**
- `Feat:` - New feature
- `Fix:` - Bug fix
- `Docs:` - Documentation changes
- `Style:` - Code style changes (formatting, etc.)
- `Refactor:` - Code refactoring
- `Test:` - Adding or updating tests
- `Chore:` - Maintenance tasks

**Examples:**
```
Feat: Add train fare calculation feature

Fix: Resolve login authentication issue
- Fixed password validation logic
- Updated session handling
Fixes #45

Docs: Update API documentation with new endpoints
```

### 4. Push Your Changes

```bash
git push origin feature/your-feature-name
```

### 5. Create Pull Request

1. Go to your fork on GitHub
2. Click "New Pull Request"
3. Select your branch
4. Fill in the PR template
5. Submit for review

## 📋 Pull Request Guidelines

### PR Title Format

```
[Type] Brief description
```

Examples:
- `[Feature] Add password reset functionality`
- `[Fix] Resolve train search API timeout`
- `[Docs] Update deployment instructions`

### PR Description Template

```markdown
## Description
Brief description of what this PR does

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Documentation update
- [ ] Code refactoring
- [ ] Other (please describe):

## Changes Made
- Change 1
- Change 2
- Change 3

## Testing
How has this been tested?
- [ ] Manual testing
- [ ] Unit tests
- [ ] Integration tests

## Screenshots (if applicable)
Add screenshots to help explain your changes

## Checklist
- [ ] Code follows project style guidelines
- [ ] Self-reviewed my own code
- [ ] Commented code where needed
- [ ] Updated documentation
- [ ] No new warnings generated
- [ ] All tests pass

## Related Issues
Fixes #(issue number)
Related to #(issue number)
```

## 🎨 Code Style Guidelines

### Python Code Style

Follow **PEP 8** guidelines:

```python
# Good
def fetch_train_data(station_code, date):
    """
    Fetch train data from API.
    
    Args:
        station_code (str): Station code
        date (str): Date in YYYY-MM-DD format
        
    Returns:
        dict: Train data response
    """
    if not station_code or not date:
        return None
    
    response = api_call(station_code, date)
    return response

# Avoid
def FetchTrainData(stationcode,date):
    if not stationcode or not date:return None
    resp=api_call(stationcode,date)
    return resp
```

**Key Points:**
- Use 4 spaces for indentation (no tabs)
- Max line length: 79 characters for code, 72 for comments
- Use descriptive variable names
- Add docstrings to functions and classes
- Use type hints where applicable

### HTML/Jinja2 Templates

```html
<!-- Good -->
<div class="container mt-5">
    {% if user_logged_in %}
        <h1>Welcome, {{ user.username }}!</h1>
    {% else %}
        <h1>Welcome, Guest!</h1>
    {% endif %}
</div>

<!-- Avoid -->
<div class=container mt-5>
{% if user_logged_in %}<h1>Welcome, {{user.username}}!</h1>
{% else %}<h1>Welcome, Guest!</h1>{% endif %}</div>
```

### CSS Style

```css
/* Good */
.train-card {
    padding: 1rem;
    margin-bottom: 1rem;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

/* Avoid */
.train-card{padding:1rem;margin-bottom:1rem;border-radius:8px;box-shadow:0 2px 4px rgba(0,0,0,0.1);}
```

## 🐛 Reporting Bugs

### Before Reporting

1. Check if the bug has already been reported
2. Try to reproduce the issue
3. Collect relevant information

### Bug Report Template

```markdown
**Describe the Bug**
A clear and concise description of what the bug is.

**To Reproduce**
Steps to reproduce the behavior:
1. Go to '...'
2. Click on '...'
3. Scroll down to '...'
4. See error

**Expected Behavior**
What you expected to happen.

**Actual Behavior**
What actually happened.

**Screenshots**
If applicable, add screenshots.

**Environment:**
- OS: [e.g., Windows 11, Ubuntu 22.04]
- Python Version: [e.g., 3.9.5]
- Browser: [e.g., Chrome 95, Firefox 93]

**Additional Context**
Any other relevant information.

**Error Messages/Logs**
```
Paste error messages or relevant log output here
```
```

## 💡 Suggesting Features

### Feature Request Template

```markdown
**Feature Description**
Clear description of the feature you'd like to see.

**Problem It Solves**
What problem does this feature solve?

**Proposed Solution**
How would you implement this feature?

**Alternatives Considered**
Any alternative solutions you've thought about?

**Additional Context**
Any other relevant information, mockups, or examples.

**Priority**
- [ ] High
- [ ] Medium
- [ ] Low
```

## 📚 Documentation Contributions

### Types of Documentation

- **Code Comments** - Inline explanations
- **Docstrings** - Function/class documentation
- **README.md** - Project overview
- **docs/** - Detailed guides
- **API Documentation** - Endpoint references

### Documentation Guidelines

1. **Be Clear and Concise**
   - Use simple language
   - Avoid jargon where possible
   - Explain technical terms

2. **Provide Examples**
   - Include code examples
   - Show expected output
   - Demonstrate common use cases

3. **Keep Updated**
   - Update docs when code changes
   - Remove outdated information
   - Verify links still work

4. **Use Proper Formatting**
   - Follow markdown conventions
   - Use consistent heading levels
   - Format code blocks properly

## 🧪 Testing Guidelines

### Manual Testing

Before submitting a PR, test:

1. **Your Changes**
   - New feature works as intended
   - Bug fix resolves the issue

2. **Existing Functionality**
   - User registration/login
   - Train search
   - Booking creation
   - Dashboard access

3. **Edge Cases**
   - Empty inputs
   - Invalid data
   - Network failures

### Writing Tests (Future)

When test suite is added:

```python
# Example test structure
def test_user_registration():
    """Test user can register successfully."""
    user_data = {
        'username': 'testuser',
        'email': 'test@example.com',
        'password': 'SecurePass123!'
    }
    response = register_user(user_data)
    assert response.status_code == 201
    assert 'success' in response.json()['message']
```

## 🔍 Code Review Process

### For Contributors

- Be patient - reviewers are volunteers
- Respond to feedback constructively
- Make requested changes promptly
- Ask questions if feedback is unclear

### For Reviewers

- Be respectful and constructive
- Explain why changes are needed
- Approve quickly if code is good
- Request changes if necessary

## 🏆 Recognition

Contributors will be:
- Listed in README.md acknowledgments
- Credited in release notes
- Thanked in commit messages

## ❓ Questions?

- **Documentation**: Check [docs/](docs/) folder
- **GitHub Issues**: Open an issue with `question` label
- **Team Chat**: Ask in project communication channel
- **Email**: Contact maintainers (see README.md)

## 📄 License

By contributing to Rail-Saathi, you agree that your contributions will be licensed under the MIT License.

## 🙏 Thank You!

Every contribution, no matter how small, makes Rail-Saathi better. Thank you for being part of this project!

---

**Last Updated:** October 2025  
**Need Help?** Open an issue or contact the maintainers.
