# Rollout: Commit Quality Check Workflow to Consuming Repositories

**Status**: Phase 1 Complete - Ready for Rollout
**PDR**: PDR-commit-cleanup-workflow-2025-10-10.md
**Priority**: Medium

## Summary

Roll out the new `commit-quality-check-reusable.yml` workflow to all consuming repositories:
- vm-infra
- dotfiles
- project-templates
- protonvpn-manager (future)

## Phase 1 Implementation (Complete ✅)

- ✅ Created `commit-quality-check-reusable.yml` reusable workflow
- ✅ Added to .github repository's own PR validation (dogfooding)
- ✅ Updated README.md with workflow documentation
- ✅ Updated CLAUDE.md with commit cleanup guidance
- ✅ Created PDR document

## Phase 2: Rollout to Consuming Repos

### Week 1: Test in github-workflow-test Repository

- [ ] Add `commit-quality` job to `~/workspace/github-workflow-test/.github/workflows/ci.yml`
- [ ] Create test PR with intentional fixup commits:
  - Initial commit
  - "fixup: oops forgot file" commit
  - "ci fix" commit
  - "lint" commit
- [ ] Verify workflow runs and posts comment
- [ ] Verify cleanup script works correctly
- [ ] Test all three cleanup options (script, manual, squash)
- [ ] Verify cleanup benefit scores (LOW/MEDIUM/HIGH)
- [ ] Verify threshold filtering works
- [ ] Gather feedback from Doctor Hubert

### Week 2: Pilot in Real Repo (vm-infra)

- [ ] Add `commit-quality` job to `vm-infra/.github/workflows/ci.yml`
- [ ] Use on real PRs
- [ ] Verify no false positives
- [ ] Confirm patterns detect actual fixups

### Week 3: Expand (dotfiles, project-templates)

- [ ] Add `commit-quality` job to `dotfiles/.github/workflows/ci.yml`
- [ ] Add `commit-quality` job to `project-templates/.github/workflows/ci.yml`
- [ ] Update project-templates documentation
- [ ] Verify templates include commit-quality workflow

### Week 4: Evaluation

- [ ] **Decision Point**: Is Phase 1 sufficient?
- [ ] Measure: How often do developers use cleanup scripts?
- [ ] Decide: Stay at Phase 1 or implement Phase 2 (automation)?

## Success Criteria

- ✅ Workflow runs without errors on 10+ PRs across repos
- ✅ Developers report finding analysis helpful
- ✅ At least 30% of flagged PRs get cleaned up
- ✅ Zero false positives (clean commits flagged incorrectly)
- ✅ Zero blocking failures (workflow doesn't break CI)

## Implementation Code

```yaml
# Add to each repo's .github/workflows/ci.yml (or pr-validation.yml)

commit-quality:
  name: Commit Quality Check
  uses: maxrantil/.github/.github/workflows/commit-quality-check-reusable.yml@main
  with:
    base-branch: 'master'
    fail-on-fixups: false
    suggest-cleanup: true
    cleanup-score-threshold: 'MEDIUM'
```

## Phase 2 (Optional - Future)

**Only if Phase 1 proves insufficient:**

- Create `commit-cleanup-reusable.yml` with `/cleanup-commits` command trigger
- Add authorization checks and safety guards
- Test extensively before rollout
- Requires Doctor Hubert approval after Phase 1 evaluation

## Notes

- Phase 1 is read-only (no force-push, no modifications)
- Developers maintain full control
- Three options provided: script, manual rebase, or squash-on-merge
- Low time-preference approach: validate need before automating

## Related Files

- `.github/workflows/commit-quality-check-reusable.yml` - Reusable workflow
- `.github/workflows/pr-validation.yml` - .github repo's own CI (dogfooding)
- `README.md` - Workflow documentation
- `CLAUDE.md` - Updated development guidelines
- `PDR-commit-cleanup-workflow-2025-10-10.md` - Complete design document
