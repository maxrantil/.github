# ABOUTME: Comprehensive testing documentation for reusable workflows
# Workflow Testing Guide

This document provides a comprehensive test plan for all reusable workflows in the `.github` repository.

## Testing Philosophy

**Goal**: Ensure each workflow has clear pass/fail visibility and catches violations correctly.

**Approach**: Test both success and failure scenarios for each workflow before rolling out to production repositories.

---

## Current Workflows (Phase 1)

### 1. Block AI Attribution (`block-ai-attribution-reusable.yml`)

**Purpose**: Prevents AI tool names and agent mentions from appearing in commit messages.

**Test Scenarios**:

#### ✅ Should Pass:
- Clean commit messages without AI mentions
- Commits with human co-authors
- Commits mentioning "agent" in unrelated context (e.g., "user agent", "shipping agent")

#### ❌ Should Fail:
- Commits with "Co-authored-by: Claude"
- Commits with "Generated with ChatGPT"
- Commits with "Reviewed by architecture-designer agent"
- Commits with "Validated by security-validator"
- Commits with "agent validation completed"
- Commits with "claude.com/claude-code" links

**Test Status**:
- ✅ Pass case validated (PR #37 in dotfiles)
- ✅ Fail case validated (PR #38 in dotfiles - detected "Reviewed by architecture-designer agent")

**Edge Cases to Test**:
- [ ] Multiple violations in one commit
- [ ] Violations in commit body (not just subject)
- [ ] Case sensitivity ("CLAUDE" vs "Claude")

---

### 2. PR Title Check (`pr-title-check-reusable.yml`)

**Purpose**: Validates PR titles follow conventional commit format.

**Test Scenarios**:

#### ✅ Should Pass:
- `feat: add new feature`
- `fix(api): resolve timeout issue`
- `docs: update installation guide`
- `feat!: breaking change`
- `chore(deps): update dependencies`

#### ❌ Should Fail:
- `Add new feature` (no type)
- `feature: add new feature` (invalid type)
- `fix:missing space`
- `fix` (no description)
- `This is not a valid conventional commit title`

**Test Status**:
- ✅ Pass case validated (PR #37 - title "test: validate centralized reusable workflows")
- ✅ Fail case validated (PR #39 - title "This is not a valid conventional commit title")

**Edge Cases to Test**:
- [ ] Very long titles
- [ ] Special characters in scope
- [ ] Multiple scopes `feat(api,ui): change`
- [ ] Scope with spaces

---

### 3. Protect Master Branch (`protect-master-reusable.yml`)

**Purpose**: Blocks direct pushes to master branch (only allows PR merges).

**Test Scenarios**:

#### ✅ Should Pass:
- Push from PR merge (commit message contains `(#123)` or starts with `Merge pull request`)
- Normal PR workflow

#### ❌ Should Fail:
- Direct `git push origin master` without PR
- Force push to master
- Commit pushed to master that doesn't come from PR merge

**Test Status**:
- ⏭️ Correctly skips on PR events (PR #37, #38, #39)
- ⚠️ **Not yet tested**: Actual push to master (need careful testing approach)

**Testing Approach**:
1. Create test repository or use non-critical branch
2. Attempt direct push to master
3. Verify workflow blocks it
4. Then merge via PR and verify it allows

**Edge Cases to Test**:
- [ ] Squash merge (commit message format)
- [ ] Rebase merge (commit message format)
- [ ] Fast-forward merge
- [ ] Hotfix scenarios

---

### 4. Pre-commit Check (`pre-commit-check-reusable.yml`)

**Purpose**: Runs pre-commit hooks in CI to catch bypassed hooks (`--no-verify`).

**Test Scenarios**:

#### ✅ Should Pass:
- Code that passes all pre-commit hooks
- Commits made with pre-commit hooks enabled

#### ❌ Should Fail:
- Code with trailing whitespace (if hook configured)
- Code with formatting issues (if hook configured)
- Code that would fail pre-commit but was committed with `--no-verify`

**Test Status**:
- ⚠️ **Not tested**: Requires repository with `.pre-commit-config.yaml`
- ℹ️ Dotfiles has pre-commit but workflow not enabled yet

**Testing Approach**:
1. Use repository with pre-commit configured (protonvpn-manager)
2. Create commit that violates pre-commit rules
3. Use `--no-verify` to bypass locally
4. Verify CI catches it

**Edge Cases to Test**:
- [ ] Multiple pre-commit hook failures
- [ ] Hooks that modify files
- [ ] Slow-running hooks (timeout behavior)

---

## Test Matrix Summary

| Workflow | Pass Tested | Fail Tested | Edge Cases | Production Ready |
|----------|-------------|-------------|------------|------------------|
| Block AI Attribution | ✅ | ✅ | ⚠️ Partial | ✅ Yes |
| PR Title Check | ✅ | ✅ | ⚠️ Partial | ✅ Yes |
| Protect Master | ⏭️ Skips only | ❌ No | ❌ No | ⚠️ Needs testing |
| Pre-commit Check | ❌ No | ❌ No | ❌ No | ❌ Not tested |

---

## Testing in dotfiles (Test Repository)

**PRs Created for Testing**:
- PR #36: Initial test (auto-merged before workflows on master)
- PR #37: ✅ Success case - all workflows passed
- PR #38: ❌ Failure case - AI attribution detected
- PR #39: ❌ Failure case - invalid PR title

**Status**: dotfiles serves as Phase 1 test bed ✅

---

## Recommended Testing Before Rollout

### Immediate Tests Needed:

1. **Protect Master Workflow**:
   - Create test branch in dotfiles
   - Test direct push blocking
   - Test PR merge allowing

2. **Pre-commit Check Workflow**:
   - Enable in dotfiles (has pre-commit config)
   - Test with valid and invalid code

3. **Edge Cases**:
   - Multiple violations in one commit
   - Very long titles/messages
   - Special characters

### Before Production Rollout:

1. ✅ All workflows tested in dotfiles (success + failure)
2. ✅ Edge cases documented and tested
3. ✅ Clear error messages verified
4. ✅ Performance acceptable (< 10s per check)
5. ✅ Documentation complete in README.md

---

## Testing Checklist for New Workflows

When adding new workflows, test:

- [ ] Success case (workflow passes)
- [ ] Failure case (workflow fails with clear message)
- [ ] Edge case: empty input
- [ ] Edge case: very large input
- [ ] Edge case: special characters
- [ ] Performance: completes in < 30s
- [ ] Error messages: clear and actionable
- [ ] Documentation: README updated with examples

---

## Automated Testing (Future)

**Potential Automation**:
- Script to create test PRs with various scenarios
- Automated validation that workflows run
- Check that error messages are clear
- Performance monitoring

**Tools to Consider**:
- `act` (nektos/act) for local testing
- GitHub Actions testing framework
- Shell scripts for scenario generation

---

## Next Phase: Issue Workflows

Once current workflows are fully tested, add:

1. **issue-ai-attribution-check**: Block AI mentions in issues
2. **issue-format-check**: Validate issue completeness
3. **issue-auto-label**: Auto-label based on content
4. **issue-prd-reminder**: Remind about PRD/PDR workflow

Each will need similar test coverage before rollout.

---

## Lessons Learned

### Setup Issues Encountered:
1. ❌ Workflows initially in `workflows/` instead of `.github/workflows/`
2. ❌ Referenced `@main` instead of `@master`
3. ❌ Double `.github` in workflow paths
4. ✅ Fixed: All workflows now in correct location with correct refs

### Best Practices:
- ✅ Test in dotfiles before rolling out
- ✅ One clear action per workflow file
- ✅ Clear error messages with policy guidance
- ✅ Document expected behavior upfront

---

## Contact

Questions about testing? Check `CLAUDE.md` or create an issue.

**Last Updated**: 2025-10-09
**Test Repository**: dotfiles
**Production Repositories**: 5 repos (rollout pending)
