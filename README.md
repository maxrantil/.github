# .github

Centralized reusable workflows and templates for all maxrantil repositories.

## Purpose

This special `.github` repository provides:
- **Reusable Workflows**: Shared CI/CD workflows callable from any repository
- **Workflow Templates**: Pre-configured templates that appear in GitHub's "Actions" tab
- **Profile README**: Showcases on GitHub profile page

## Benefits

**Single Source of Truth**: Update workflows once, all repositories benefit automatically.

**Consistency**: All projects follow the same quality standards and conventions.

**CLAUDE.md Compliance**: Enforces TDD, conventional commits, and session handoff requirements.

## Available Reusable Workflows

### Python Test Workflow

**File**: `.github/workflows/python-test-reusable.yml`

Runs Python tests with configurable Python version, dependencies, and test commands.

**Usage**:
```yaml
jobs:
  test:
    uses: maxrantil/.github/.github/workflows/python-test-reusable.yml@main
    with:
      python-version: '3.11'
      install-command: 'pip install -e ".[dev]"'
      test-command: 'pytest --cov --cov-report=term-missing'
```

**Inputs**:
- `python-version`: Python version (default: `3.11`)
- `install-command`: Dependency installation command (default: `pip install -e ".[dev]"`)
- `test-command`: Test execution command (default: `pytest --cov --cov-report=term-missing`)
- `working-directory`: Working directory (default: `.`)

### Shell Quality Workflow

**File**: `.github/workflows/shell-quality-reusable.yml`

Runs ShellCheck and shfmt formatting validation.

**Usage**:
```yaml
jobs:
  shell-quality:
    uses: maxrantil/.github/.github/workflows/shell-quality-reusable.yml@main
    with:
      shellcheck-severity: 'warning'
      shfmt-options: '-d -i 4 -ci -sr'
```

**Inputs**:
- `shellcheck-severity`: ShellCheck severity level (default: `warning`)
- `shellcheck-ignore-paths`: Paths to ignore (space-separated)
- `shfmt-paths`: Paths to check (default: `**/*.sh`)
- `shfmt-options`: shfmt formatting options (default: `-d -i 4 -ci -sr`)

### Conventional Commit Check

**File**: `.github/workflows/conventional-commit-check-reusable.yml`

Validates commit messages follow conventional commit format.

**Usage**:
```yaml
jobs:
  commit-format:
    uses: maxrantil/.github/.github/workflows/conventional-commit-check-reusable.yml@main
    with:
      base-branch: 'master'
```

**Inputs**:
- `allowed-types`: Comma-separated commit types (default: `feat,fix,docs,style,refactor,test,chore,perf,ci,build,revert`)
- `base-branch`: Base branch to compare (default: `master`)

### Session Handoff Check

**File**: `.github/workflows/session-handoff-check-reusable.yml`

Ensures session handoff documentation exists per CLAUDE.md requirements.

**Usage**:
```yaml
jobs:
  session-handoff:
    uses: maxrantil/.github/.github/workflows/session-handoff-check-reusable.yml@main
    with:
      base-branch: 'master'
```

**Inputs**:
- `base-branch`: Base branch to compare (default: `master`)
- `handoff-file`: Expected handoff file (default: `SESSION_HANDOVER.md`)
- `handoff-dir`: Directory for dated files (default: `docs/implementation`)
- `min-lines`: Minimum lines for valid doc (default: `10`)

## Workflow Templates

Templates appear in GitHub's "Actions" → "New workflow" interface.

### Python CI Template

**Template**: `workflow-templates/python-ci.yml`

Complete Python CI with tests, commit checks, and handoff verification.

### Shell CI Template

**Template**: `workflow-templates/shell-ci.yml`

Complete Shell CI with quality checks, commit format, and handoff verification.

## Usage Examples

### Migrating Existing Repository

**Before** (project-specific workflow):
```yaml
# .github/workflows/ci.yml (in each repo)
name: CI
on:
  pull_request:
    branches: [master]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.11'
      - run: pip install -e ".[dev]"
      - run: pytest --cov
```

**After** (using reusable workflow):
```yaml
# .github/workflows/ci.yml (in each repo)
name: CI
on:
  pull_request:
    branches: [master]
jobs:
  test:
    uses: maxrantil/.github/.github/workflows/python-test-reusable.yml@main
    with:
      python-version: '3.11'
```

**Benefits**:
- Shorter, clearer workflows
- Updates propagate automatically
- Consistency across all projects

### Adding to New Repository

1. **Create workflow file** in your repo: `.github/workflows/ci.yml`
2. **Reference reusable workflows**:
```yaml
name: CI
on:
  pull_request:
    branches: [master]

jobs:
  test:
    uses: maxrantil/.github/.github/workflows/python-test-reusable.yml@main

  commit-format:
    uses: maxrantil/.github/.github/workflows/conventional-commit-check-reusable.yml@main

  session-handoff:
    uses: maxrantil/.github/.github/workflows/session-handoff-check-reusable.yml@main
```
3. **Customize inputs** as needed

## Repository Structure

```
.github/
├── workflows/                      # Reusable workflows
│   ├── python-test-reusable.yml
│   ├── shell-quality-reusable.yml
│   ├── conventional-commit-check-reusable.yml
│   └── session-handoff-check-reusable.yml
├── workflow-templates/             # Templates for GitHub UI
│   ├── python-ci.yml
│   ├── python-ci.properties.json
│   ├── shell-ci.yml
│   └── shell-ci.properties.json
├── profile/
│   └── README.md                  # GitHub profile page
└── README.md                      # This file
```

## Versioning Strategy

**Current approach**: Reference `@main` for automatic updates.

**Pinned versions**: For stability, reference specific commits:
```yaml
uses: maxrantil/.github/.github/workflows/python-test-reusable.yml@abc1234
```

**Recommendation**: Use `@main` to get improvements automatically. Pin to commit SHA for critical production projects.

## Updating Workflows

**To update a reusable workflow**:
1. Clone this repository
2. Make changes to workflow files
3. Commit and push to `master`
4. All repositories using `@main` get updates on next workflow run

**Migration path for repositories**:
- No changes needed if using `@main`
- Update commit SHA if pinned to specific version

## Projects Using These Workflows

- [protonvpn-manager](https://github.com/maxrantil/protonvpn-manager)
- [vm-infra](https://github.com/maxrantil/vm-infra)
- [dotfiles](https://github.com/maxrantil/dotfiles)
- [project-templates](https://github.com/maxrantil/project-templates)

## Relationship to project-templates

**`.github`** (this repo):
- Centralized reusable workflows
- Single source of truth
- Updates propagate automatically
- For existing projects

**[project-templates](https://github.com/maxrantil/project-templates)**:
- Complete project starters
- Copy-paste approach
- For new projects
- Includes full directory structure

**Both have value**: Use `.github` for workflow updates across all repos. Use `project-templates` when starting new projects.

## Development Workflow

Per CLAUDE.md requirements:

1. **Feature branches** only (never commit to master)
2. **Conventional commits** (enforced by own workflows)
3. **Session handoff** after each change
4. **Test workflows** before merging

## Contributing

1. Create feature branch: `feat/improve-python-workflow`
2. Make changes
3. Test locally with `act` or in test repository
4. Create PR with session handoff documentation
5. Merge to master after validation

## License

Same as individual projects (varies by repository).

## Questions?

Check the [project-templates README](https://github.com/maxrantil/project-templates) for more context on the overall workflow philosophy.
