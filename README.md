# Doc-Expert Skill

**Input-Driven Enterprise Document Generation**

Doc-Expert is a Claude Code skill that generates enterprise-ready draft documents by combining a document template, source materials, and an optional set of enterprise guidelines with a controlled section-by-section interview. Designed for PM, BA, and QA Lead roles who need structured first drafts with minimal cognitive load.

---

## When to Use Doc-Expert

**Use doc-expert when:**
- You have a document template (with section guidelines) and source materials (PRD, BRD, feature specs)
- You want the skill to extract structure from the template and populate it from your inputs
- You need a structured first draft with minimal back-and-forth
- You're starting from scratch (no previous document version)

**Don't use doc-expert when:**
- You have a previous document version to update → Use `/doc-revision` instead
- You just need minor edits to existing content → Use regular editing
- You want to write it yourself → Just write directly

---

## Quick Start

### Basic Usage

```
/doc-expert
```

Then provide your inputs using this format:

```
Document type: [e.g., Test Strategy and Plan]
Project/Feature name: [name]
Scope summary: [1–3 sentences on what is being delivered]
Known constraints or risks: [optional]
Special instructions: [optional]

Attached files:
- [Template]: <filename>
- [Requirements]: <filename>
- [Guidelines]: <filename> (optional)
```

---

## Input Model

### Required Inputs

| Input | Role | Example |
|-------|------|---------|
| **Document Template** | Defines output structure, section order, and per-section guidance | Test Strategy and Plan Template |
| **Source Document(s)** | Provides domain content to populate sections | PRD, BRD, feature spec, user stories |

### Optional Inputs

| Input | Role | Example |
|-------|------|---------|
| **Enterprise Guidelines** | Org-specific constraints, standards, or checklists | Test Guidelines, UAT Operating Guidelines, Definition of Done |
| **User Prompt** | Scoping instructions, known constraints, emphasis | Focus on integration testing; UAT is critical |

If you provide only a brief prompt with no files, the skill proceeds with a question-driven approach and will warn that output will rely heavily on your answers.

---

## High-Level Workflow

Five phases:

```
Phase 1: Input Analysis
  Parse all inputs, build internal context model

Phase 2: Template Structure Extraction
  Extract sections, per-section guidelines, content shapes

Phase 3: Build Plan & Confirmation
  Present section tree with coverage assessment, confirm with user

Phase 4: Section-by-Section Construction
  Controlled interview per section, draft, confirm, proceed

Phase 5: Assembly & Output
  Combine, quality check, deliver Confluence-ready Markdown
```

---

## Step-by-Step Workflow

### Example Session: Test Strategy and Plan

**Step 1: User Triggers Skill**
```
User: "Generate a test strategy and plan for Project Lumi"

Attached:
- [Template]: Test_Strategy_Plan_Template.docx
- [Requirements]: Lumi_Virtual_Connect_PRD.pdf
- [Guidelines]: Enterprise_Test_Guidelines.pdf
```

**Step 2: Doc-Expert Analyzes Inputs**
```
Claude: [Parses template → extracts section tree]
Claude: [Parses PRD → builds context model]
Claude: [Parses guidelines → registers constraints]
```

**Step 3: Build Plan Presented**
```
Claude: "Here is the section structure I extracted from your template:"

1. Introduction ✅ Strong coverage from PRD
2. Scope ✅ Strong coverage from PRD
3. Test Approach ⚠️ Partial — will need 2 questions
4. Test Cases ❓ No coverage — will need user input
5. Entry / Exit Criteria ⚠️ Partial — will use defaults + confirm
6. Assumptions & Risks ✅ Extracted from PRD risks section

"Does this structure look correct? Any sections to add, remove, or skip?"
```

**Step 4: Section-by-Section Construction**
```
Claude: "Working on Section 3 — Test Approach"
[Shows what was extracted] [Shows template guideline]
[Presents draft with gaps marked [TBC]]
[Asks 1–3 targeted questions about gaps only]
User: [Answers or says "use defaults"]
Claude: [Finalizes section, moves to next]
```

**Step 5: Assembly & Delivery**
```
Claude: [Assembles full document]
Claude: [Quality check: all sections present, no orphan [TBC], guidelines applied]
Claude: [Delivers complete Confluence-ready Markdown]
Claude: "This document is a DRAFT and requires review before approval."
Claude: [Lists any [TBC] items needing resolution]
```

**Step 6: Optional — Word Export**
```
User: "Give me a Word version"
Claude: [Uses docx skill to export]
```

---

## Phase Details

### Phase 1 — Input Analysis

