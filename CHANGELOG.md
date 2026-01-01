# Changelog

All notable changes to the Python Quality Orb will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Planned for v1.2.0

- Enhanced test coverage reporting
- Integration with SonarQube
- Support for custom ruff linting rules
- Performance optimizations for large codebases
- Enhanced caching strategies

## [1.1.0] - 2024-12-31

### Added

- Smart rebasing command with conflict resolution
- Comprehensive ARCHITECTURE.md documentation
- Enhanced slash command support for interactive PR workflows
- Improved caching for pip dependencies
- Better error handling and reporting in lint command

### Changed

- Refactored command structure for better maintainability
- Updated executor to use Python 3.11 as default
- Improved documentation with more usage examples
- Enhanced PR review workflow with better integration

### Fixed

- Resolved issues with auto-commit functionality
- Fixed mypy type checking in certain edge cases
- Corrected path handling in format command
- Improved bandit security scanning configuration

### Documentation

- Added detailed architecture documentation
- Enhanced README with comprehensive examples
- Added troubleshooting guide
- Improved inline documentation for all commands

## [1.0.0] - 2024-12-15

### Initial Release

- Initial release of Python Quality Orb
- Auto-formatting with Black and isort
- Comprehensive linting with pylint, flake8, mypy, and bandit
- Automated PR reviews with Danger
- Code quality job with configurable parameters
- PR review job for automated workflows
- Python executor with pre-installed quality tools
- Support for custom line lengths and formatting options
- Auto-commit functionality for formatting changes
- Comprehensive documentation and usage examples

### Commands

- `format`: Auto-format Python code with Black and isort
- `lint`: Run comprehensive linting suite
- `danger`: Automated PR review with Danger
- `rebase`: Basic rebase functionality

### Jobs

- `code-quality`: Full code quality suite with formatting and linting
- `pr-review`: Automated PR review workflow

### Executors

- `python`: Python 3.11 Docker executor with pre-installed tools

### Features

- Configurable line length (default: 100)
- Optional auto-commit for formatting changes
- Fail-on-error configuration for linting
- Minimum coverage requirements for PR reviews
- Integration with CircleCI contexts
- Support for custom paths and configurations

[Unreleased]: https://github.com/branchbutler-labs/python-quality-orb/compare/v1.1.0...HEAD
[1.1.0]: https://github.com/branchbutler-labs/python-quality-orb/compare/v1.0.0...v1.1.0
[1.0.0]: https://github.com/branchbutler-labs/python-quality-orb/releases/tag/v1.0.0
