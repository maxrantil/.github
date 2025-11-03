# Next Session Startup Prompt

**Date**: 2025-11-03
**Repository**: .github (Special GitHub Repository - Reusable Workflows)
**Current State**: All core workflows COMPLETE (100%)
**Next Work**: Deploy workflows to consuming repositories

---

## 🎯 Session Goal

Deploy remaining workflows to consuming repositories:
1. **Issue #42**: Terraform validation to vm-infra (~15 min)
2. **Issue #40**: Secret scanning to textile-showcase & vm-infra (~45 min)

---

## 📊 Current Status

### Core Workflow Suite: ✅ COMPLETE (8/8 issues)

**Completed Work**:
- ✅ Issue #1: Profile README (PRs #36, #37)
- ✅ Issue #4: Workflow caching (PR #32)
- ✅ Issue #34: CI pipeline fix (PR #33)
- ✅ Issue #5: Terraform validation workflow (PR #41)
- ✅ Issue #6: Ansible lint workflow (PR #43)
- ✅ Issue #7: Secret scanning workflow (PR #39 + 4 deployments)
- ✅ Issue #44: Ansible docs enhancement (PR #45)

**Repository State**:
- Branch: master
- Status: Clean working directory
- Last commit: 64649bf
- Workflows: 16 total (100% validated)
- Documentation: Gold standard quality

### Open Deployment Issues (2 remaining)

**Issue #42**: Deploy Terraform Validation to vm-infra
- Priority: MEDIUM
- Complexity: LOW
- Time: ~15 minutes
- Repository: vm-infra

**Issue #40**: Deploy Secret Scanning to textile-showcase & vm-infra
- Priority: LOW
- Complexity: LOW-MEDIUM
- Time: ~30-45 minutes per repository
- Repositories: textile-showcase, vm-infra

---

## 🚀 Quick Start Commands

```bash
# View Issue #42 details
gh issue view 42 --repo maxrantil/.github

# Switch to vm-infra repository for Issue #42
cd /home/mqx/workspace/vm-infra

# View Issue #40 details
gh issue view 40 --repo maxrantil/.github
```

---

## 📋 Issue #42: Deploy Terraform Validation to vm-infra

**Estimated Time**: 15 minutes
**Priority**: MEDIUM (quick win)
**Repository**: vm-infra

