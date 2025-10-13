# Product Design & Requirements: Commit History Cleanup Workflow

**Date**: 2025-10-10
**Status**: Draft - Awaiting Doctor Hubert Approval
**Repository**: .github (centralized reusable workflows)
**Approach**: Low time-preference, phased rollout

---

## Problem Statement

PRs created by Claude Code often accumulate fixup commits during iteration:
1. Initial commit with implementation
2. CI fails → fixup commit
3. CI fails again → another fixup
4. Pre-commit catches issue → lint fix commit
5. Finally green → but 5+ messy commits

**Current pain**: Messy commit history in final PR, but manual cleanup is error-prone.

---

## Design Principles

Per Doctor Hubert's motto: "Low time-preference. Slow is smooth, smooth is fast."

1. **Start with validation, not automation**
2. **Preserve developer control**
3. **Explicit over implicit**
4. **Safe by default**
5. **Easy rollback**

---

## Recommended Solution: Two-Phase Approach

### Phase 1: Validation-Only Workflow (RECOMMENDED START)

**Timeline**: Week 1-2
**Risk**: Zero (read-only)

**Workflow**: `commit-quality-check-reusable.yml`

**Behavior**:
1. Analyzes PR commit history
2. Detects fixup patterns (`fix ci`, `oops`, `typo`, etc.)
3. Generates cleanup script for developer
4. Posts comment with:
   - Analysis (X commits, Y fixups, cleanup benefit score)
   - Ready-to-run cleanup script
   - Option to proceed manually or trigger automation (Phase 2)

**Benefits**:
- ✅ Provides awareness without forcing action
- ✅ Developers learn what "clean history" means
- ✅ Zero security risk (read-only)
- ✅ Preserves code review context
- ✅ Easy to test and iterate

**Implementation**:
```yaml
# .github/workflows/commit-quality-check-reusable.yml
name: Commit Quality Check (Reusable)

on:
  workflow_call:
    inputs:
      base-branch:
        type: string
        default: 'master'
      fail-on-fixups:
        type: boolean
        default: false
      suggest-cleanup:
        type: boolean
        default: true

jobs:
  analyze-commits:
    name: Analyze Commit Quality
    runs-on: ubuntu-latest
    permissions:
      pull-requests: write  # Comment only
      contents: read        # Read-only

    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Analyze commit history
        id: analyze
        run: |
          # Count commits
          COMMITS=$(git log origin/${{ inputs.base-branch }}..HEAD --oneline)
          COMMIT_COUNT=$(echo "$COMMITS" | wc -l)

          # Count fixup patterns
          FIXUP_COUNT=$(echo "$COMMITS" | grep -ciE "^[a-f0-9]+ (fixup|fix:|wip:|tmp:|oops|typo)" || echo "0")

          # Detect CI fix patterns
          CI_FIX_COUNT=$(echo "$COMMITS" | grep -ciE "(ci |lint |pre-commit)" || echo "0")

          HAS_FIXUPS=$([ "$FIXUP_COUNT" -gt 0 ] && echo "true" || echo "false")

          # Score cleanup benefit
          if [ "$COMMIT_COUNT" -gt 10 ] && [ "$FIXUP_COUNT" -gt 3 ]; then
            CLEANUP_SCORE="HIGH"
          elif [ "$FIXUP_COUNT" -gt 1 ]; then
            CLEANUP_SCORE="MEDIUM"
          else
            CLEANUP_SCORE="LOW"
          fi

          echo "commit_count=$COMMIT_COUNT" >> $GITHUB_OUTPUT
          echo "fixup_count=$FIXUP_COUNT" >> $GITHUB_OUTPUT
          echo "ci_fix_count=$CI_FIX_COUNT" >> $GITHUB_OUTPUT
          echo "has_fixups=$HAS_FIXUPS" >> $GITHUB_OUTPUT
          echo "cleanup_score=$CLEANUP_SCORE" >> $GITHUB_OUTPUT

          # Generate cleanup script
          if [ "$HAS_FIXUPS" = "true" ]; then
            cat > cleanup-script.sh << 'EOF'
#!/bin/bash
# Commit cleanup script - Run locally before merge
set -e

BRANCH=$(git branch --show-current)
BASE_BRANCH="origin/${{ inputs.base-branch }}"

echo "🔍 Current branch: $BRANCH"
echo "🔍 Base branch: $BASE_BRANCH"
echo ""

# Show commits to be squashed
echo "📋 Commits to be squashed:"
git log --oneline $BASE_BRANCH..HEAD
echo ""

# Confirm
read -p "Proceed with cleanup? (y/N) " -n 1 -r
echo
if [[ ! $REPLY =~ ^[Yy]$ ]]; then
    echo "Aborted"
    exit 1
fi

# Get first commit message (preserve the main one)
FIRST_MSG=$(git log $BASE_BRANCH..HEAD --reverse --format=%s | head -1)

# Soft reset to base
git reset --soft $BASE_BRANCH

# Create single clean commit
git commit -m "$FIRST_MSG"

echo ""
echo "✅ Cleanup complete!"
echo "📊 Result: $(git log --oneline $BASE_BRANCH..HEAD)"
echo ""
echo "⚠️  Next step: Force push with --force-with-lease"
echo "   git push --force-with-lease origin $BRANCH"
EOF
            chmod +x cleanup-script.sh
          fi

      - name: Post cleanup suggestion
        if: steps.analyze.outputs.has_fixups == 'true' && inputs.suggest-cleanup
        uses: actions/github-script@v7
        with:
          script: |
            const fs = require('fs');
            const commitCount = '${{ steps.analyze.outputs.commit_count }}';
            const fixupCount = '${{ steps.analyze.outputs.fixup_count }}';
            const cifixCount = '${{ steps.analyze.outputs.ci_fix_count }}';
            const cleanupScore = '${{ steps.analyze.outputs.cleanup_score }}';

            const script = fs.existsSync('cleanup-script.sh')
              ? fs.readFileSync('cleanup-script.sh', 'utf8')
              : 'Script generation failed';

            const comment = `## 🧹 Commit Cleanup Opportunity

