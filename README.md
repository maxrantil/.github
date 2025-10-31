# ABOUTME: Central documentation for .github reusable workflows repository
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

## Workflow Validation Status

**All workflows are production-ready**: 14/14 workflows (100%) have been comprehensively validated.

### Validation Metrics

- **Total Workflows**: 14
- **Validated**: 14 (100%)
- **Test Scenarios**: 100+
- **Overall Pass Rate**: 100%
- **Bypass Success Rate**: 0%
- **False Positive Rate**: 0%
- **Confidence Level**: HIGH

### Validation Methodology

All workflows have been tested using one or more of these approaches:

- **Attack Testing**: Attempting to bypass validation logic with malicious/edge case inputs
- **Guidance Testing**: Validating read-only analyzers post helpful, accurate comments
- **Integration Testing**: Testing framework wrappers and error detection

### Validation Documentation

Comprehensive validation reports are available for the following workflows:

1. [VALIDATION_ISSUE_17.md](docs/validation/VALIDATION_ISSUE_17.md) - Issue Auto-Label (17 scenarios, 11 label categories)
2. [VALIDATION_ISSUE_19.md](docs/validation/VALIDATION_ISSUE_19.md) - Issue PRD Reminder (13 scenarios)
3. [VALIDATION_ISSUE_20.md](docs/validation/VALIDATION_ISSUE_20.md) - PR Body AI Attribution (20+ scenarios)
4. [VALIDATION_ISSUE_21.md](docs/validation/VALIDATION_ISSUE_21.md) - PR Title Check (14 scenarios)
5. [VALIDATION_ISSUE_25.md](docs/validation/VALIDATION_ISSUE_25.md) - Block AI Attribution (18 scenarios)
6. [VALIDATION_ISSUE_26.md](docs/validation/VALIDATION_ISSUE_26.md) - Commit Quality Check (8 scenarios)
7. [VALIDATION_ISSUE_27.md](docs/validation/VALIDATION_ISSUE_27.md) - Pre-commit Check (3 integration scenarios)