- PDFs parsed via `pdf` skill; Word files via `docx` skill; Markdown read directly
- Extracts from template: section tree, per-section guidance, expected content shape
- Extracts from source docs: features, requirements, user stories, acceptance criteria, system components, integrations, actors
- Extracts from enterprise guidelines: constraints, standards, mandatory inclusions
- Internally classifies coverage per section: **strong / partial / none** (not shown to user as a raw list — used to calibrate question density)

### Phase 2 — Template Structure Extraction

- Builds section hierarchy (1 / 1.1 / 1.1.1)
- Detects content shape per section: paragraph / table / list / hybrid
- Extracts per-section guidance text from the template itself

### Phase 3 — Build Plan & Confirmation

Presents to user:
1. Extracted section tree (numbered, with titles)
2. Coverage per section: ✅ Strong / ⚠️ Partial / ❓ None
3. Enterprise guidelines detected and how they will be applied

Asks at most **3 questions** in this phase:
- Does the section structure look correct?
- Any sections to skip or deprioritize?
- Any additional context before we begin?

### Phase 4 — Section-by-Section Construction

For each section:
- States which section is being worked
- Shows extracted content relevant to the section
- Shows the template's guideline for the section (brief summary)
- **Strong coverage** → presents draft, asks to confirm or adjust
- **Partial coverage** → presents draft with [TBC] gaps, asks targeted questions about gaps only
- **No coverage** → asks focused questions, then drafts

**Question rules:**
- **1–3 questions per turn** — aim for 1 where possible
- Never ask about metadata (status, approvers, version, dates)
- Never ask obvious questions answerable from the provided inputs
- Always prefer showing a draft and letting the user react
- If user says "just do it" / "use defaults" → switch to aggressive-default mode, draft all remaining sections with safe defaults, present for bulk review

**Large table sections (e.g., test cases, RACI):**
- Draft 3–5 rows at a time, present for review, iterate
- Never generate an entire large table in one shot

### Phase 5 — Assembly & Output

Quality check before delivery:
- All template sections present (or explicitly skipped)
- No orphan [TBC] markers without explanation
- Enterprise guideline requirements addressed
- Tables consistently formatted
- Heading hierarchy correct
- Cross-references between sections valid

Domain-specific checks:

| Document Type | Additional Checks |
|---------------|-------------------|
| Test Strategy/Plan | Happy path? Negative paths? Edge cases? Integration impact? Entry/exit criteria? Regression scope? |
| Requirements Doc | Acceptance criteria? Requirements uniquely identifiable? Traceability possible? |
| Release Plan | Rollback plan? Dependency list complete? |

---

## Output Format

All doc-expert documents are **Confluence-ready Markdown**:

