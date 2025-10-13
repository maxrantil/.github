# Development Guidelines

## 🎯 PROJECT STATUS & CONTEXT

**Project**: .github (Special GitHub Repository)
**Purpose**: Centralized reusable workflows and templates for all maxrantil repositories
**Current Status**: Initial release with 4 reusable workflows + 2 templates
**Active Branch**: master
**Last Updated**: 2025-10-09

**Key Documentation**:
- `README.md` - Complete usage guide and API reference
- `profile/README.md` - GitHub profile showcase
- Individual workflow files - ABOUTME headers explain purpose

---

## 🚨 QUICK START CHECKLIST

**Before ANY work:**

1. **PRD/PDR Required?** New features/UX changes → PRD first. Approved PRDs → PDR next.
2. **GitHub Issue** (after PRD/PDR if applicable)
3. **Feature Branch** (`feat/issue-123-description`) - NEVER commit to master
4. **Agent Analysis** (see trigger rules in Section 2)
5. **TDD Cycle** (failing test → minimal code → refactor) - Test workflows before merging!
6. **Draft PR** (early visibility)
7. **Agent Validation** (MANDATORY before marking ready)
8. **Close Issue** (verify completion)
9. **🚨 Session Handoff** (MANDATORY after issue completion - see Section 5)

---

## 1. WORKFLOW ESSENTIALS

### Git Workflow

#### **1. Planning Phase**

- **Create GitHub issue ONLY after PRD/PDR approval** (if required)
- **Consider impact**: Workflow changes affect ALL consuming repositories
- Issue describes workflow changes and migration path

#### **2. Branch Setup**

- **NEVER commit directly to `master`**
- Create descriptive branch: `feat/add-rust-workflow`, `fix/python-uv-support`
- Reference issue in branch name: `feat/issue-123-description`

#### **3. Development Phase**

- **Test workflow changes** in a sandbox repository BEFORE merging
- **Document breaking changes** clearly in PR and CHANGELOG
- Make atomic commits (one logical change per commit)
- **Commit cleanup**: Draft PRs allow messy commits during iteration (fixups, typos, CI fixes)
- **NEVER use `--no-verify`** to bypass hooks
- **NEVER include co-author or tool attribution**

**Automated Commit Quality Check**:
- `commit-quality-check-reusable.yml` analyzes commits automatically
- Detects fixup patterns: `fixup`, `wip:`, `oops`, `typo`, `ci `, `lint`
- Posts comment with ready-to-run cleanup script
- Provides three options: script, manual rebase, or squash-on-merge
- **Read-only**: Never modifies commits (Phase 1 - guidance only)

#### **4. Review Phase**

- **Pull requests** for all changes (draft early, ready when complete)
- Use commit/PR messages like `Fixes #123` for auto-linking
- **Include migration guide** if workflows change
- **Commit cleanup before merge**:
  - Run provided cleanup script from workflow comment (if suggested)
  - OR use GitHub's "Squash and merge" button
  - OR keep commits if they're already clean
- Squash only when merging to `master` (commits should be clean by this point)

#### **5. Completion Phase**

- **Verify issue closure** after PR merge
- **Update consuming repositories** if breaking changes introduced
- **🚨 MANDATORY: Complete session handoff** (see Section 5)

---

### Session Handoff Integration (Quick Reference)

**CRITICAL: This is NON-NEGOTIABLE. Perform handoff after EACH GitHub issue - no exceptions!**

**✅ ALWAYS perform handoff when:**
- ✅ **Any GitHub issue closed/completed** (regardless of size) ← **PRIMARY TRIGGER**
- ✅ **Any PR merged to master**
- ✅ **Work session ending** (even if work incomplete)
- ✅ **Major documentation created** (new workflow, breaking changes)

**Quick Handoff Checklist:**
1. ✅ Issue work completed (workflow changes tested)
2. ✅ Draft PR created and pushed to GitHub
3. ✅ Workflows tested in sandbox repository
4. ✅ Session handoff document created/updated
5. ✅ 5-10 line startup prompt generated for next session
6. ✅ Clean working directory (no uncommitted changes)

**📋 See Section 5 for complete Session Handoff Protocol**

---

### Test-Driven Development (NON-NEGOTIABLE)

**For workflow development:**

1. **RED** - Create test repository with failing workflow
2. **GREEN** - Modify workflow to make it pass
3. **REFACTOR** - Improve workflow while tests pass
4. **NEVER** merge workflow changes without testing in real repository

**Testing Methods:**
- Local testing with `act` (nektos/act)
- Test repository on GitHub
- Validate in existing projects before release

---

## 2. AGENT INTEGRATION

### Context Triggers

**MANDATORY: Claude must invoke these agents when context matches:**

- **Multi-workflow changes, new templates** → `architecture-designer`
- **Credentials, secrets, permissions** → `security-validator`
- **All workflow modifications** → `code-quality-analyzer`
- **Performance keywords** (timeout, caching, optimization) → `performance-optimizer`
- **Deploy/infrastructure mentions** → `devops-deployment-agent`
- **Testing workflows, validation** → `test-automation-qa`
- **Documentation changes, README updates** → `documentation-knowledge-manager`

