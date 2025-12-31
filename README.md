# Python Quality Orb

[![CircleCI](https://circleci.com/gh/branchbutler-labs/python-quality-orb.svg?style=shield)](https://circleci.com/gh/branchbutler-labs/python-quality-orb)
[![CircleCI Orb Version](https://badges.circleci.com/orbs/branchbutler-labs/python-quality.svg)](https://circleci.com/developer/orbs/orb/branchbutler-labs/python-quality)

CircleCI orb for Python code quality automation. Provides formatting, linting, static analysis, and automated PR reviews with slash command support.

## Features

- 🎨 **Auto-formatting** with Black and isort
- 🔍 **Comprehensive linting** with pylint, flake8, mypy, and bandit
- 🤖 **Automated PR reviews** with Danger
- 🔄 **Smart rebasing** with conflict resolution
- ⚡ **Slash command support** for interactive PR workflows

## Quick Start

### Basic Usage

```yaml
version: 2.1

orbs:
  python-quality: branchbutler-labs/python-quality@1.0.0

workflows:
  code-quality:
    jobs:
      - python-quality/code-quality:
          context: branchbutler
```

### PR Automation Workflow

```yaml
version: 2.1

orbs:
  python-quality: branchbutler-labs/python-quality@1.0.0

workflows:
  pr-checks:
    jobs:
      - python-quality/code-quality:
          auto-commit: true
          filters:
            branches:
              ignore: main

      - python-quality/pr-review:
          requires:
            - python-quality/code-quality
```

## Commands

### `format`

Format Python code with Black and isort.

**Parameters:**
- `line-length` (integer, default: `100`) - Maximum line length
- `auto-commit` (boolean, default: `true`) - Auto-commit changes
- `paths` (string, default: `"."`) - Paths to format
- `isort-profile` (string, default: `"black"`) - isort profile

**Example:**
```yaml
steps:
  - python-quality/format:
      line-length: 120
      auto-commit: true
```

### `lint`

Run Python linters and static analysis.

**Parameters:**
- `fail-on-error` (boolean, default: `true`) - Fail on linting errors
- `run-pylint` (boolean, default: `true`) - Run pylint
- `run-flake8` (boolean, default: `true`) - Run flake8
- `run-mypy` (boolean, default: `true`) - Run mypy
- `run-bandit` (boolean, default: `true`) - Run bandit
- `max-line-length` (integer, default: `100`) - Maximum line length
- `paths` (string, default: `"."`) - Paths to lint

**Example:**
```yaml
steps:
  - python-quality/lint:
      fail-on-error: false
      run-mypy: true
```

### `danger`

Run Danger for automated PR review.

**Parameters:**
- `github-token` (env_var_name, default: `GITHUB_TOKEN`) - GitHub token variable
- `min-coverage` (integer, default: `80`) - Minimum test coverage
- `fail-on-warnings` (boolean, default: `false`) - Fail on warnings

**Example:**
```yaml
steps:
  - python-quality/danger:
      min-coverage: 90
```

### `rebase`

Rebase PR branch onto target branch.

**Parameters:**
- `base-branch` (string, default: `"main"`) - Base branch to rebase onto
- `auto-resolve-conflicts` (boolean, default: `false`) - Auto-resolve conflicts
- `force-push` (boolean, default: `true`) - Force push after rebase

**Example:**
```yaml
steps:
  - python-quality/rebase:
      base-branch: "develop"
      auto-resolve-conflicts: true
```

## Jobs

### `code-quality`

Run full code quality suite with formatting and linting.

**Parameters:**
- `checkout-code` (boolean, default: `true`) - Checkout repository
- `run-format` (boolean, default: `true`) - Run formatting
- `run-lint` (boolean, default: `true`) - Run linting
- `line-length` (integer, default: `100`) - Maximum line length
- `auto-commit` (boolean, default: `false`) - Auto-commit changes

**Example:**
```yaml
jobs:
  - python-quality/code-quality:
      line-length: 100
      auto-commit: true
      context: branchbutler
```

### `pr-review`

Automated PR review with Danger.

**Parameters:**
- `min-coverage` (integer, default: `80`) - Minimum test coverage

**Example:**
```yaml
jobs:
  - python-quality/pr-review:
      min-coverage: 90
```

## Slash Commands

Use these commands in PR comments to trigger actions:

### `/format`
Auto-format code with Black and isort.

```
/format
```

### `/lint`
Run comprehensive linting.

```
/lint
```

### `/rebase`
Rebase PR branch onto target.

```
/rebase
```

### `/danger`
Run automated PR review.

```
/danger
```

> **Note**: Slash commands require webhook setup. See [slash command documentation](https://github.com/branchbutler-labs/branchbutler-orbs/tree/main/templates/slash-command).

## Executors

### `python`

Python executor with pre-installed tools.

**Parameters:**
- `tag` (string, default: `"3.11"`) - Python version
- `resource-class` (enum, default: `"medium"`) - Resource class

**Example:**
```yaml
executor:
  name: python-quality/python
  tag: "3.10"
  resource-class: "large"
```

## Configuration Examples

### Format Only

```yaml
version: 2.1

orbs:
  python-quality: branchbutler-labs/python-quality@1.0.0

workflows:
  format:
    jobs:
      - python-quality/code-quality:
          run-format: true
          run-lint: false
          auto-commit: true
```

### Lint Only (No Format)

```yaml
version: 2.1

orbs:
  python-quality: branchbutler-labs/python-quality@1.0.0

workflows:
  lint:
    jobs:
      - python-quality/code-quality:
          run-format: false
          run-lint: true
```

### Custom Job with Individual Commands

```yaml
version: 2.1

orbs:
  python-quality: branchbutler-labs/python-quality@1.0.0

jobs:
  my-custom-job:
    executor: python-quality/python
    steps:
      - checkout
      - python-quality/format:
          line-length: 120
      - python-quality/lint:
          run-mypy: true
          fail-on-error: false
      - run:
          name: Run tests
          command: pytest

workflows:
  main:
    jobs:
      - my-custom-job
```

### Full PR Automation

```yaml
version: 2.1

orbs:
  python-quality: branchbutler-labs/python-quality@1.0.0

workflows:
  pr-automation:
    jobs:
      # Format and commit on PRs
      - python-quality/code-quality:
          name: format-code
          run-format: true
          run-lint: false
          auto-commit: true
          filters:
            branches:
              ignore: main

      # Lint after formatting
      - python-quality/code-quality:
          name: lint-code
          run-format: false
          run-lint: true
          requires:
            - format-code
          filters:
            branches:
              ignore: main

      # Run automated PR review
      - python-quality/pr-review:
          requires:
            - lint-code
          filters:
            branches:
              ignore: main
```

## Requirements

Your repository should have:
- Python project with Python files (`.py`)
- Optional: `requirements.txt` and/or `requirements-dev.txt`
- Optional: `.pylintrc`, `.flake8`, `mypy.ini` for custom linter config

## Environment Variables

### Required for PR Features
- `GITHUB_TOKEN` - GitHub personal access token with `repo` scope

### Optional
- `CIRCLECI_TOKEN` - For slash command integration

## Development

### Prerequisites
- CircleCI CLI: `brew install circleci`
- CircleCI account with orb publishing enabled

### Validate Orb

```bash
circleci orb validate src/@orb.yml
```

### Pack Orb

```bash
circleci orb pack src > orb.yml
```

### Publish Dev Version

```bash
circleci orb publish orb.yml branchbutler-labs/python-quality@dev:first
```

### Promote to Production

```bash
circleci orb publish promote branchbutler-labs/python-quality@dev:first patch
```

## Contributing

Contributions welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Make your changes with tests
4. Submit a pull request

## Support

- **Documentation**: [CircleCI Orb Registry](https://circleci.com/developer/orbs/orb/branchbutler-labs/python-quality)
- **Issues**: [GitHub Issues](https://github.com/branchbutler-labs/python-quality-orb/issues)
- **Website**: [Branch Butler](https://branchbutler.com)

## License

MIT License - See [LICENSE](LICENSE) file for details.

---

**Part of the Branch Butler family of DevOps automation tools.**

🔗 [branchbutler.com](https://branchbutler.com) | 🐙 [GitHub](https://github.com/branchbutler-labs)
