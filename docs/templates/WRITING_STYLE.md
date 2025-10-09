# ABOUTME: Writing style guidelines to avoid AI-generated appearance

# Writing Style Guidelines

**Purpose**: Write naturally, avoid AI-generated patterns that trigger detection or sound inauthentic.

## 🚫 Things to AVOID (AI Red Flags)

### Tone
- ❌ **Too polite** - "I would be happy to assist you with this matter"
- ✅ **Natural** - "Here's what I found"

### Grammar
- ❌ **Perfect English without typos** - Unnatural perfection
- ✅ **Human imperfect** - Occasional typos, informal contractions (don't, can't, it's)

### Formatting
- ❌ **Mixed Case To Make It Seem Serious** - Looks corporate/formal
- ✅ **Sentence case or lowercase** - "This is the title" or "this is casual"

### Punctuation
- ❌ **Using mdash (—)** - AI loves em-dashes
- ✅ **Use simple dash (-)** or just use periods

### Emojis
- ❌ **Emoji happy overuse** - 🎉✨🚀💡 every sentence
- ✅ **Sparse or none** - Only when actually meaningful

### Length
- ❌ **Too long at first shot** - Exhaustive explanations upfront
- ✅ **Concise first, details on request** - Get to the point

### Structure
- ❌ **Bullet point bonanza** - Everything in nested bullets
- ✅ **Mix paragraphs and bullets** - Use bullets when actually needed

## ✅ Natural Writing Patterns

### Be Direct
```
❌ AI: "I would be delighted to provide you with comprehensive assistance regarding..."
✅ Human: "Here's what you need to know:"
```

### Use Contractions
```
❌ AI: "I do not believe that will work"
✅ Human: "That won't work"
```

### Vary Sentence Length
```
❌ AI: "The implementation requires careful consideration. The approach should be systematic. The testing must be thorough."
✅ Human: "Implementation needs care. Be systematic, test thoroughly, and you'll be fine."
```

### Show Personality
```
❌ AI: "This solution presents several advantages which merit consideration."
✅ Human: "This works well. Here's why."
```

### Embrace Imperfection
```
❌ AI: "The aforementioned configuration necessitates modification."
✅ Human: "Fix the config - it's broken."
```

## Examples

### Issue Title
```markdown
❌ AI Style:
"Comprehensive Implementation of Advanced Performance Testing Engine with Intelligent Server Selection Capabilities"

✅ Human Style:
"Add performance testing for server selection"
```

### Commit Message
```markdown
❌ AI Style:
"feat: Implement comprehensive performance testing engine with intelligent server selection algorithms and network connectivity validation mechanisms"

✅ Human Style:
"feat: add performance tests for server selection"
```

### PR Description
```markdown
❌ AI Style:
"## Summary
I am pleased to present this comprehensive pull request which implements a robust solution for the performance testing requirements. The implementation includes extensive testing coverage and follows best practices throughout.

## Changes Implemented
- ✨ Added comprehensive performance testing engine
- 🚀 Implemented intelligent server selection algorithms
- 💡 Created network connectivity validation
- ✅ Ensured complete test coverage
..."

✅ Human Style:
"## What Changed
Added performance testing for server selection. Tests ping/latency, picks fastest server.

## Why
Need to avoid slow servers. Current random selection sucks.

## Testing
- Unit tests for latency measurement
- Integration test with mock servers
- Manual test with real ProtonVPN servers

Works fine, ready to merge.
"
```

### Documentation
```markdown
❌ AI Style:
"This comprehensive guide will walk you through the entire process of configuring your development environment. Please follow each step carefully to ensure optimal results."

✅ Human Style:
"Quick setup:

1. Clone repo
2. Run install.sh
3. You're done

Breaks? Check logs in ~/.cache/whatever"
```

## When Writing Issues/PRs

**DO:**
- Get to the point fast
- Use normal words (not "utilize", "leverage", "facilitate")
- Show some personality
- Be okay with incomplete sentences
- Mix formal and casual (you're a developer, not a lawyer)

**DON'T:**
- Write like you're being graded
- Over-explain obvious things
- Use corporate speak
- Apologize excessively
- Add emojis everywhere

## Reference Density

```markdown
❌ Too Dense (AI):
According to the specifications outlined in CLAUDE.md Section 3.2.1,
as detailed in the comprehensive implementation guide (see: docs/implementation/PHASE-4.md),
and cross-referenced with the architectural decisions documented in PDR-2024-09-15.md...

✅ Natural (Human):
Per CLAUDE.md, we need TDD. See docs/implementation/PHASE-4.md for details.
```

## TL;DR

**Write like you're explaining to a coworker in Slack**, not like you're writing a thesis or corporate memo.

- Short is good
- Typos happen
- Casual is fine
- Bullet points are tools, not requirements
- Emojis: use sparingly or not at all
- Be direct, skip the fluff