**Additional validated workflows** (comprehensive testing completed, formal reports not published):
- Shell Quality (Issue #12)
- Python Test (Issue #13)
- Session Handoff Check (Issue #14)
- Protect Master (Issue #16)
- Issue Format Check (Issue #18)
- Conventional Commit Check
- Issue AI Attribution Check

All validation work tracked in [Issue #15](https://github.com/maxrantil/.github/issues/15) (closed).

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
      # Uses uv by default per CLAUDE.md requirements
      # Customize if needed:
      # pre-install-command: 'sudo apt-get install -y system-package'
      # install-command: 'uv sync'
      # test-command: 'uv run pytest --cov'
```

**Inputs**:
- `python-version`: Python version (default: `3.11`)
- `pre-install-command`: Command before dependency install (default: `''`) - For system packages
- `install-command`: Dependency installation command (default: `uv sync`) - Per CLAUDE.md UV requirement
- `test-command`: Test execution command (default: `uv run pytest --cov --cov-report=term-missing`)
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

### Block AI Attribution

**File**: `.github/workflows/block-ai-attribution-reusable.yml`

Blocks AI tool and agent attribution in commit messages.

**Usage**:
```yaml
jobs:
  block-attribution:
    uses: maxrantil/.github/.github/workflows/block-ai-attribution-reusable.yml@main
    with:
      base-branch: 'master'
```

**Inputs**:
- `base-branch`: Base branch to compare (default: `master`)
- `block-ai-tools`: Block AI tool names in Co-authored-by and Generated with (default: `true`)
- `block-agent-mentions`: Block agent names in commit messages (default: `true`)

### PR Title Check

**File**: `.github/workflows/pr-title-check-reusable.yml`

Validates PR titles follow conventional commit format.

**Usage**:
```yaml
jobs:
  pr-title:
    uses: maxrantil/.github/.github/workflows/pr-title-check-reusable.yml@main
    with:
      allowed-types: 'feat,fix,docs,style,refactor,test,chore'
```

**Inputs**:
- `allowed-types`: Comma-separated commit types (default: `feat,fix,docs,style,refactor,test,chore,perf,ci,build,revert`)

### Protect Master Branch

**File**: `.github/workflows/protect-master-reusable.yml`

Blocks direct pushes to master branch (only allows PR merges).

**Usage**:
```yaml
jobs:
  protect-master:
    uses: maxrantil/.github/.github/workflows/protect-master-reusable.yml@main
    with:
      protected-branch: 'master'
```

**Inputs**:
- `protected-branch`: Branch to protect (default: `master`)

**Note**: This workflow runs on `push` events, not `pull_request`. Add it to a separate workflow file or use `on: [push, pull_request]`.

### Pre-commit Check

**File**: `.github/workflows/pre-commit-check-reusable.yml`

Runs pre-commit hooks in CI to catch bypassed hooks.

**Usage**:
```yaml
jobs:
  pre-commit:
    uses: maxrantil/.github/.github/workflows/pre-commit-check-reusable.yml@main
    with:
      python-version: '3.11'
```

**Inputs**:
- `python-version`: Python version for pre-commit (default: `3.x`)
- `base-branch`: Base branch to compare for changed files (default: `master`)
- `run-on-all-files`: Run on all files instead of just changed files (default: `false`)

### Commit Quality Check

**File**: `.github/workflows/commit-quality-check-reusable.yml`

Analyzes commit history for fixup patterns and provides cleanup guidance without modifying commits.

**Usage**:
```yaml
jobs:
  commit-quality:
    uses: maxrantil/.github/.github/workflows/commit-quality-check-reusable.yml@main
    with:
      base-branch: 'master'
      fail-on-fixups: false
      suggest-cleanup: true
```

**Inputs**:
- `base-branch`: Base branch to compare against (default: `master`)
- `fail-on-fixups`: Fail workflow if fixup commits detected (default: `false`)
- `suggest-cleanup`: Post comment with cleanup suggestions (default: `true`)
- `cleanup-score-threshold`: Minimum score to suggest cleanup: `LOW`, `MEDIUM`, `HIGH` (default: `MEDIUM`)

**Behavior**:
- Detects fixup patterns: `fixup`, `wip:`, `tmp:`, `oops`, `typo`, `ci `, `lint `, `pre-commit`
- Calculates cleanup benefit score based on commit count and fixup ratio
- Generates ready-to-run cleanup script in PR comment
- Provides three options: automated script, manual rebase, or squash-on-merge
- **Read-only**: Never modifies commits (validation only)

**Cleanup Benefit Scores**:
- **HIGH**: >10 commits with >3 fixups - strong cleanup benefit
- **MEDIUM**: >1 fixup - moderate cleanup benefit
- **LOW**: 0-1 fixups - minimal cleanup benefit

**Phase 1** (Current): Validation-only workflow provides guidance
**Phase 2** (Future): Optional comment-triggered automation (`/cleanup-commits` command)

## Issue Validation Workflows

Phase 2 workflows for automated issue validation, labeling, and policy enforcement.

### Issue AI Attribution Check

**File**: `.github/workflows/issue-ai-attribution-check-reusable.yml`

Detects and blocks AI tool attribution and agent mentions in GitHub issues.

**Usage**:
```yaml
jobs:
  ai-attribution-check:
    permissions:
      issues: write
    uses: maxrantil/.github/.github/workflows/issue-ai-attribution-check-reusable.yml@master
    with:
      fail_on_attribution: true
```

**Inputs**:
- `fail_on_attribution`: Whether to fail the workflow when AI attribution is detected (default: `true`)

**Behavior**:
- Scans issue title and body for AI tool patterns (Claude, GPT, ChatGPT, Copilot, etc.)
- Detects agent validation mentions (architecture-designer, security-validator, etc.)
- Posts detailed policy explanation comment
- Adds `needs-revision` label
- Fails workflow (if enabled) to block issue until edited

**Policy Enforced**: Per CLAUDE.md Section 1, AI/agent attribution should NOT be in issues, commits, or PRs. Agent validations belong in session handoff and implementation docs.

### Issue Format Check

**File**: `.github/workflows/issue-format-check-reusable.yml`

Validates issue structure and completeness, providing feedback for improvement.

**Usage**:
```yaml
jobs:
  format-check:
    permissions:
      issues: write
    uses: maxrantil/.github/.github/workflows/issue-format-check-reusable.yml@master
    with:
      min_body_length: 20
      fail_on_problems: true
```

**Inputs**:
- `min_body_length`: Minimum required length for issue body in characters (default: `20`)
- `fail_on_problems`: Whether to fail workflow on critical format problems (default: `true`)

**Behavior**:
- Checks for empty or very short descriptions
- Detects vague titles
- Suggests issue templates for better structure
- Posts structured feedback with recommended formats for bugs/features/tasks
- Adds `needs-info` label for critical problems
- Optionally fails workflow to encourage better issues

### Issue PRD/PDR Reminder

**File**: `.github/workflows/issue-prd-reminder-reusable.yml`

Reminds about PRD/PDR documentation requirements for feature requests and enhancements.

**Usage**:
```yaml
jobs:
  prd-reminder:
    permissions:
      issues: write
    uses: maxrantil/.github/.github/workflows/issue-prd-reminder-reusable.yml@master
    with:
      prd_location_path: 'docs/implementation/'
      required_for_labels: 'enhancement,feature'
```

**Inputs**:
- `prd_location_path`: Path where PRD/PDR documents should be stored (default: `docs/implementation/`)
- `required_for_labels`: Comma-separated list of labels that trigger reminder (default: `enhancement,feature`)

**Behavior**:
- Only triggers for issues with specified labels (enhancement, feature, etc.)
- Skips if issue already mentions PRD/PDR
- Posts comprehensive workflow reminder with:
  - PRD requirements and review process
  - PDR requirements and agent validation steps
  - Complete workflow diagram (Idea → PRD → PDR → Implementation)
- Does not fail workflow (informational only)

### Issue Auto-Labeling

**File**: `.github/workflows/issue-auto-label-reusable.yml`

Automatically applies relevant labels based on issue content analysis.

**Usage**:
```yaml
jobs:
  auto-label:
    permissions:
      issues: write
    uses: maxrantil/.github/.github/workflows/issue-auto-label-reusable.yml@master
    with:
      enable_comment: true
```

**Inputs**:
- `enable_comment`: Whether to post a comment explaining applied labels (default: `true`)

**Behavior**:
- Analyzes issue title and body for keywords
- Applies relevant labels:
  - `bug` - Error, crash, failure, broken
  - `enhancement` - Feature, new, implement, improve
  - `documentation` - Docs, readme, guide
  - `security` - Vulnerability, exploit, CVE
  - `performance` - Slow, optimize, latency
  - `testing` - Test, coverage, unit test
  - `refactoring` - Refactor, cleanup, technical debt
  - `ci-cd` - Pipeline, workflow, GitHub Actions
  - `priority: high` - Urgent, critical, blocker (from title)
  - `priority: medium` - Important, soon
  - `question` - Contains question marks or "how to"
- Optionally posts comment listing applied labels
- Never fails workflow (labeling is informational)

### Complete Issue Validation Example

Combine all 4 issue workflows for comprehensive validation:

```yaml
name: Issue Validation
on:
  issues:
    types: [opened, edited, labeled]

permissions:
  issues: write

jobs:
  ai-attribution-check:
    name: Check AI Attribution
    uses: maxrantil/.github/.github/workflows/issue-ai-attribution-check-reusable.yml@master
    with:
      fail_on_attribution: true

  format-check:
    name: Check Format
    uses: maxrantil/.github/.github/workflows/issue-format-check-reusable.yml@master
    with:
      min_body_length: 20
      fail_on_problems: true

  prd-reminder:
    name: PRD/PDR Reminder
    uses: maxrantil/.github/.github/workflows/issue-prd-reminder-reusable.yml@master
    with:
      prd_location_path: 'docs/implementation/'
      required_for_labels: 'enhancement,feature'

  auto-label:
    name: Auto-Label
    uses: maxrantil/.github/.github/workflows/issue-auto-label-reusable.yml@master
    with:
      enable_comment: true
```

**Note**: Issue workflows require `permissions: issues: write` in the calling workflow.

## Workflow Templates

Templates appear in GitHub's "Actions" → "New workflow" interface.

### Python CI Template

**Template**: `workflow-templates/python-ci.yml`

Complete Python CI with tests, commit checks, and handoff verification.

### Shell CI Template

**Template**: `workflow-templates/shell-ci.yml`

Complete Shell CI with quality checks, commit format, and handoff verification.

## Document Templates

Centralized templates for project documentation, issues, PRs, and development guidelines.

**Location**: `docs/templates/`

### Available Templates

- **PRD-template.md** - Product Requirements Document
- **PDR-template.md** - Product Design & Requirements
- **session-handoff-template.md** - Session continuity documentation
- **github-issue-template.md** - GitHub issue with TDD checklist (consolidated)
- **github-issue-prd-template.md** - PRD-specific issue tracking
- **github-issue-pdr-template.md** - PDR-specific issue tracking
- **github-pr-template.md** - Pull request template with agent validation
- **github-commit-template.md** - Conventional commit message format
- **WRITING_STYLE.md** - Anti-AI writing guidelines

### Usage from Other Repositories

**Direct URLs** (for reference in CLAUDE.md files):
```markdown
- All templates: https://github.com/maxrantil/.github/tree/main/docs/templates
- PRD: https://github.com/maxrantil/.github/blob/main/docs/templates/PRD-template.md
- PDR: https://github.com/maxrantil/.github/blob/main/docs/templates/PDR-template.md
- Session Handoff: https://github.com/maxrantil/.github/blob/main/docs/templates/session-handoff-template.md
- Issue: https://github.com/maxrantil/.github/blob/main/docs/templates/github-issue-template.md
- PR: https://github.com/maxrantil/.github/blob/main/docs/templates/github-pr-template.md
- Writing Style: https://github.com/maxrantil/.github/blob/main/docs/templates/WRITING_STYLE.md
```

### TEMPLATE Command

In CLAUDE.md files across repositories, the **TEMPLATE command** allows quick template fetching:

```markdown
Doctor Hubert: "TEMPLATE PRD"
Claude: [Reads and displays PRD-template.md]
```

**Available commands:**
- `TEMPLATE PRD` → Product Requirements Document
- `TEMPLATE PDR` → Product Design & Requirements
- `TEMPLATE SESSION-HANDOFF` → Session handoff template
- `TEMPLATE ISSUE` → GitHub issue template
- `TEMPLATE PR` → Pull request template
- `TEMPLATE COMMIT` → Commit message template
- `TEMPLATE WRITING` → Anti-AI writing style guidelines

This enables Doctor Hubert to instantly retrieve templates without navigating GitHub URLs.

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
├── .github/
│   └── workflows/                  # Reusable workflows
│       ├── Phase 1: PR Validation
│       ├── python-test-reusable.yml
│       ├── shell-quality-reusable.yml
│       ├── conventional-commit-check-reusable.yml
│       ├── session-handoff-check-reusable.yml
│       ├── block-ai-attribution-reusable.yml
│       ├── pr-title-check-reusable.yml
│       ├── protect-master-reusable.yml
│       ├── pre-commit-check-reusable.yml
│       ├── commit-quality-check-reusable.yml
│       ├── Phase 2: Issue Validation
│       ├── issue-ai-attribution-check-reusable.yml
│       ├── issue-format-check-reusable.yml
│       ├── issue-prd-reminder-reusable.yml
│       └── issue-auto-label-reusable.yml
├── workflow-templates/             # Templates for GitHub UI
│   ├── python-ci.yml
│   ├── python-ci.properties.json
│   ├── shell-ci.yml
│   └── shell-ci.properties.json
├── docs/
│   └── templates/                  # Centralized project templates
│       ├── PRD-template.md
│       ├── PDR-template.md
│       ├── session-handoff-template.md
│       ├── github-issue-template.md
│       ├── github-issue-prd-template.md
│       ├── github-issue-pdr-template.md
│       ├── github-pr-template.md
│       ├── github-commit-template.md
│       └── WRITING_STYLE.md
├── profile/
│   └── README.md                   # GitHub profile showcase
├── CLAUDE.md                       # Development guidelines
├── PDR-commit-cleanup-workflow-2025-10-10.md  # Phase 1 design doc
└── README.md                       # This file
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

## Testing Workflow Changes

**CRITICAL**: All workflow changes MUST be tested before merging.

### Method 1: Local Testing with act (Optional - Quick Iteration)

```bash
# Install act (nektos/act)
brew install act  # macOS
# or: https://github.com/nektos/act#installation

# List available workflows
act -l

# Simulate pull_request event
act pull_request

# Test specific workflow
act -W .github/workflows/python-test-reusable.yml
```

**Limitations**: Some GitHub Actions features may not work identically in act.

### Method 2: Test Repository (Required Before Merge)

1. **Create test repository** or use existing project
2. **Add workflow** that uses modified reusable workflow:
```yaml
# .github/workflows/test-reusable.yml
name: Test Reusable Workflow
on: [push, pull_request]

jobs:
  test:
    uses: maxrantil/.github/.github/workflows/python-test-reusable.yml@your-branch-name
    with:
      python-version: '3.11'
```
3. **Push to test repository** and verify workflow runs successfully
4. **Test edge cases** with different input combinations
5. **Verify all jobs pass** and output is as expected

### Method 3: Gradual Rollout (For Breaking Changes)

1. Create new semantic version tag (e.g., `v2.0.0`)
2. Update **one** consuming repository to use new version
3. Validate thoroughly in real-world usage
4. Update other repositories once proven stable
5. Update `@main` pointer after validation

### Pre-Merge Checklist

- [ ] Workflow tested in real repository (not just locally)
- [ ] Edge cases validated (different inputs, failure modes)
- [ ] Documentation updated (README, input descriptions)
- [ ] Breaking changes documented with migration guide
- [ ] Session handoff created per CLAUDE.md requirements

## Contributing

1. Create feature branch: `feat/improve-python-workflow`
2. Make changes to workflows
3. **Test in sandbox repository** (see Testing section above)
4. Update README.md with any new inputs or behavior changes
5. Create PR with session handoff documentation
6. Merge to master after validation

## License

Same as individual projects (varies by repository).

## Questions?

Check the [project-templates README](https://github.com/maxrantil/project-templates) for more context on the overall workflow philosophy.