**Analysis**:
- Total commits: ${commitCount}
- Fixup commits: ${fixupCount}
- CI fix commits: ${cifixCount}
- Cleanup benefit: **${cleanupScore}**

**Recommendation**: Consider cleaning up commits before merge for better git history.

### Option 1: Manual Cleanup (Recommended for Phase 1)

Run this script locally:

\`\`\`bash
${script}
\`\`\`

Or manually:
\`\`\`bash
# Interactive rebase
git rebase -i origin/${{ inputs.base-branch }}

# Squash fixup commits, clean messages
# Force push when done
git push --force-with-lease
\`\`\`

### Option 2: Automated Cleanup (Phase 2 - Coming Soon)

Comment \`/cleanup-commits\` to trigger automated cleanup.
*(Not yet available - validation phase only)*

### Option 3: Merge As-Is

Use "Squash and merge" button - GitHub will combine commits automatically.

---
*This is guidance only - choose the approach that fits your workflow.*
            `;

            await github.rest.issues.createComment({
              owner: context.repo.owner,
              repo: context.repo.repo,
              issue_number: context.issue.number,
              body: comment
            });

      - name: Fail if required
        if: steps.analyze.outputs.has_fixups == 'true' && inputs.fail-on-fixups
        run: |
          echo "❌ Fixup commits detected and fail-on-fixups is enabled"
          exit 1

      - name: Success
        if: steps.analyze.outputs.has_fixups == 'false'
        run: |
          echo "✅ No fixup commits detected - commit history looks clean"
```

**Usage in consuming repos**:
```yaml
# .github/workflows/ci.yml
jobs:
  commit-quality:
    uses: maxrantil/.github/.github/workflows/commit-quality-check-reusable.yml@main
    with:
      base-branch: 'master'
      fail-on-fixups: false  # Don't block, just inform
      suggest-cleanup: true