### Validation Requirements

**Post-Implementation Validation:**
All relevant agents must validate final implementation before marking work complete.

**Critical for .github repository:**
- **architecture-designer**: Ensure workflows are composable and maintainable
- **documentation-knowledge-manager**: Update README and examples
- **devops-deployment-agent**: Validate CI/CD patterns and best practices

---

## 3. CODE STANDARDS

### Workflow Principles

- Simple, reusable workflows over complex ones
- Sensible defaults with customization options
- Clear input/output contracts
- **NEVER hardcode versions** without parameterization
- **NEVER break existing consumers** without migration guide

### File Requirements

- All workflow files start with: `# ABOUTME: [description]`
- All documentation files start with: `# ABOUTME: [description]`
- Evergreen comments (describe current state)
- Ask before making breaking changes

### YAML Standards

- Use workflow_call for reusable workflows
- Provide clear input descriptions
- Set sensible defaults for all inputs
- Document expected behavior in comments
- Use descriptive job and step names

---

## 4. PROJECT MANAGEMENT

### Documentation Standards

```
.github/
├── README.md                       # Complete usage guide
├── CLAUDE.md                       # This file
├── SESSION_HANDOVER.md             # Session continuity
├── workflows/                      # Reusable workflows (workflow_call)
│   ├── python-test-reusable.yml
│   ├── shell-quality-reusable.yml
│   ├── conventional-commit-check-reusable.yml
│   └── session-handoff-check-reusable.yml
├── workflow-templates/             # GitHub UI templates
│   ├── python-ci.yml
│   ├── python-ci.properties.json
│   ├── shell-ci.yml
│   └── shell-ci.properties.json
├── profile/
│   └── README.md                   # GitHub profile
└── .gitignore
```

### README.md Requirements (Living Document)

Must include and keep updated:
- Purpose and benefits of centralized workflows
- Complete API documentation for all reusable workflows
- Usage examples for each workflow
- Migration guides for breaking changes
- Troubleshooting section
- Version strategy (tags, @main, pinned commits)

**Update after**: New workflows, breaking changes, input parameter changes

---

## 5. SESSION COMPLETION & HANDOFF PROCEDURES

### **🚨 MANDATORY Session Handoff Triggers**

**ALWAYS invoke the Session Handoff Protocol when ANY of these occur:**

1. ✅ **Any GitHub issue closed/completed** (regardless of size) ← **MOST COMMON TRIGGER**
2. ✅ **Any PR merged to master**
3. ✅ **Work session ending** (even if work incomplete)
4. ✅ **Major workflow created or modified**

**🎯 Golden Rule: If you're asking yourself "Should I do handoff?" → The answer is YES**

---

### **MANDATORY Session Completion Protocol**

Follow CLAUDE.md Section 5 template from parent guidelines.

**Additional .github-specific requirements:**

- Document which consuming repositories may be affected
- List breaking changes and migration steps
- Note any new inputs or changed defaults
- Reference test repositories used for validation

---

## 6. EMERGENCY PROCEDURES

### When Workflows Break in Production

1. **Assess impact** - Which repositories are affected?
2. **Create hotfix branch** from master
3. **Minimal fix only** (no scope creep)
4. **Test in affected repository** before merging
5. **Fast-track PR** (notify Doctor Hubert)
6. **Notify affected repositories** if action needed
7. **Post-mortem** after resolution

### Getting Help

- **Breaking change concerns**: Ask Doctor Hubert before merging
- **Testing strategy unclear**: Use test-automation-qa agent
- **Impact assessment**: Use architecture-designer agent

---

## 7. TECHNOLOGY REFERENCES

@~/.claude/docs/using-uv.md

