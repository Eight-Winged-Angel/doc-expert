# Doc-Expert Skill

**Expert-Driven Enterprise Document Generation**

Doc-Expert is a Claude Code skill that generates enterprise-ready draft documents with minimal user effort. It's designed for users who lack domain knowledge or don't know how to structure documents.

---

## When to Use Doc-Expert

✅ **Use doc-expert when:**
- You don't know how to write a specific document type
- You lack domain expertise (QA, PM, BA, etc.)
- You need a structured draft quickly with minimal back-and-forth
- You want expert guidance without over-questioning
- You're starting from scratch (no previous document version)

❌ **Don't use doc-expert when:**
- You have a previous document version to update → Use `/doc-revision` instead
- You just need minor edits to existing content → Use regular editing
- You want to write it yourself → Just write directly

---

## Quick Start

### Basic Usage

```
/doc-expert
```

Then describe what you need. Example:

```
Write a test plan for Feature X
```

### Advanced Usage (QA-Senior Mode)

```
Act as senior QA and write a test plan for Feature X
```

or

```
Use QA mode to write a test plan for Feature X
```

---

## Operating Modes

### 1. Normal Mode (Default)

**Use for:** Any enterprise document (requirements, release plans, SOPs, architecture docs)

**What it does:**
- Asks 5 questions maximum
- Uses safe defaults for missing information
- Generates structured enterprise-ready document
- Marks uncertain areas as "To Be Confirmed"

**Example:**
```
User: "Write a release plan for Project Phoenix"
Claude: [Asks 5 questions about scope, timeline, stakeholders, risks, rollback]
Claude: [Generates release plan with checklist, timeline, and rollback procedures]
```

---

### 2. QA-Senior Mode

**Trigger phrases:**
- "Act as senior QA"
- "Use QA mode"
- "You are a senior QA"

**Use for:** QA test plans, test strategies, QA analysis

**What it does:**
- Thinks like a 10+ year QA consultant
- Prioritizes by risk (P0, P1, P2, P3)
- Includes negative testing and edge cases
- Considers regression impact
- Adds performance, integration, and security scenarios
- Provides coaching notes explaining QA thinking

**QA-Senior Reasoning:**
- ✅ Happy path covered
- ✅ Negative path covered
- ✅ Edge cases considered
- ✅ Integration impact analyzed
- ✅ Rollback scenario planned
- ✅ Data state reset addressed
- ✅ Regression scope defined

**Example:**
```
User: "Act as senior QA and write a test plan for login feature with 2FA"
Claude: [Asks 5 questions with QA focus]
Claude: [Generates test plan with:]
  - Risk-based prioritization
  - Positive tests (valid login with 2FA)
  - Negative tests (invalid credentials, expired 2FA, network timeout)
  - Edge cases (first login, simultaneous logins, token race conditions)
  - Performance SLA validation
  - Regression coverage
```

---

## Core Principles

### 1. Minimal Questioning (Max 5 Questions)
Doc-expert asks only essential information. It won't over-question you.

**Minimum Information Contract:**
1. What is being delivered? (feature/system/process)
2. What changed?
3. Who is affected? (users/system/internal)
4. Target release timeline?
5. Any known constraints or risks?

**If you don't know:** Just say "TBD" or "I don't know" - doc-expert will use safe defaults.

---

### 2. Safe Defaults
Doc-expert never blocks on missing information. It applies industry-standard defaults:

| Missing Info | Default Value |
|--------------|---------------|
| Document status | DRAFT |
| Version | 1.0 |
| Test environment | Staging (prod-like) |
| Risk level | Medium |
| Approvers | [Leave blank] |
| Entry criteria | Build stable, major defects closed |
| Exit criteria | No critical defects, acceptable minor defects |

---

### 3. No Metadata Questions
Doc-expert **never** asks about:
- Document status (always DRAFT)
- Approvers (leaves blank)
- Review cycle
- Version number (defaults to 1.0)
- Document owner

These are auto-filled with safe defaults.

---

## Document Types Recognized

Doc-expert automatically recognizes common enterprise document types:

| Type | Keywords | Structure |
|------|----------|-----------|
| **Test Plan** | "test", "QA", "testing", "quality" | Scope, Approach, Test Cases, Entry/Exit Criteria |
| **Requirements** | "requirements", "BRD", "PRD", "functional" | Overview, Requirements Matrix, Acceptance Criteria |
| **Release Plan** | "release", "deployment", "go-live" | Timeline, Checklist, Rollback Plan |
| **Architecture** | "architecture", "design", "technical" | System Overview, Components, Data Flow |
| **SOP** | "procedure", "process", "how-to" | Purpose, Steps, Roles, Exceptions |

