# Python Quality Orb - Architecture Documentation

## Overview

The Python Quality Orb is a CircleCI orb providing automated Python code quality automation for the Branch Butler organization. It offers formatting, linting, PR reviews, and git automation capabilities.

**Repository**: <https://github.com/branchbutler-labs/python-quality-orb>
**Registry**: <https://circleci.com/developer/orbs/orb/branchbutler-labs/python-quality>
**Current Version**: v1.1.0

## Directory Structure

```text
python-quality-orb/
├── .circleci/
│   ├── config.yml              # Main CI/CD pipeline (orb-tools based)
│   └── test-deploy.yml         # Integration test suite
├── src/
│   ├── @orb.yml                # Orb entry point with metadata and imports
│   ├── commands/
│   │   ├── format.yml          # Black + isort formatting
│   │   ├── lint.yml            # pylint, flake8, mypy, bandit
│   │   ├── danger.yml          # Automated PR review
│   │   └── rebase.yml          # Smart git rebasing
│   ├── jobs/
│   │   ├── code-quality.yml    # Full quality suite job
│   │   └── pr-review.yml       # PR review job wrapper
│   └── executors/
│       └── python.yml          # Docker Python executor
├── docs/
│   └── examples/
│       ├── basic.yml           # Simple usage
│       ├── full-automation.yml # Complex workflow
│       └── custom-commands.yml # Custom job patterns
├── test-integration/
│   └── test-config.yml         # Integration test config
├── .pre-commit-config.yaml     # Pre-commit hooks (NEW)
├── CHANGELOG.md                # Version history (NEW)
├── README.md                   # User documentation
├── ARCHITECTURE.md             # This file
└── LICENSE                     # MIT License
```

## Component Architecture

### Core Components

#### 1. Executor: Python Environment

**File**: `src/executors/python.yml`
**Purpose**: Provides configurable Python Docker environment

```yaml
Parameters:
  - tag: Python version (default: "3.11")
  - resource-class: CPU/memory allocation (default: "medium")

Features:
  - CircleCI maintained images (cimg/python)
  - Pip caching via PIP_CACHE_DIR
  - Unbuffered output for real-time logs
```

#### 2. Commands

| Command | File | Purpose | Key Parameters |
|---------|------|---------|----------------|
| format | commands/format.yml | Black + isort formatting | line-length, auto-commit, paths |
| lint | commands/lint.yml | Multi-tool linting suite | fail-on-error, run-* toggles |
| danger | commands/danger.yml | Automated PR review | github-token, fail-on-warnings |
| rebase | commands/rebase.yml | Smart git rebasing | base-branch, auto-resolve-conflicts |

#### 3. Jobs

| Job | File | Purpose | Wraps Commands |
|-----|------|---------|----------------|
| code-quality | jobs/code-quality.yml | Full quality suite | format + lint |
| pr-review | jobs/pr-review.yml | PR automation | danger |

### Data Flow

```text
Developer Push → CircleCI Trigger → Orb Execution
                                          │
                    ┌─────────────────────┴─────────────────────┐
                    │                                           │
              code-quality job                            pr-review job
                    │                                           │
         ┌─────────┴─────────┐                                  │
         │                   │                                  │
    format command     lint command                      danger command
         │                   │                                  │
    Black + isort    pylint, flake8,                    PR analysis +
         │           mypy, bandit                       GitHub comments
         │                   │                                  │
    [auto-commit]     [fail/pass]                       [PR feedback]
```

## Integration Points

### External Services

- **GitHub API**: PR comments via Danger, commit pushing
- **CircleCI API**: Pipeline parameters, orb publishing
- **PyPI**: Tool installation (Black, pylint, etc.)

### Environment Variables

| Variable | Used By | Required | Description |
|----------|---------|----------|-------------|
| GITHUB_TOKEN | danger | Yes | GitHub API access for PR comments |
| CIRCLECI_TOKEN | CI/CD | Yes | Orb publishing (context: orb-publishing) |