### Context
- Terraform validation workflow exists in .github repository (PR #41 - merged)
- vm-infra has Terraform configurations needing validation
- Simple workflow addition - no code changes needed

### Implementation Steps

1. **Switch to vm-infra repository**
   ```bash
   cd /home/mqx/workspace/vm-infra
   git status
   git checkout master && git pull
   ```

2. **Read issue for complete details**
   ```bash
   gh issue view 42 --repo maxrantil/.github
   ```

3. **Create feature branch**
   ```bash
   git checkout -b feat/add-terraform-validation
   ```

4. **Create workflow file**: `.github/workflows/terraform-validation.yml`

   Template provided in Issue #42:
   ```yaml
   # ABOUTME: Validates Terraform configurations using centralized reusable workflow
   name: Terraform Validation

   on:
     pull_request:
       branches: [master, main]
       paths:
         - 'terraform/**'
         - '.github/workflows/terraform-validation.yml'
     push:
       branches: [master, main]
       paths:
         - 'terraform/**'

   jobs:
     validate:
       name: Terraform Validation
       uses: maxrantil/.github/.github/workflows/terraform-validate-reusable.yml@main
       with:
         working-directory: 'terraform'
         terraform-version: 'latest'
         run-plan: false
   ```

5. **Commit and push**
   ```bash
   git add .github/workflows/terraform-validation.yml
   git commit -m "feat: add Terraform validation workflow

   Integrates terraform-validate-reusable.yml from maxrantil/.github.

   Validation includes:
   - terraform fmt -check -recursive
   - terraform init -backend=false
   - terraform validate

   Workflow triggers on changes to terraform/** paths.

   Related: maxrantil/.github#42"

   git push -u origin feat/add-terraform-validation
   ```

6. **Create PR in vm-infra**
   ```bash
   gh pr create --title "feat: add Terraform validation workflow" \
     --body "## Summary

   Integrates Terraform validation from maxrantil/.github reusable workflow.

   Related: maxrantil/.github#42

   ## Validation Checks
   - ✅ Terraform fmt (format check)
   - ✅ Terraform init (initialization without backend)
   - ✅ Terraform validate (configuration validation)

   ## Test Plan
   - [ ] Workflow triggers on this PR
   - [ ] All Terraform validation steps pass
   - [ ] Path filtering works (only runs on terraform/** changes)

   ## Benefits
   - Catches Terraform syntax errors in CI
   - Enforces consistent formatting
   - Prevents invalid configurations from merging"
   ```

7. **Verify workflow runs successfully**
   - Check PR for workflow run
   - Verify all 3 validation steps pass
   - Review any errors and fix if needed

8. **Merge PR to vm-infra master**
   ```bash
   gh pr merge --squash --delete-branch
   ```

9. **Update Issue #42 in .github repository**
   ```bash
   cd /home/mqx/workspace/.github
   gh issue comment 42 --body "✅ Deployed to vm-infra

   PR: [link to vm-infra PR]
   Status: COMPLETE
   All validation steps passing"

   gh issue close 42
   ```

### Success Criteria
- ✅ Workflow file created in vm-infra
- ✅ Workflow triggers on PR to terraform/** paths
- ✅ All validation steps pass (fmt, init, validate)
- ✅ PR merged to vm-infra master
- ✅ Issue #42 closed in .github repository

---

## 📋 Issue #40: Deploy Secret Scanning

**Estimated Time**: 30-45 minutes per repository
**Priority**: LOW
**Repositories**: textile-showcase, vm-infra

### Context
- Secret scanning workflow exists in .github repository (PR #39 - merged)
- Workflow uses Gitleaks to detect accidentally committed secrets
- Already deployed to: project-templates, dotfiles, protonvpn-manager
- Found real secrets in testing (9 total): GPG keys, API keys, VPN configs

### Implementation Steps (Per Repository)

#### Part 1: textile-showcase

1. **Switch to repository**
   ```bash
   cd /home/mqx/workspace/textile-showcase
   git checkout master && git pull
   git checkout -b feat/add-secret-scanning
   ```

2. **Create workflow file**: `.github/workflows/secret-scan.yml`
   ```yaml
   name: Secret Scanning

   on:
     push:
       branches: [master]
     pull_request:
       branches: [master]

   permissions:
     contents: read

   jobs:
     secret-scan:
       name: Scan for Secrets
       uses: maxrantil/.github/.github/workflows/secret-scan-reusable.yml@master
       with:
         gitleaks-version: 'latest'
         fail-on-error: true
   ```

3. **Create config file**: `.gitleaks.toml`
   ```toml
   title = "Gitleaks Config for textile-showcase"

   [extend]
   useDefault = true

   [allowlist]
   description = "Allow known false positives"
   paths = [
     '''README\.md$''',
     '''docs/.*\.md$''',
     '''\.github/workflows/.*''',
   ]

   regexes = [
     '''example\.com''',
     '''REPLACEME''',
     '''EXAMPLE_.*''',
   ]
   ```

4. **Test locally (optional)**
   ```bash
   # If you want to see what secrets might be detected before PR
   docker run --rm -v $(pwd):/repo zricethezav/gitleaks:latest \
     detect --source /repo --config /repo/.gitleaks.toml --verbose
   ```

5. **Create PR**
   ```bash
   git add .github/workflows/secret-scan.yml .gitleaks.toml
   git commit -m "feat: add secret scanning workflow"
   git push -u origin feat/add-secret-scanning

   gh pr create --title "feat: add secret scanning workflow" \
     --body "Integrates secret scanning from maxrantil/.github.

     Related: maxrantil/.github#40"
   ```

6. **Handle any findings**
   - If secrets detected: Review each one
   - False positives: Add to `.gitleaks.toml` allowlist
   - Real secrets: Rotate immediately, then allowlist the file/pattern
   - Commit fixes, push again

7. **Merge when clean**
   ```bash
   gh pr merge --squash --delete-branch
   ```

#### Part 2: vm-infra

**Repeat same process for vm-infra** with repository-specific `.gitleaks.toml`:

```toml
title = "Gitleaks Config for vm-infra"

[extend]
useDefault = true

[allowlist]
description = "Allow known false positives"
paths = [
  '''README\.md$''',
  '''docs/.*\.md$''',
  '''\.github/workflows/.*''',
  '''terraform/.*\.example$''',  # Example files
  '''ansible/inventory\.example$''',  # Example inventory
]

regexes = [
  '''example\.com''',
  '''REPLACEME''',
  '''EXAMPLE_.*''',
  '''your-.*-here''',
]
```

**Note**: vm-infra may have more false positives due to:
- Terraform example files
- Ansible inventory examples
- Documentation with example credentials

8. **Update Issue #40 when both complete**
   ```bash
   cd /home/mqx/workspace/.github
   gh issue comment 40 --body "✅ Deployed to both repositories

   **textile-showcase**: [PR link]
   - Secrets found: [count]
   - Status: COMPLETE

   **vm-infra**: [PR link]
   - Secrets found: [count]
   - Status: COMPLETE"

   gh issue close 40
   ```

### Success Criteria
- ✅ Secret scanning active in textile-showcase
- ✅ Secret scanning active in vm-infra
- ✅ Both PRs merged without blocking false positives
- ✅ Any real secrets found are rotated/allowlisted
- ✅ Issue #40 closed in .github repository

---

## 📝 Session Workflow

### Recommended Order

1. **Issue #42 first** (15 min)
   - Quick win
   - Simpler (no custom config needed)
   - Builds momentum

2. **Issue #40 second** (45 min)
   - More complex (custom config required)
   - May need iteration to handle findings
   - Two repositories to deploy

### During Work

**Track progress with TodoWrite**:
```
- Deploy Terraform validation to vm-infra
- Deploy secret scanning to textile-showcase
- Deploy secret scanning to vm-infra
- Close Issue #42
- Close Issue #40
- Update SESSION_HANDOVER.md
```

**Follow CLAUDE.md standards**:
- ✅ Feature branches for all changes
- ✅ Conventional commits
- ✅ Test workflows before merging
- ✅ Session handoff after completion

### After Completion

**Update .github repository documentation**:

1. Create session handoff: `docs/implementation/session-handoff-deployments-2025-11-03.md`
2. Update `SESSION_HANDOVER.md` with deployment results
3. Commit and push to .github master

**Final state**:
- ✅ All 8 core issues complete
- ✅ All 2 deployment issues complete
- ✅ 100% workflow suite deployed to all repositories
- ✅ .github repository feature-complete

---

## 🎯 Expected Outcomes

**After Issue #42**:
- vm-infra has automated Terraform validation
- Invalid Terraform configs blocked in CI
- Consistent formatting enforced

**After Issue #40**:
- textile-showcase protected from credential leaks
- vm-infra protected from credential leaks
- Secret scanning coverage: 6/6 repositories (100%)

**Final State**:
- ✅ Core workflow suite: 100% complete
- ✅ Deployment to consuming repos: 100% complete
- ✅ Documentation: Gold standard quality
- ✅ .github repository: Feature-complete

---

## 💡 Quick Reference

**Issue Links**:
- Issue #42: https://github.com/maxrantil/.github/issues/42
- Issue #40: https://github.com/maxrantil/.github/issues/40

**Documentation**:
- Terraform Validation: README.md lines 347-397
- Secret Scanning: README.md lines 287-346
- Session Handoffs: docs/implementation/

**Related PRs**:
- Terraform workflow: PR #41 (merged)
- Secret scanning workflow: PR #39 (merged)
- Previous deployments: PRs #13, #58, #117

---

**Start with Issue #42 for quick win, then tackle Issue #40. Low time-preference: test thoroughly, handle findings properly, document everything.**