If type is ambiguous, doc-expert asks once. If still unclear, defaults to generic structured document.

---

## Step-by-Step Workflow

### Example Session: Test Plan for Chatbot Feature

**Step 1: User Triggers Skill**
```
User: "Act as senior QA and write a test plan for Lumi Virtual Connect chatbot"
```

**Step 2: Doc-Expert Asks 5 Questions**
```
Claude: "Before I generate your test plan, I need 5 clarifications:"

1. Is this a new feature or enhancement?
2. What's the target release date?
3. What platform does this integrate with?
4. How many concurrent users expected?
5. Any performance requirements?
```

**Step 3: User Provides Answers**
```
User:
1. New feature
2. Feb 14-27
3. TBD
4. 5 agents
5. 3-second response time
```

**Step 4: Doc-Expert Generates Document**
```
Claude: [Generates complete test plan with:]
- Document metadata (DRAFT, v1.0, dates)
- Executive summary
- Scope (in/out)
- Risk-based test approach (P0/P1/P2/P3)
- 14 test cases (happy path, negative, edge cases)
- Entry/exit criteria
- Assumptions
- Risks & mitigation
- Test schedule
- Open questions
```

**Step 5: Optional - Confluence Export**
```
User: "Give me a Confluence-ready version"
Claude: [Creates .md file with Confluence markup]
```

---

## Output Format

All doc-expert documents include:

✅ **Clear headings** (H2/H3 hierarchy)
✅ **Structured sections** (logical flow)
✅ **Enterprise tone** (professional, concise)
✅ **Confluence-ready formatting** (tables, lists, headings)
✅ **Explicit "Assumptions" section**
✅ **Explicit "Risks" section**
✅ **Explicit "Open Questions" section** (if any)
✅ **DRAFT status** prominently displayed

---

## Real-World Examples

### Example 1: Test Plan (QA-Senior Mode)

**Input:**
```
Act as senior QA. Write a test plan for Lumi Virtual Connect chatbot with:
- Real-time agent support
- AI suggestions for responses
- KB article recommendations
- 3-second SLA
```

**Output:**
- 14 test cases (4 P0 critical, 3 P1 high, 5 P2 medium, 2 P3 low)
- Risk-based prioritization
- Negative testing (invalid queries, network latency, session timeouts)
- Performance validation (5 concurrent agents, 3-second SLA)
- Entry/exit criteria with clear gates
- 2-week test schedule

**See:** `doc-expert-session-transcript-lumi-test-plan.md` for full example

---

### Example 2: Requirements Document (Normal Mode)

**Input:**
```
Write a requirements document for password reset feature
```

**Questions Asked:**
1. Current system behavior?
2. Target users (internal/external)?
3. Security requirements?
4. Timeline?
5. Known constraints?

**Output:**
- Functional requirements matrix
- Non-functional requirements (security, performance)
- User stories with acceptance criteria
- Edge cases and error handling
- Dependencies and assumptions

---

### Example 3: Release Plan

**Input:**
```
Write a release plan for v2.0 of our mobile app
```

**Questions Asked:**
1. What's new in v2.0?
2. Release date?
3. Platform (iOS/Android/both)?
4. Phased rollout or all-at-once?
5. Rollback triggers?

**Output:**
- Release timeline (pre-release, release, post-release)
- Deployment checklist (30+ items)
- Rollback procedures
- Communication plan
- Success metrics

---

## Coaching Features (QA-Senior Mode)

When using QA-Senior Mode, doc-expert includes coaching notes to help you learn:

**Example Coaching Notes:**
```
> **Coaching Note:** As a senior QA, I organize testing by risk level.
  High-risk items get tested first and most thoroughly.

> **Coaching Note:** These MUST pass for release. If any fail, nothing
  else matters.

> **Coaching Note:** Senior QAs always test how systems FAIL, not just
  how they succeed.

> **Coaching Note:** Don't start testing until these are met - otherwise
  you'll waste time on incomplete builds.
```

Plus a **Coaching Summary** section at the end explaining:
- Key QA principles applied
- What to do next
- Questions to ask your team
- Common pitfalls to avoid

---

## Tips for Best Results

### 1. Provide Context Upfront
**Good:**
```
Write a test plan for Lumi chatbot that provides real-time agent support
with AI suggestions. Expected benefits: faster response times, reduced
cognitive load, better compliance.
```

**Less Effective:**
```
Write a test plan
```

---

### 2. Use Trigger Phrases for Specialized Modes

**For QA documents:**
```
"Act as senior QA"
"Use QA mode"
```

