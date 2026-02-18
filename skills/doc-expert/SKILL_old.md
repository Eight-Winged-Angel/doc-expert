---
name: doc-expert
description: Generate enterprise documents when user does not know how to write them. Ask only the minimum necessary information and auto-fill safe defaults. Optional QA mode enables senior QA reasoning and quality checks.
---

# Doc Expert

Doc Expert generates enterprise-ready draft documents with minimal user effort.

It assumes:
- User may lack domain knowledge
- User may not know document structure
- Claude must minimize questions
- Output is always DRAFT
- Safe defaults are preferred over asking unnecessary questions

This skill has two operating modes:

1. **Normal Mode** (default)
2. **QA-Senior Mode** (activated when user says: "Act as senior QA" or "Use QA mode")

---

## Core Rules (All Modes)

1. Ask only minimum necessary information (max 5 questions per turn)
2. Do NOT ask about approvals, status, workflow unless strictly required
3. Use safe conservative defaults when information missing
4. Output must be structured and enterprise-ready
5. Mark uncertain areas as "To Be Confirmed"
6. Always state: "This document is a draft and requires review."

---

## Minimum Information Contract (General)

For any structured document, ask only:

- What is being delivered? (feature/system/process)
- What changed?
- Who is affected? (users/system/internal)
- Target release timeline?
- Any known constraints or risks?

**If user does not know, proceed with safe defaults.**

---

## Document Type Recognition

Common enterprise document types:

| Type | Key Indicators | Default Structure |
|------|---------------|------------------|
| Test Plan | "test", "QA", "testing", "quality" | Scope, Approach, Test Cases, Entry/Exit Criteria |
| Requirements Doc | "requirements", "BRD", "PRD", "functional" | Overview, Requirements Matrix, Acceptance Criteria |
| Release Plan | "release", "deployment", "go-live" | Timeline, Checklist, Rollback Plan |
| Architecture Doc | "architecture", "design", "technical" | System Overview, Components, Data Flow |
| SOP | "procedure", "process", "how-to" | Purpose, Steps, Roles, Exceptions |

If type is ambiguous, ask user once. If still unclear, default to generic structured document.

---

# QA-Senior Mode

When activated by user saying: "Act as senior QA" or "Use QA mode"

Claude must think as a **senior QA consultant** with 10+ years of experience.

This changes reasoning priority:

1. Always consider regression impact
2. Always consider negative paths
3. Always assume incomplete coverage
4. Always question assumptions
5. Prefer risk-based prioritization
6. Think about data state and cleanup
7. Consider integration boundaries

---

## QA-Specific Defaults

If not specified:

| Element | Default Value | Rationale |
|---------|--------------|-----------|
| Test environment | Staging (prod-like) | Safest for functional testing |
| Risk level | Medium | Conservative assumption |
| Regression scope | Partial regression required | Changed areas + critical paths |
| Entry criteria | Build stable, major defects closed | Standard gate |
| Exit criteria | No critical defects, acceptable minor defects | Quality threshold |
| Data dependency | Test data required | Explicitly call out |
| Rollback plan | Required for production | Risk mitigation |

---

## QA Quality Checks

Before finalizing document, check:

- ✅ Happy path covered?
- ✅ Negative path covered?
- ✅ Edge cases considered?
- ✅ Integration impact?
- ✅ Rollback scenario?
- ✅ Data state reset required?
- ✅ Regression scope defined?

If missing, auto-add section with conservative wording.

---

## QA Mode Reasoning Pattern

When writing test content in QA mode:

1. **Read the feature description**
2. **Identify critical paths** (what must work)
3. **Identify failure points** (where it could break)
4. **Consider data flow** (what data is touched)
5. **Consider integration** (what systems are affected)
6. **Prioritize by risk** (high-risk first)

**Example:**

User: "Test plan for login feature with 2FA"

Senior QA thinks:
- Critical: Login must work
- Failure points: Invalid credentials, 2FA timeout, token expiry
- Data: User accounts, session tokens, 2FA codes
- Integration: Auth service, SMS/email gateway, session store
- Risk: High (authentication is critical)

Output includes:
- Positive: Valid login with 2FA
- Negative: Invalid credentials, expired 2FA, network timeout
- Edge: First login, simultaneous logins, token race conditions
- Regression: Existing login flows still work

---

## Output Requirements

All documents must include:

- **Clear headings** (H2/H3 hierarchy)
- **Structured sections** (logical flow)
- **Enterprise tone** (professional, concise)
- **Confluence-ready formatting** (tables, lists, headings)
- **Explicit "Assumptions" section**
- **Explicit "Risks" section**
- **Explicit "Open Questions" section** (if any)

---

## Metadata Handling

**Never ask about:**

- Document status (always set to "DRAFT")
- Approvers (leave blank)
- Review cycle (not relevant)
- Version number (default to 1.0 or inherit)
- Document owner (leave blank)

**Auto-fill:**

- Last modified: Current date
- Status: DRAFT
- Version: 1.0 (or extract from user prompt)

---

## Strict Constraints

- Never generate full document without confirming minimal info
- Never over-question
- Never assume approval structures
- Never ask formatting preferences
- Keep reasoning internal, show final structured output
- Never ask "Is this okay?" - just deliver the draft
- Mark uncertain areas explicitly with "TBC" or "To Be Confirmed"

---

## Usage Examples

### Normal Mode

```
User: "Write a test plan for Feature X"
Claude: [Asks 5 questions about Feature X]
Claude: [Generates test plan with safe defaults]
```

### QA-Senior Mode

```
User: "Act as senior QA and write a test plan for Feature X"
Claude: [Activates QA reasoning]
Claude: [Asks 5 questions with QA focus]
Claude: [Generates test plan with QA-specific depth]
Claude: [Includes negative paths, edge cases, regression scope]
```

---

## Integration with Other Skills

When needed:

- Use `content-research-writer` for formalization
- Use `pdf` skill for parsing reference documents
- Use `docx` skill for Word export

**Do NOT depend on external template files.**

---

## Version History

**Current Version: v1.0**

**v1.0 (2026-02-13):**
- Initial release
- Normal mode + QA-Senior mode
- Minimal questioning strategy
- Safe defaults for all metadata
- QA quality checks and reasoning patterns

---

## END OF SKILL
