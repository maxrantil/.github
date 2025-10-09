# ABOUTME: GitHub issue template with TDD checklist and concrete example
# GitHub Issue Template

Use this template for all GitHub issues to ensure TDD compliance and clear scope.

## Quick Start

**For gh CLI (recommended):**
```bash
gh issue create --title "Your feature/fix title" --body "$(cat <<'EOF'
## Summary
[Brief description - what needs to be built/fixed]

## Problem Statement
[Why needed? What problem does it solve?]

## Acceptance Criteria
- [ ] [Specific, testable requirement 1]
- [ ] [Specific, testable requirement 2]
- [ ] [Specific, testable requirement 3]

## TDD Checklist (MANDATORY)
- [ ] RED Phase: Tests written first and confirmed to fail
- [ ] GREEN Phase: Minimal code to make tests pass
- [ ] REFACTOR Phase: Code improved while tests stay green
- [ ] Unit Tests: Individual functions tested
- [ ] Integration Tests: Component interactions tested
- [ ] End-to-End Tests: Complete workflows tested

## Implementation Plan
[Break down into TDD cycles if complex]

## Dependencies
[Prerequisites before starting this work]
EOF
)"
```

## Full Template Structure

```markdown
## Summary

[Brief description of what needs to be built/fixed]

## Problem Statement

[Why is this needed? What problem does it solve?]

## Acceptance Criteria

- [ ] [Specific, testable requirement 1]
- [ ] [Specific, testable requirement 2]
- [ ] [Specific, testable requirement 3]

## TDD Checklist (MANDATORY)

- [ ] **RED Phase**: Tests written first and confirmed to fail for the right reason
- [ ] **GREEN Phase**: Minimal code written to make tests pass
- [ ] **REFACTOR Phase**: Code improved while keeping all tests green
- [ ] **Unit Tests**: Individual functions/components tested in isolation
- [ ] **Integration Tests**: Component interactions tested
- [ ] **End-to-End Tests**: Complete workflows tested
- [ ] **Test Quality**: All tests pass and output is pristine
- [ ] **No Production Code**: No implementation written without failing test first

## Implementation Plan

[Break down into TDD cycles if complex]

### Cycle 1: [Feature Name]

1. Write failing test for [specific behavior]
2. Implement minimal code to pass
3. Refactor while keeping tests green

### Cycle 2: [Next Feature]

1. Write failing test for [specific behavior]
2. Implement minimal code to pass
3. Refactor while keeping tests green

## Dependencies

[What needs to be complete before starting this work?]

## Definition of Done

- [ ] All acceptance criteria met
- [ ] All TDD checklist items completed
- [ ] Implementation plan phase updated (if applicable)
- [ ] Code reviewed and merged
```

## Concrete Example

**Issue Title**: "Implement Phase 4: Performance Testing Engine"

```markdown
## Summary

Implement the Performance Testing Engine (Phase 4) of the VPN porting implementation plan.

## Problem Statement

Current server selection is random. Need intelligent selection based on performance metrics to avoid slow servers and improve connection quality.

## Scope

This phase includes:
- Network connectivity testing (ping, DNS resolution)
- Server performance testing with multi-server latency testing
- Intelligent server selection based on performance scoring

## Acceptance Criteria

- [ ] Network connectivity validation works reliably
- [ ] `best-vpn-profile` can test 8 profiles and rank by performance
- [ ] Smart connection logic selects optimal servers (score < 20)
- [ ] Bad servers are avoided (score > 200)
- [ ] Connection failure limits enforced (max 2 consecutive)

## TDD Checklist (MANDATORY)

- [ ] RED Phase: Write failing tests for latency measurement
- [ ] GREEN Phase: Implement basic latency testing
- [ ] REFACTOR Phase: Optimize measurement accuracy
- [ ] Unit Tests: Test ping/DNS functions independently
- [ ] Integration Tests: Test full scoring algorithm
- [ ] End-to-End Tests: Test server selection with mock servers

## Implementation Plan

Following VPN_PORTING_IMPLEMENTATION_PLAN.md Phase 4 tasks:

### 4.1 Network Connectivity Testing
1. Write test: ping returns valid latency
2. Implement: basic ping function
3. Refactor: add error handling

### 4.2 Server Performance Testing
1. Write test: score calculation accurate
2. Implement: scoring algorithm
3. Refactor: optimize for multiple servers

### 4.3 Intelligent Server Selection
1. Write test: selects best server from pool
2. Implement: selection logic
3. Refactor: handle edge cases

## Dependencies

- Requires Phase 3 (Connection Management) to be complete ✅
- Must integrate with existing cache system
- Needs server list from ProtonVPN API

## Labels

- enhancement
- phase-4
- performance-testing

## Definition of Done

- [ ] All acceptance criteria met
- [ ] All TDD checklist items completed
- [ ] Phase 4 marked complete in implementation plan
- [ ] Tests pass in CI
- [ ] Code reviewed and merged
```

## Usage Notes

1. **Every issue MUST include the TDD checklist**
2. **Check off items as you complete them during development**
3. **Never mark an issue complete until all checklist items are done**
4. **Use issue comments to document your RED-GREEN-REFACTOR cycles**
5. **Reference specific test names and results in comments**

## Writing Style

Follow `WRITING_STYLE.md` guidelines:
- Be concise (not AI-verbose)
- Use natural language (contractions, informal)
- Skip corporate speak
- No emoji bonanza
- Get to the point

## Related Templates

- **PRD**: For product requirements → `PRD-template.md`
- **PDR**: For technical design → `PDR-template.md`
- **PR**: For pull requests → `github-pr-template.md`