## Agent Roles for Development

### DevOps Agent (CircleCI Orb Expert)

**Responsibilities**:

- CircleCI orb structure and best practices
- CI/CD pipeline configuration
- Orb publishing and versioning
- Integration testing setup
- Infrastructure-as-code patterns

**Key Skills**:

- circleci CLI proficiency
- orb-tools orb usage
- YAML schema validation
- Semantic versioning
- Docker executor configuration

### QA Agent (Testing Specialist)

**Responsibilities**:

- Pre-commit hook configuration
- Integration test development
- YAML/Markdown linting
- Secret detection
- Test coverage validation

**Key Skills**:

- Pre-commit framework
- pytest integration
- Code quality tools
- Security scanning
- CI test orchestration

### Python Quality Agent

**Responsibilities**:

- Danger system implementation
- Auto-labeling logic
- PR management automation
- Code quality rule definition

**Key Skills**:

- danger-python
- GitHub API
- Label automation
- PR workflow optimization

## Danger System Architecture

### Current Implementation (Basic)

```python
# Embedded Dangerfile checks:
- PR description validation (< 10 chars warning)
- Changelog detection for main branch
- Large file detection (> 500KB)
- TODO comment scanning
```

### Enhanced Implementation (Target)

```python
# Comprehensive Dangerfile with:
- Auto-labeling by file type and size
- PR template enforcement
- Test coverage requirements
- Breaking change detection
- Dependency audit
- Commit message validation
- Stale PR detection
- Assignee auto-assignment
- Review request automation
```

## Pre-commit Configuration

### Target Hooks

```yaml
repos:
  # YAML validation
  - repo: https://github.com/adrienverge/yamllint
    hooks:
      - id: yamllint

  # Markdown linting
  - repo: https://github.com/igorshubovych/markdownlint-cli
    hooks:
      - id: markdownlint

  # Secret detection
  - repo: https://github.com/Yelp/detect-secrets
    hooks:
      - id: detect-secrets

  # CircleCI config validation
  - repo: local
    hooks:
      - id: circleci-validate
        name: Validate CircleCI config
        entry: circleci config validate
        language: system

  # Orb validation
  - repo: local
    hooks:
      - id: orb-validate
        name: Validate orb
        entry: bash -c 'circleci orb pack src > orb.yml && circleci orb validate orb.yml'
        language: system
```

## Testing Strategy

### Unit Tests

- YAML schema validation
- Parameter default verification
- Include syntax validation

### Integration Tests

- Command execution in Docker
- Job workflow completion
- Executor configuration
- Cache key generation

### E2E Tests

- Full orb workflow on sample repo
- PR comment posting
- Auto-commit functionality

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-01-01 | Initial release (monolithic) |
| 1.1.0 | 2026-01-01 | Refactored to modular structure |
| 1.2.0 | TBD | Enhanced Danger, pre-commit, fixes |

## Known Issues & Fixes Required

### Critical

1. **min-coverage parameter unused** in danger.yml
2. **20-file limit** in lint command pylint step
3. **Hardcoded fail-on-error: false** in code-quality job

### High Priority

1. No version pinning for pip packages
2. Limited conflict resolution in rebase
3. No pyproject.toml/Pipfile cache support

### Medium Priority

1. No .pylintrc/.flake8 config file support
2. Limited error handling in git operations
3. Incomplete troubleshooting documentation

## References

- [CircleCI Orb Best Practices](https://circleci.com/docs/orbs/author/orbs-best-practices/)
- [orb-tools Orb](https://circleci.com/developer/orbs/orb/circleci/orb-tools)
- [Danger Python](https://danger.systems/python/)
- [Pre-commit Framework](https://pre-commit.com/)

---
*Last Updated: 2026-01-01*
*Maintainer: Branch Butler Team*
