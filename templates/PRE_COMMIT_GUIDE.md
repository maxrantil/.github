# Pre-commit Configuration Guide

## Overview

This is the optimized universal `.pre-commit-config.yaml` template for all maxrantil repositories. It combines the best practices from all existing repos and enforces CLAUDE.md policies.

## What's Included

### Core Features (Always Active)

1. **AI Attribution Blocking** ⚠️ **CRITICAL**
   - Blocks `Co-authored-by: Claude/GPT/AI/...`
   - Blocks `Generated with [AI tool]` messages
   - Blocks agent validation mentions
   - Enforces CLAUDE.md policy compliance

2. **Conventional Commit Format** 📝
   - Validates commit message structure
   - Required format: `<type>[optional scope]: <description>`
   - Supported types: feat, fix, docs, style, refactor, test, chore, perf, ci, build, revert

3. **File Quality Checks** ✨
   - Remove trailing whitespace
   - Fix end of files (ensure newline)
   - Normalize line endings (LF)
   - Validate YAML/JSON syntax
   - Detect merge conflicts
   - Check for case conflicts

4. **Security Checks** 🔒
   - Detect private keys (SSH keys, certificates)
   - Detect credentials (passwords, API keys, tokens)
   - Check for large files (>1MB)

5. **Shell Script Quality** 🐚
   - ShellCheck linting
   - Verify shebangs on executables
   - Ensure scripts with shebangs are executable

6. **Markdown Linting** 📄
   - Consistent markdown style
   - Disabled rules: MD013 (line length), MD041 (first line heading)

### Language-Specific Sections (Commented Out)

Uncomment the relevant section for your project:

- **Python**: Black, Ruff, MyPy
- **JavaScript/TypeScript**: ESLint, Prettier, TSC
- **Terraform**: Format, Validate, Docs

## Installation

### 1. Install pre-commit

```bash
# Using pip
pip install pre-commit

# Using brew (macOS)
brew install pre-commit

# Using apt (Ubuntu/Debian)
sudo apt install pre-commit
```

### 2. Copy template to your repo

```bash
cp /path/to/.github/templates/.pre-commit-config.yaml .pre-commit-config.yaml
```

### 3. Customize for your project

Edit `.pre-commit-config.yaml`:

1. **Uncomment language-specific hooks** if needed (Python, JS/TS, Terraform)
2. **Update exclusions** in `no-ai-attribution` hook if you have special files
3. **Add project-specific hooks** in the final section

### 4. Install the hooks

```bash
# Install pre-commit hooks
pre-commit install

# Install commit-msg hooks (for conventional commits)
pre-commit install --hook-type commit-msg

# Optional: Run on all files to test
pre-commit run --all-files
```

## Usage

### Automatic (Recommended)

Once installed, hooks run automatically:
- **On `git commit`**: File quality, AI blocking, syntax checks
- **On commit message**: Conventional commit format validation
- **On `git push`**: Tests (if configured)

### Manual

Run hooks manually:

```bash
# Run all hooks on staged files
pre-commit run

# Run all hooks on all files
pre-commit run --all-files

# Run specific hook
pre-commit run no-ai-attribution

# Skip hooks (NOT RECOMMENDED)
git commit --no-verify  # DANGEROUS - bypasses CLAUDE.md enforcement
```

## Configuration Examples

### Python Project

Uncomment Python section and customize:

```yaml
- repo: https://github.com/astral-sh/ruff-pre-commit
  rev: v0.7.4
  hooks:
    - id: ruff
      args: [--fix, --select, E,F,W,I]  # Customize rules
```

### JavaScript/TypeScript Project

Uncomment JS/TS section:

```yaml
- repo: local
  hooks:
    - id: eslint
      name: ESLint
      entry: npx eslint
      language: system
      files: \.(js|jsx|ts|tsx)$
      args: [--fix]
      exclude: ^\.next/  # Add project-specific excludes
```