**For other domains (future modes):**
```
"Act as senior BA"
"Use architecture mode"
```

---

### 3. Answer "TBD" When You Don't Know

**Example:**
```
Claude: "What platform does this integrate with?"
User: "TBD"
Claude: [Uses safe default and marks as risk]
```

Don't block on unknowns - doc-expert handles them gracefully.

---

### 4. Request Confluence Export

After generation:
```
User: "Give me a Confluence-ready version"
```

Doc-expert creates a `.md` file with Confluence markup for easy copy-paste.

---

## Comparison: Doc-Expert vs Doc-Revision

| Aspect | Doc-Expert | Doc-Revision |
|--------|-----------|--------------|
| **User Knowledge** | User doesn't know how to write | User has previous version |
| **Input** | Minimal info (5 questions) | Previous document (PDF/Word) |
| **Approach** | Expert-driven generation | Template-driven update |
| **Questions** | 5 questions max per turn | 3-5 questions per turn |
| **Metadata** | Auto-fills all metadata | Inherits/auto-increments |
| **Output** | Draft from scratch | Updated version |
| **Special Modes** | QA-Senior Mode | Document Type Profiles |

**Use doc-expert when:** User needs help writing from scratch
**Use doc-revision when:** User has a previous version to update

---

## Integration with Other Skills

Doc-expert can integrate with:

- **content-research-writer** - For section-by-section refinement
- **pdf** skill - For parsing reference documents
- **docx** skill - For Word export
- **markdown-tools** - For document conversion

---

## File Outputs

When you request a saved version, doc-expert creates:

```
<project-name>_<doc-type>_v<version>.md
```

**Example:**
```
Lumi_Virtual_Connect_Test_Plan_v1.0.md
```

Located in your current working directory.

---

## Troubleshooting

### "Doc-expert is asking too many questions"
**Issue:** More than 5 questions asked
**Fix:** This shouldn't happen. If it does, remind doc-expert: "Keep it to 5 questions max"

### "I don't understand the technical terms"
**Issue:** Document too complex
**Fix:** Use QA-Senior Mode with coaching notes enabled, or ask: "Explain this in simpler terms"

### "The document is missing critical sections"
**Issue:** Important content omitted
**Fix:** Tell doc-expert: "Add section on [X]" and it will regenerate

### "I need to customize the output format"
**Issue:** Confluence format not suitable
**Fix:** Request: "Export as Word document" (uses docx skill) or "Export as PDF" (uses pdf skill)

---

## Version History

**Current Version:** v1.0

**v1.0 (2026-02-13):**
- Initial release
- Normal mode + QA-Senior mode
- Minimal questioning strategy (max 5 questions)
- Safe defaults for all metadata
- QA quality checks and reasoning patterns
- Coaching features for learning

---

## Contributing

To improve doc-expert:

1. **Add new document types** - Edit SKILL.md document type recognition table
2. **Add new specialized modes** - Follow QA-Senior Mode pattern
3. **Improve defaults** - Update safe defaults based on enterprise standards
4. **Add coaching features** - Enhance teaching/learning aspects

---

## Support & Examples

**Full Session Example:**
- See `doc-expert-session-transcript-lumi-test-plan.md` for complete workflow

**Test Document Generated:**
- See `Lumi_Virtual_Connect_Test_Plan_v1.0.md` for output example

**Skill Definition:**
- See `.claude/skills/doc-expert/SKILL.md` for complete skill specification

---

## Quick Reference Card

```
┌─────────────────────────────────────────────────────────┐
│ DOC-EXPERT SKILL QUICK REFERENCE                        │
├─────────────────────────────────────────────────────────┤
│ BASIC USAGE:                                            │
│   /doc-expert                                           │
│   "Write a [document type] for [feature/system]"       │
│                                                         │
│ QA-SENIOR MODE:                                         │
│   "Act as senior QA and write a test plan for X"       │
│                                                         │
│ MAX QUESTIONS: 5                                        │
│                                                         │
│ SAFE DEFAULTS:                                          │
│   - Status: DRAFT                                       │
│   - Version: 1.0                                        │
│   - Risk: Medium                                        │
│                                                         │
│ OUTPUTS:                                                │
│   - Structured enterprise document                      │
│   - Confluence-ready markdown                           │
│   - Coaching notes (QA mode)                            │
│                                                         │
│ DON'T USE WHEN:                                         │
│   - You have a previous version → use /doc-revision     │
│   - Just need minor edits → edit directly               │
└─────────────────────────────────────────────────────────┘
```

---

**Ready to generate enterprise documents with minimal effort? Try `/doc-expert` now!** 🚀
