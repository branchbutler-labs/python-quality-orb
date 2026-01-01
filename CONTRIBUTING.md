# Contributing to Python Quality Orb

Thank you for your interest in contributing to the Python Quality Orb! This document provides guidelines and instructions for contributing.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [Development Setup](#development-setup)
- [Making Changes](#making-changes)
- [Testing](#testing)
- [Submitting Changes](#submitting-changes)
- [Release Process](#release-process)

## Code of Conduct

Please be respectful and constructive in all interactions. We welcome contributors of all experience levels.

## Getting Started

### Prerequisites

- **CircleCI CLI**: Install with `brew install circleci`
- **Python 3.11+**: For running pre-commit hooks
- **pre-commit**: Install with `pip install pre-commit`
- **GitHub CLI** (optional): For creating PRs from command line

### Repository Structure

```
python-quality-orb/
├── src/                    # Orb source files
│   ├── @orb.yml           # Main orb definition
│   ├── commands/          # Reusable commands
│   ├── jobs/              # Pre-built jobs
│   └── executors/         # Execution environments
├── .circleci/             # CI/CD configuration
├── .github/               # GitHub workflows & templates
├── docs/examples/         # Usage examples
└── test-integration/      # Integration tests
```

## Development Setup

### 1. Fork and Clone

```bash
# Fork the repository on GitHub, then:
git clone https://github.com/YOUR_USERNAME/python-quality-orb.git
cd python-quality-orb
```

### 2. Install Pre-commit Hooks

```bash
pip install pre-commit
pre-commit install
```

### 3. Verify Setup

```bash
# Validate the orb
circleci orb pack src > orb.yml
circleci orb validate orb.yml

# Run pre-commit checks
pre-commit run --all-files
```

## Making Changes

### Adding a New Command

1. Create a new file in `src/commands/`:

```yaml
# src/commands/my-command.yml
description: >
  Description of what the command does.

parameters:
  param-name:
    type: string
    default: "default-value"
    description: What this parameter controls

steps:
  - run:
      name: Step name
      command: |
        echo "Running with param: << parameters.param-name >>"
```

2. Add to `src/@orb.yml`:

```yaml
commands:
  my-command: <<include(commands/my-command.yml)>>
```

3. Add an example in `src/@orb.yml` or `docs/examples/`.

### Adding a New Job

1. Create a new file in `src/jobs/`:

```yaml
# src/jobs/my-job.yml
description: >
  Description of what the job does.

executor: python

parameters:
  param-name:
    type: string
    default: "value"

steps:
  - checkout
  - my-command:
      param-name: << parameters.param-name >>
```

2. Add to `src/@orb.yml`:

```yaml
jobs:
  my-job: <<include(jobs/my-job.yml)>>
```

### Modifying Existing Components

1. Make changes to the relevant file in `src/`
2. Update documentation in `README.md` if parameters change
3. Update `CHANGELOG.md` with your changes
4. Add/update tests if applicable

## Testing

### Local Validation

```bash
# Pack and validate
circleci orb pack src > orb.yml
circleci orb validate orb.yml

# Validate CircleCI config
circleci config validate .circleci/config.yml
```

### Pre-commit Checks

```bash
# Run all hooks
pre-commit run --all-files

# Run specific hook
pre-commit run yamllint --all-files
```

### Integration Testing

For testing in a real CircleCI pipeline:

1. Publish a dev version:
```bash
circleci orb publish orb.yml branchbutler-labs/python-quality@dev:your-branch
```

2. Use in a test project:
```yaml
orbs:
  python-quality: branchbutler-labs/python-quality@dev:your-branch
```

## Submitting Changes

### Commit Message Format

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```
type(scope): description

[optional body]

[optional footer]
```

Types:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `refactor`: Code refactoring
- `test`: Adding/updating tests
- `chore`: Maintenance tasks

Examples:
```
feat(commands): add new coverage command
fix(lint): remove 20-file limit in pylint
docs: update README with new parameters
```

### Pull Request Process

1. **Create a branch**:
```bash
git checkout -b feat/my-feature
```

2. **Make changes** and commit:
```bash
git add .
git commit -m "feat(commands): add new feature"
```

3. **Push and create PR**:
```bash
git push origin feat/my-feature
# Then create PR on GitHub
```

4. **PR Requirements**:
   - Fill out the PR template completely
   - All CI checks must pass
   - Update CHANGELOG.md
   - Request review from maintainers

### Code Review

- PRs require at least one approval
- Address all review comments
- Keep PRs focused and reasonably sized
- Breaking changes require discussion first

## Release Process

Releases are managed by maintainers following semantic versioning:

- **Patch** (1.0.x): Bug fixes, documentation
- **Minor** (1.x.0): New features, backward compatible
- **Major** (x.0.0): Breaking changes

### Release Checklist

1. Update `CHANGELOG.md` with release notes
2. Update version references in examples
3. Create release PR
4. After merge, publish:
```bash
circleci orb publish promote branchbutler-labs/python-quality@dev:main patch|minor|major
```

## Best Practices

### CircleCI Orb Standards

- Follow [CircleCI Orb Best Practices](https://circleci.com/docs/orbs/author/orbs-best-practices/)
- All parameters must have descriptions
- Use `env_var_name` type for secrets
- Provide sensible defaults
- Include usage examples

### Code Style

- Use 2-space indentation in YAML
- Keep line length under 120 characters
- Use descriptive parameter names
- Add comments for complex logic

### Documentation

- Update README.md for user-facing changes
- Keep ARCHITECTURE.md current for structural changes
- Add inline comments in complex commands
- Include examples for new features

## Getting Help

- **Questions**: Open a [Discussion](https://github.com/branchbutler-labs/python-quality-orb/discussions)
- **Bugs**: Open an [Issue](https://github.com/branchbutler-labs/python-quality-orb/issues)
- **CircleCI Help**: [CircleCI Documentation](https://circleci.com/docs/)

---

Thank you for contributing! 🎉