### Add Custom Tests

```yaml
- repo: local
  hooks:
    - id: run-unit-tests
      name: Run unit tests
      entry: npm test  # or pytest, go test, etc.
      language: system
      pass_filenames: false
      stages: [pre-push]  # Only on push, not every commit
```

## Troubleshooting

### Hook fails with "command not found"

**Problem**: Tool not installed or not in PATH

**Solution**:
```bash
# Install the tool
npm install -g eslint  # for ESLint
pip install black      # for Black
brew install shellcheck # for ShellCheck

# Or use project-local tools
npx eslint  # for local ESLint
```

### AI attribution check blocks valid code

**Problem**: False positive detection

**Solution**: Add exclusion to the hook:
```yaml
- id: no-ai-attribution
  exclude: '^(\.pre-commit-config\.yaml|path/to/special-file\.txt)$'
```

### Conventional commit check too strict

**Problem**: Need different commit message format

**Solution**: Update the pattern in `conventional-commit-msg` hook or discuss with team to align on standards.

### Hooks are slow

**Problem**: Running expensive operations on every commit

**Solutions**:
1. Move expensive hooks to `pre-push` stage:
   ```yaml
   stages: [pre-push]  # Instead of [pre-commit]
   ```

2. Use `pass_filenames: true` to only check changed files
   ```yaml
   pass_filenames: true
   ```

3. Skip specific files:
   ```yaml
   exclude: '^(tests/|docs/|\.github/)'
   ```

## Maintenance

### Update hook versions

```bash
# Update all hooks to latest versions
pre-commit autoupdate

# Review changes
git diff .pre-commit-config.yaml

# Test updates
pre-commit run --all-files
```

### Common version updates

- pre-commit/pre-commit-hooks: Update `rev` to latest
- shellcheck-py/shellcheck-py: Update `rev` to latest
- markdownlint-cli: Update `rev` to latest

Check latest versions at: https://pre-commit.com/hooks.html

## Integration with CI/CD

Pre-commit hooks complement (not replace) CI/CD workflows:

| Check | Pre-commit | CI/CD | Why Both? |
|-------|-----------|-------|-----------|
| AI Attribution | ✅ Local | ✅ GitHub Actions | Defense in depth - catch early + enforce centrally |
| Commit Format | ✅ Local | ✅ GitHub Actions | Local feedback + enforced on all commits |
| Tests | ⚠️ Optional | ✅ Required | Fast feedback locally, comprehensive in CI |
| Security Scan | ✅ Basic | ✅ Full | Quick checks locally, deep scans in CI |

## Best Practices

1. **Always install commit-msg hook**: `pre-commit install --hook-type commit-msg`
2. **Never use `--no-verify`**: Bypasses CLAUDE.md enforcement
3. **Run on all files after updates**: `pre-commit run --all-files`
4. **Keep hooks fast**: Move slow checks to pre-push stage
5. **Update regularly**: `pre-commit autoupdate` monthly
6. **Test before committing to master**: Create PR with hook changes first

## Repository Status

### ✅ Fully Configured

- **protonvpn-manager**: Has AI blocking, conventional commits, shell checks, tests
- **dotfiles**: Has AI blocking, conventional commits (PR #54)
- **project-templates**: Has AI blocking, conventional commits (PR #9)

### ⚠️ Needs AI Blocking

- **vm-infra**: Has security checks, needs AI blocking + conventional commits
- **textile-showcase**: Has frontend checks, needs AI blocking + conventional commits

## Links

- [Pre-commit documentation](https://pre-commit.com/)
- [Conventional Commits specification](https://www.conventionalcommits.org/)
- [CLAUDE.md](../CLAUDE.md) - Project policies
- [Available hooks](https://pre-commit.com/hooks.html)

---

**Last Updated**: 2025-10-13
**Maintained By**: Doctor Hubert (via Claude Code)
**Template Version**: 1.0.0