**GitHub Actions Documentation**:
- [Reusing workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows)
- [Creating workflow templates](https://docs.github.com/en/actions/using-workflows/creating-starter-workflows-for-your-organization)
- [Special .github repository](https://docs.github.com/en/communities/setting-up-your-project-for-healthy-contributions/creating-a-default-community-health-file)

---

## 8. RELATIONSHIP & COMMUNICATION

- Address as "Doctor Hubert" (ALWAYS)
- We're coworkers/teammates
- Irreverent humor welcome when not blocking work
- Ask for help rather than struggling alone

### Quick Command Reference

**Doctor Hubert can use these trigger phrases:**

- **"HANDOFF"** → Immediately trigger session handoff protocol
- **"MANDATORY-AGENTS"** → Force immediate agent analysis
- **"CROSS-VALIDATE"** → Run all validation agents on current state
- **"TEMPLATE [name]"** → Fetch and display specific template from docs/templates/

### Template Command Usage

When Doctor Hubert says **"TEMPLATE [name]"**, immediately fetch and display the requested template.

**Available Templates:**
- `TEMPLATE PRD` → Product Requirements Document template
- `TEMPLATE PDR` → Product Design & Requirements template
- `TEMPLATE SESSION-HANDOFF` → Session handoff template
- `TEMPLATE ISSUE` → GitHub issue template with TDD checklist
- `TEMPLATE PR` → GitHub pull request template
- `TEMPLATE COMMIT` → Commit message template
- `TEMPLATE WRITING` → Anti-AI writing style guidelines

**Example:**
```
Doctor Hubert: "TEMPLATE PRD"
Claude: [Reads and displays /home/mqx/workspace/.github/docs/templates/PRD-template.md]
```

**Template URLs** (for reference in other repos):
- All templates: https://github.com/maxrantil/.github/tree/main/docs/templates
- PRD: https://github.com/maxrantil/.github/blob/main/docs/templates/PRD-template.md
- PDR: https://github.com/maxrantil/.github/blob/main/docs/templates/PDR-template.md
- Session Handoff: https://github.com/maxrantil/.github/blob/main/docs/templates/session-handoff-template.md
- Issue: https://github.com/maxrantil/.github/blob/main/docs/templates/github-issue-template.md
- PR: https://github.com/maxrantil/.github/blob/main/docs/templates/github-pr-template.md
- Writing Style: https://github.com/maxrantil/.github/blob/main/docs/templates/WRITING_STYLE.md

---

## 9. PROJECT-SPECIFIC NOTES

### .github Repository (Special Repository)

**Purpose**: Centralized reusable workflows and templates
**Approach**: Single source of truth for CI/CD patterns across all repositories
**Philosophy**: Update once, benefit everywhere

**Key Principles:**

1. **Backward Compatibility**: Don't break existing consumers
2. **Semantic Versioning**: Use tags for stable versions (@v1, @v1.2.3)
3. **Clear Defaults**: Workflows should work out-of-box with sensible defaults
4. **Comprehensive Documentation**: Every input parameter documented
5. **Testing Required**: All workflow changes must be tested in real repository

**Consuming Repositories:**
- protonvpn-manager (13 workflows → can reduce to 3-4)
- vm-infra
- dotfiles
- project-templates

**Workflow Testing Strategy:**

1. **Local Testing** (optional, for quick iteration):
   ```bash
   # Install act (nektos/act)
   act -l  # List workflows
   act pull_request  # Simulate PR event
   ```

2. **Test Repository** (required before merge):
   - Create test repository or branch
   - Add workflow that uses modified reusable workflow
   - Verify workflow runs successfully
   - Test with different input combinations

3. **Gradual Rollout** (for breaking changes):
   - Create new version tag (v2.0.0)
   - Update one consuming repository
   - Validate thoroughly
   - Update others once proven stable

**Breaking Changes Protocol:**

1. Document in PR description
2. Create migration guide in README
3. Test in at least 2 consuming repositories
4. Create new semantic version tag
5. Update CHANGELOG (if exists)
6. Notify in consuming repository PRs/issues

---

## 10. WORKFLOW QUALITY STANDARDS

### Reusable Workflow Checklist

Every reusable workflow MUST have:

- ✅ `# ABOUTME:` header describing purpose
- ✅ `workflow_call` trigger
- ✅ Clear input descriptions
- ✅ Sensible defaults for all inputs
- ✅ Type declarations for inputs (`string`, `boolean`, `number`)
- ✅ Working directory support (if applicable)
- ✅ Clear job and step names
- ✅ Documentation in README.md with usage example

### Template Quality Checklist

Every workflow template MUST have:

- ✅ Companion `.properties.json` file
- ✅ Clear name and description in properties
- ✅ Appropriate iconName and categories
- ✅ Example showing reusable workflow usage
- ✅ Comments explaining customization points
- ✅ Reference to main documentation

### Documentation Quality Standards

- All inputs documented with type and default
- Usage examples for common scenarios
- Troubleshooting section for common issues
- Migration guide for version updates
- Link to GitHub Actions documentation

---

## 11. VERSION MANAGEMENT

### Current Strategy

**@main** (default): Latest version, auto-updates
- Use for: Active development, non-critical projects
- Risk: Breaking changes propagate immediately
- Benefit: Always get latest improvements

**Tagged Versions** (recommended for production):
- Use for: Production systems, stable projects
- Format: `@v1`, `@v1.2.3` (semantic versioning)
- Risk: Must manually update
- Benefit: Predictable, controlled updates

**Commit SHAs** (for absolute stability):
- Use for: Critical systems, compliance requirements
- Format: `@abc1234567890`
- Risk: No updates without explicit change
- Benefit: Immutable, reproducible builds

### Creating New Versions

```bash
# Minor update (backward compatible)
git tag v1.1.0
git push origin v1.1.0

# Major update (breaking changes)
git tag v2.0.0
git push origin v2.0.0

# Update major version tag
git tag -f v2
git push -f origin v2
```

---

**This CLAUDE.md is specific to the special .github repository. Follow it religiously for maintaining high-quality, reusable workflows that serve all maxrantil repositories.**