```

---

### Phase 2: Comment-Triggered Automation (OPTIONAL, WEEK 3-4)

**Decision Gate**: Only implement if Phase 1 shows high value and team wants automation.

**Trigger**: Comment `/cleanup-commits` on PR
**Risk**: Low (explicit command, audit trail)

**Workflow**: `commit-cleanup-reusable.yml`

**Behavior**:
1. Triggered by PR comment containing `/cleanup-commits`
2. Validates:
   - PR is in draft mode (safety check)
   - Comment author is PR owner or org admin
   - All CI checks passing
   - No recent pushes (5-minute window)
3. Performs cleanup:
   - Detects fixup patterns
   - Soft reset to base branch
   - Creates single clean commit
   - Force-pushes with `--force-with-lease`
4. Posts confirmation comment

**Safety features**:
- Dry-run option (`/cleanup-commits --dry-run`)
- Draft-only enforcement
- Authorization checks
- Audit trail in comments
- Force-with-lease (prevents data loss)

**Implementation**: See architecture agent's detailed implementation.

---

## Agent Validation Summary

### Security Validator: ✅ Approved with Mitigations
- **Phase 1**: No security concerns (read-only)
- **Phase 2**: 4/5 security score with proper authorization checks
- **Required mitigations**:
  - Verify PR author or admin only
  - Check branch protection status
  - Wait for security workflows to complete
  - Archive original commit history

### DevOps Agent: ✅ Strongly Prefers Phase 1
- **Phase 1**: Low operational risk, high value
- **Phase 2**: Acceptable if needed, but prefer validation-only
- **Key concern**: "Commit cleanup is inherently destructive"
- **Recommendation**: Start with Phase 1, only add Phase 2 if clear ROI

### Architecture Designer: ✅ Approved (4.5/5)
- **Phase 1**: Perfect fit with existing patterns
- **Phase 2**: Comment-based trigger matches industry standards
- **Pattern fit**: Same as Dependabot `/rebase`, K8s Prow `/test`
- **Extensible**: Can add more commands later (`/squash`, `/rebase`)

---

## Comparison to Alternatives

### Auto-Cleanup on CI Success
- **Rejected**: All agents scored 2-2.5/5
- **Issues**: Implicit behavior, race conditions, security risks
- **Verdict**: Violates "explicit over implicit" principle

### Label-Based Trigger
- **Score**: 3.4/5 (acceptable but not ideal)
- **Issues**: Label management overhead, less discoverable
- **Verdict**: Comment trigger is superior

### Manual Only
- **Score**: High purity (5/5) but low practicality (1/5)
- **Issues**: Doesn't scale, human error, toil accumulation
- **Verdict**: Phase 1 provides automation without risk

---

## Rollout Plan

### Week 1: Implementation
- [ ] Create `commit-quality-check-reusable.yml` in `.github` repo
- [ ] Test in `.github` repo itself (dogfooding)
- [ ] Verify script generation works correctly
- [ ] Test comment posting

### Week 2: Test in github-workflow-test Repository
- [ ] Add to `~/workspace/github-workflow-test/.github/workflows/ci.yml`
- [ ] Create test PR with intentional fixup commits
- [ ] Verify workflow runs and posts comment
- [ ] Verify cleanup script works correctly
- [ ] Test all three cleanup options
- [ ] Gather feedback from Doctor Hubert

### Week 3: Pilot in Real Repos
- [ ] Add to `vm-infra` workflow
- [ ] Add to `dotfiles` workflow
- [ ] Add to `project-templates` workflow
- [ ] Update README.md with usage docs

### Week 4: Decision Point
- [ ] **Evaluate**: Is Phase 1 sufficient?
- [ ] **Measure**: How often do developers use cleanup scripts?
- [ ] **Decide**: Proceed to Phase 2 or stay at Phase 1?

### Month 2+ (IF Phase 2 Approved):
- [ ] Implement `commit-cleanup-reusable.yml`
- [ ] Add authorization checks
- [ ] Add dry-run support
- [ ] Test extensively in test repo
- [ ] Gradual rollout

---

## Success Metrics

### Phase 1 Success Criteria
- ✅ Workflow runs without errors on 10+ PRs
- ✅ Developers report finding analysis helpful
- ✅ At least 30% of flagged PRs get cleaned up (manually or via script)
- ✅ Zero false positives (clean commits flagged as needing cleanup)
- ✅ Zero blocking failures (workflow doesn't break CI)

### Phase 2 Decision Criteria (Only if considering)
- ✅ Developers request automation (not just accept it)
- ✅ Clear ROI: Time saved > maintenance burden
- ✅ High confidence in pattern detection (>95% accuracy)
- ✅ Doctor Hubert approval after Phase 1 evaluation

---

## Risk Assessment

### Phase 1 Risks: ✅ VERY LOW
- ❌ **Data Loss**: Not possible (read-only)
- ❌ **Security**: Not possible (no write permissions)
- ⚠️ **False Positives**: Possible, but low impact (just a comment)
- ⚠️ **Noise**: Could create too many comments (mitigate: score threshold)

### Phase 2 Risks: ⚠️ MEDIUM (if implemented)
- ⚠️ **Force-Push Conflicts**: Possible if developer pushes during cleanup
- ⚠️ **Authorization Bypass**: Mitigated by PR author check
- ⚠️ **Data Loss**: Mitigated by `--force-with-lease`
- ⚠️ **Audit Gaps**: Mitigated by comment trail + artifact archival

---

## Rollback Plan

### Phase 1 Rollback (if needed)
1. Remove workflow from consuming repo CI
2. Delete or disable `commit-quality-check-reusable.yml`
3. No data impact (read-only)

### Phase 2 Rollback (if implemented and needed)
1. Remove comment trigger from `pr-commands.yml`
2. Disable `commit-cleanup-reusable.yml`
3. Provide manual recovery instructions for affected PRs
4. Restore from git reflog if needed

---

## Open Questions for Doctor Hubert

1. **Phase 1 Approval**: Proceed with validation-only workflow?
2. **Detection Patterns**: Are these fixup patterns correct?
   - `fixup`, `fix:`, `wip:`, `tmp:`, `oops`, `typo`
   - `ci `, `lint `, `pre-commit`
3. **Fail-on-Fixups**: Should workflow optionally block PRs with fixups?
4. **Phase 2 Timing**: When to evaluate if Phase 2 needed? (After 2 weeks? 1 month?)
5. **Cleanup Threshold**: Only suggest cleanup if benefit score is MEDIUM or HIGH?

---

## Files to Create

### Phase 1
```
.github/.github/workflows/commit-quality-check-reusable.yml
```

### Phase 1 Updates (Test & Consuming Repos)
```
github-workflow-test/.github/workflows/ci.yml (test first)
vm-infra/.github/workflows/ci.yml             (add commit-quality job)
dotfiles/.github/workflows/ci.yml             (add commit-quality job)
project-templates/.github/workflows/ci.yml    (add commit-quality job)
```

### Phase 1 Documentation
```
.github/README.md (add Commit Quality Check section)
.github/CLAUDE.md (reference in Section 1, Development Phase)
```

### Phase 2 (If Approved)
```
.github/.github/workflows/commit-cleanup-reusable.yml
vm-infra/.github/workflows/pr-commands.yml
dotfiles/.github/workflows/pr-commands.yml
project-templates/.github/workflows/pr-commands.yml
```

---

## Conclusion

**Recommended Approach**: Start with Phase 1 (validation-only).

**Rationale**:
1. ✅ Zero risk (read-only)
2. ✅ Provides immediate value (awareness + guidance)
3. ✅ Maintains developer control
4. ✅ Validates need before automating
5. ✅ Easy to implement and test
6. ✅ Aligns with "low time-preference" philosophy

**Decision Point**: After 2-4 weeks of Phase 1, evaluate whether Phase 2 automation is needed.

**Expected Outcome**: Phase 1 will likely be sufficient. Most developers will:
- Use provided cleanup scripts manually
- Or use GitHub's "Squash and merge" button
- Automation (Phase 2) may not be needed at all

**Next Step**: Doctor Hubert approval to proceed with Phase 1 implementation.

---

**Document Status**: Draft - Awaiting Approval
**Created**: 2025-10-10
**Agent Validation**: ✅ Security (approved), ✅ DevOps (prefers Phase 1), ✅ Architecture (4.5/5)