- Proper heading hierarchy (## / ### / ####)
- Clean pipe-delimited tables
- Numbered and bulleted lists where appropriate
- Bold for emphasis per template conventions
- No raw formatting artifacts
- Copy-paste ready for Confluence
- **DRAFT status** prominently stated
- All uncertain content marked **[TBC — Default Applied]** with rationale

---

## Safe Defaults

Doc-expert never blocks on missing information:

| Missing Info | Default Value |
|--------------|---------------|
| Document status | DRAFT |
| Version | 1.0 |
| Test environment | Staging (prod-like) |
| Risk level | Medium |
| Approvers | [Leave blank] |
| Entry criteria | Build stable, major defects closed |
| Exit criteria | No critical defects, acceptable minor defects |

Defaults are marked **[TBC — Default Applied]** so reviewers can identify them.

---

## Metadata Handling

Doc-expert **never asks** about document metadata:

| Field | Strategy |
|-------|----------|
| Status | Always **"DRAFT"** |
| Version | **1.0** (or extracted from user prompt) |
| Author | Leave blank or extract from user prompt |
| Approvers / Reviewers | Leave blank |
| Last Modified | Current date |
| Next Review Date | Leave blank |

---

## Error Handling

| Situation | Response |
|-----------|----------|
| No template provided | Ask user to provide one, or offer to use a generic structure for the document type |
| No source documents | Warn that output will rely heavily on user interview; proceed with question-driven approach |
| Template has no section guidelines | Infer section purpose from title and position; proceed with best-effort |
| Source documents contradict each other | Flag contradiction, ask for resolution before drafting affected section |
| Enterprise guideline conflicts with template | Present conflict, ask user which takes precedence |
| User wants to skip all questions | Switch to full-default mode, generate complete draft, present for bulk review with all [TBC] gaps marked |

---

## Tips for Best Results

### 1. Provide a Template with Embedded Guidelines

The richer your template's per-section instructions, the less the skill needs to ask.

**Better template section header:**
```
## 3. Test Approach
Describe the overall testing strategy. Include: test levels (unit, integration, UAT),
test types (functional, performance, security), tooling, and responsibilities by role.
```

**Minimal template section header:**
```
## 3. Test Approach
```

Both work — but the first reduces questions significantly.

---

### 2. Provide Source Documents

The skill extracts content from your PRDs, BRDs, and specs. The more you provide, the fewer questions you'll be asked.

---

### 3. Say "Use Defaults" to Speed Up

If you're in a hurry:
```
User: "Just use defaults for everything"
```

Doc-expert switches to aggressive-default mode: drafts all remaining sections with safe defaults, marks gaps [TBC], and delivers for bulk review.

---

### 4. Request Word or PDF Export

After generation:
```
User: "Give me a Word version"   → uses docx skill
User: "Export as PDF"            → uses pdf skill
```

---

## Comparison: Doc-Expert vs Doc-Revision

| Aspect | Doc-Expert | Doc-Revision |
|--------|-----------|--------------|
| **User Knowledge** | Has template + source docs | Has previous version of the document |
| **Input** | Template + source docs + optional guidelines | Previous document (PDF/Word) |
| **Approach** | Input-driven, template-aware generation | Template-learned update from previous version |
| **Questions** | 1–3 per section turn; max 3 in plan phase | 3–5 questions per turn |
| **Metadata** | Auto-fills all metadata | Inherits/auto-increments from previous version |
| **Output** | First draft from inputs | Updated version |

**Use doc-expert when:** You have a template and source materials and need a first draft
**Use doc-revision when:** You have a previous version and want to update it

---

## Integration with Other Skills

| Skill | When Used |
|-------|-----------|
| `pdf` | Parsing PDF inputs (requirements, guidelines, templates) |
| `docx` | Parsing Word inputs; generating Word export if requested |

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
**Issue:** More questions than expected per section
**Fix:** Provide a richer template with embedded section guidelines, and more complete source documents. Or say "use defaults" to skip remaining questions.

### "The document is missing critical sections"
**Issue:** Important content omitted
**Fix:** Tell doc-expert: "Add section on [X]" and it will regenerate. Or check that your template includes the section — the skill only generates sections present in the template.

### "I don't have a template"
**Fix:** Tell doc-expert what document type you need. It will offer to use a generic structure for that type and proceed with a question-driven approach.

### "I need to customize the output format"
**Fix:** Request: "Export as Word document" (uses docx skill) or "Export as PDF" (uses pdf skill)

---

## Version History

**Current Version:** v2.0

**v2.0 (2026-02-19):**
- Complete redesign from knowledge-driven to input-driven generation
- Template-aware: extracts structure and per-section guidelines from provided template
- Source document cross-referencing for content population
- Enterprise guideline integration as constraints layer
- Controlled section-by-section interview (1–3 questions per turn)
- Coverage assessment (strong / partial / none) drives question density
- Row-level mode for large table sections
- Aggressive-default mode for impatient users
- Domain-specific quality checks applied contextually
- Confluence-ready Markdown output

**v1.0 (2026-02-13):**
- Initial release with knowledge-driven defaults
- Normal mode + QA-Senior mode
- Minimal questioning strategy (max 5 questions total)

---

## Support & Examples

**Skill Definition:**
- See `skills/doc-expert/SKILL.md` for complete skill specification

---

## Quick Reference Card

```
┌─────────────────────────────────────────────────────────────┐
│ DOC-EXPERT SKILL QUICK REFERENCE (v2.0)                     │
├─────────────────────────────────────────────────────────────┤
│ REQUIRED INPUTS:                                            │
│   - Document template (with section guidelines)             │
│   - Source document(s) (PRD, BRD, specs)                   │
│   - Enterprise guidelines (optional)                        │
│                                                             │
│ WORKFLOW:                                                   │
│   Phase 1: Parse inputs                                     │
│   Phase 2: Extract template structure                       │
│   Phase 3: Present plan → confirm (max 3 questions)        │
│   Phase 4: Build section by section (1–3 q/turn)           │
│   Phase 5: Assemble → quality check → deliver              │
│                                                             │
│ SAFE DEFAULTS:                                              │
│   - Status: DRAFT                                           │
│   - Version: 1.0                                            │
│   - Risk: Medium                                            │
│   - Gaps marked: [TBC — Default Applied]                   │
│                                                             │
│ OUTPUTS:                                                    │
│   - Confluence-ready Markdown                               │
│   - Word export (via docx skill)                            │
│   - PDF export (via pdf skill)                              │
│                                                             │
│ DON'T USE WHEN:                                             │
│   - You have a previous version → use /doc-revision        │
│   - Just need minor edits → edit directly                   │
└─────────────────────────────────────────────────────────────┘
```

---

**Ready to generate enterprise documents from your inputs? Try `/doc-expert` now.**
