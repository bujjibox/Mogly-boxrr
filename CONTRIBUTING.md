# Contributing to Mogly-boxrr

Thank you for your interest in contributing to Mogly-boxrr! We welcome contributions from everyone.

## 📋 Table of Contents

1. [Code of Conduct](#code-of-conduct)
2. [Getting Started](#getting-started)
3. [How to Contribute](#how-to-contribute)
4. [Development Workflow](#development-workflow)
5. [Coding Standards](#coding-standards)
6. [Commit Guidelines](#commit-guidelines)
7. [Pull Request Process](#pull-request-process)
8. [Testing](#testing)
9. [Documentation](#documentation)

## 🤝 Code of Conduct

By participating in this project, you agree to abide by our Code of Conduct:

- Be respectful and inclusive
- Welcome newcomers and help them learn
- Focus on what is best for the community
- Show empathy towards other community members

## 🚀 Getting Started

### Prerequisites

Before contributing, make sure you have:

- [.NET SDK 6.0+](https://dotnet.microsoft.com/download)
- [Git](https://git-scm.com/)
- A GitHub account
- Your preferred code editor (VS Code, Visual Studio, Rider, etc.)

### Setting Up Your Development Environment

1. **Fork the repository** on GitHub

2. **Clone your fork**:
   ```bash
   git clone https://github.com/YOUR-USERNAME/Mogly-boxrr.git
   cd Mogly-boxrr
   ```

3. **Add upstream remote**:
   ```bash
   git remote add upstream https://github.com/bujjibox/Mogly-boxrr.git
   ```

4. **Install dependencies**:
   ```bash
   dotnet restore
   ```

5. **Build the project**:
   ```bash
   dotnet build
   ```

6. **Run tests**:
   ```bash
   dotnet test
   ```

## 💡 How to Contribute

There are many ways to contribute:

### Reporting Bugs

Before creating a bug report:
- Check if the bug has already been reported
- Collect information about the bug
- Try to reproduce the bug with the latest version

When reporting a bug, include:
- Clear, descriptive title
- Steps to reproduce
- Expected vs actual behavior
- Screenshots if applicable
- Environment details (.NET version, OS, etc.)

### Suggesting Enhancements

Enhancement suggestions are welcome! Please:
- Use a clear, descriptive title
- Provide detailed description of the enhancement
- Explain why this enhancement would be useful
- Include examples if possible

### Code Contributions

1. Check existing issues or create a new one
2. Comment on the issue to let others know you're working on it
3. Fork and create a branch
4. Make your changes
5. Submit a pull request

## 🔄 Development Workflow

### Creating a Branch

Create a branch for your work:

```bash
# Update your local main branch
git checkout main
git pull upstream main

# Create a new branch
git checkout -b feature/your-feature-name
# or
git checkout -b fix/bug-description
```

Branch naming conventions:
- `feature/` - New features
- `fix/` - Bug fixes
- `docs/` - Documentation changes
- `refactor/` - Code refactoring
- `test/` - Test additions or modifications

### Making Changes

1. **Write clean, readable code**
2. **Follow coding standards** (see below)
3. **Add tests** for new functionality
4. **Update documentation** as needed
5. **Keep commits focused** and atomic

### Keeping Your Branch Updated

```bash
# Fetch latest changes from upstream
git fetch upstream

# Rebase your branch on upstream/main
git rebase upstream/main

# If there are conflicts, resolve them and continue
git add .
git rebase --continue
```

## 📝 Coding Standards

### General Guidelines

- Write clear, self-documenting code
- Keep methods small and focused (Single Responsibility Principle)
- Use meaningful names for variables, methods, and classes
- Add comments only when necessary to explain "why", not "what"
- Follow SOLID principles

### C# Style Guide

```csharp
// Good naming conventions
public class UserRepository : IRepository<User>
{
    private readonly ILogger _logger;
    private readonly DbContext _context;
    
    public UserRepository(ILogger logger, DbContext context)
    {
        _logger = logger;
        _context = context;
    }
    
    public async Task<User?> GetByIdAsync(int userId)
    {
        if (userId <= 0)
            throw new ArgumentException("User ID must be positive", nameof(userId));
            
        return await _context.Users.FindAsync(userId);
    }
}
```

### Code Formatting

Use the built-in .NET code formatter:

```bash
# Format code
dotnet format

# Check formatting without making changes
dotnet format --verify-no-changes
```

### Naming Conventions

- **Classes/Interfaces**: PascalCase (e.g., `UserService`, `IRepository`)
- **Methods**: PascalCase (e.g., `GetUserById`)
- **Properties**: PascalCase (e.g., `UserName`)
- **Private fields**: _camelCase (e.g., `_logger`)
- **Local variables**: camelCase (e.g., `userId`)
- **Constants**: PascalCase (e.g., `MaxRetryCount`)

## 📦 Commit Guidelines

### Commit Message Format

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting, etc.)
- `refactor`: Code refactoring
- `test`: Adding or updating tests
- `chore`: Maintenance tasks

**Examples:**

```
feat(auth): add JWT token authentication

Implement JWT-based authentication with refresh tokens.
Includes middleware for token validation and user claims.

Closes #123
```

```
fix(repository): resolve null reference in GetByIdAsync

Add null check before accessing repository context.

Fixes #456
```

### Commit Best Practices

- Make atomic commits (one logical change per commit)
- Write clear, concise commit messages
- Reference issue numbers when applicable
- Don't commit generated files or build artifacts

## 🔍 Pull Request Process

### Before Submitting

Ensure your PR:
- ✅ Builds successfully
- ✅ Passes all tests
- ✅ Follows coding standards
- ✅ Includes tests for new functionality
- ✅ Updates documentation if needed
- ✅ Has a clear description

### Creating a Pull Request

1. **Push your branch** to your fork:
   ```bash
   git push origin feature/your-feature-name
   ```

2. **Create PR** on GitHub

3. **Fill out the PR template**:
   - Clear title describing the change
   - Description of what changed and why
   - Link to related issues
   - Screenshots if UI changes

4. **Request review** from maintainers

### PR Review Process

- Maintainers will review your PR
- Address any requested changes
- Once approved, your PR will be merged

### After Your PR is Merged

1. **Delete your branch**:
   ```bash
   git branch -d feature/your-feature-name
   git push origin --delete feature/your-feature-name
   ```

2. **Update your local main**:
   ```bash
   git checkout main
   git pull upstream main
   ```

## 🧪 Testing

### Running Tests

```bash
# Run all tests
dotnet test

# Run tests with coverage
dotnet test /p:CollectCoverage=true

# Run specific test project
dotnet test tests/Mogly.Tests/
```

### Writing Tests

- Write unit tests for all public methods
- Use meaningful test names: `MethodName_Scenario_ExpectedResult`
- Follow AAA pattern: Arrange, Act, Assert
- Mock external dependencies

Example:

```csharp
[Fact]
public async Task GetByIdAsync_ExistingUser_ReturnsUser()
{
    // Arrange
    var userId = 1;
    var expectedUser = new User { Id = userId, Name = "Test" };
    var repository = CreateRepository(expectedUser);
    
    // Act
    var result = await repository.GetByIdAsync(userId);
    
    // Assert
    Assert.NotNull(result);
    Assert.Equal(expectedUser.Id, result.Id);
    Assert.Equal(expectedUser.Name, result.Name);
}
```

## 📚 Documentation

### Code Documentation

- Add XML documentation comments for public APIs
- Include examples in documentation when helpful
- Keep documentation up to date with code changes

Example:

```csharp
/// <summary>
/// Retrieves a user by their unique identifier.
/// </summary>
/// <param name="userId">The unique identifier of the user.</param>
/// <returns>
/// The user if found; otherwise, null.
/// </returns>
/// <exception cref="ArgumentException">
/// Thrown when userId is less than or equal to zero.
/// </exception>
/// <example>
/// <code>
/// var user = await repository.GetByIdAsync(123);
/// if (user != null)
/// {
///     Console.WriteLine(user.Name);
/// }
/// </code>
/// </example>
public async Task<User?> GetByIdAsync(int userId)
{
    // Implementation
}
```

### Documentation Files

When updating documentation:
- Use clear, concise language
- Include code examples
- Add screenshots for UI features
- Update table of contents if needed

## 🎓 Learning Resources

New to contributing? Check out these resources:

- [GitHub Flow Guide](https://guides.github.com/introduction/flow/)
- [How to Write a Git Commit Message](https://chris.beams.io/posts/git-commit/)
- [C# Coding Conventions](https://docs.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions)

## ❓ Questions?

If you have questions:
- Check existing documentation
- Search closed issues
- Ask in GitHub Discussions
- Open a new issue with the "question" label

## 🙏 Thank You!

Your contributions make Mogly-boxrr better for everyone. We appreciate your time and effort!

---

Happy coding! 🎉
