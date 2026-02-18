---
name: doc-expert
description: Generate enterprise documents (first draft) by combining reference inputs (requirements docs, document templates with section guidelines, and optional enterprise guidelines) with a controlled section-by-section interview. Designed to assist PM/BA roles in tech departments by minimizing cognitive load while producing Confluence-ready Markdown output. Activate when user provides a document template plus source materials and wants to generate a structured first draft.
---

# Doc Expert — Input-Driven Document Generation Skill

## Purpose

Generate a complete first-draft enterprise document by:

- Extracting structure and per-section guidance from a **document template**
- Cross-referencing **source documents** (requirements, specs, etc.) for content
- Applying **enterprise guidelines** (optional) as constraints
- Driving a **controlled section-by-section interview** to fill gaps
- Producing **Confluence-ready Markdown** output

**Target users:** PM, BA, QA Lead, and similar roles in enterprise tech departments.

**Core value:** Reduce the cognitive load of transforming scattered inputs into a structured, reviewable first draft.

---

## 1. Trigger Conditions

Activate when user intent includes:

- "Generate a [document type] based on these inputs"
- "Write a test plan / strategy from this PRD"
- "Fill in this template using these requirements"
- "Create first draft of [document] from these files"

Or when user provides a combination of:

- A document template (with section guidelines)
- One or more source/reference documents
- An optional set of enterprise guidelines

---

## 2. Input Model

### 2.1 Required Inputs

| Input | Role | Example |
|-------|------|---------|
| **Document Template** | Defines output structure, section order, and per-section guidance | Test Strategy and Plan Template (with section guidelines embedded) |
| **Source Document(s)** | Provides domain content to populate sections | PRD, BRD, feature spec, user stories |

### 2.2 Optional Inputs

| Input | Role | Example |
|-------|------|---------|
| **Enterprise Guidelines** | Org-specific constraints, standards, or checklists that must be respected | Test Guidelines, UAT Operating Guidelines, Definition of Done |
| **User Prompt** | Scoping instructions, emphasis, known constraints | See Section 2.3 |

### 2.3 Recommended User Prompt Format

The user prompt is freeform, but the following structure yields best results. Present this to user when they ask for guidance:

```
Document type: [e.g., Test Strategy and Plan]
Project/Feature name: [name]
Scope summary: [1–3 sentences on what is being delivered]
Known constraints or risks: [optional, anything already known]
Emphasis or special instructions: [optional, e.g., "Focus on integration testing", "UAT is critical for this release"]
```

If the user provides only a brief prompt, proceed — extract missing context from source documents.

---

## 3. High-Level Workflow

Five phases:

```
┌─────────────────────────────────────┐
│  Phase 1: Input Analysis            │
│  Parse all inputs, build context    │
├─────────────────────────────────────┤
│  Phase 2: Template Structure        │
│  Extract sections + guidelines      │
├─────────────────────────────────────┤
│  Phase 3: Build Plan                │
│  Classify sections, present plan    │
├─────────────────────────────────────┤
│  Phase 4: Section-by-Section Build  │
│  Controlled interview + generation  │
├─────────────────────────────────────┤
│  Phase 5: Assembly + Output         │
│  Combine, quality check, deliver    │
└─────────────────────────────────────┘
```

---

## 4. Phase 1 — Input Analysis

### 4.1 Parse All Inputs

For each uploaded file:

- Use `pdf` skill for PDF parsing
- Use `docx` skill for Word parsing
- Read Markdown directly

### 4.2 Build Internal Context Model

Extract and organize:

- **From Template:** Section tree, per-section guidelines/instructions, expected content shape (paragraph / table / list / hybrid)
- **From Source Documents:** Key entities (features, requirements, user stories, acceptance criteria, system components, data flows, integrations, actors/roles)
- **From Enterprise Guidelines:** Constraints, standards, checklists, terminology, mandatory inclusions

### 4.3 Gap Detection

After parsing, identify:

- Template sections where source documents provide **strong coverage** (can draft with minimal questions)
- Template sections where source documents provide **partial coverage** (will need targeted questions)
- Template sections where source documents provide **no coverage** (will need user input or safe defaults)

**Do NOT present gap analysis to user as a raw list.** Use it internally to calibrate question density in Phase 4.

---

## 5. Phase 2 — Template Structure Extraction

### 5.1 Section Tree

Build a structured representation:

- Section hierarchy (1 / 1.1 / 1.1.1)
- Heading levels
- Section titles
- Per-section guideline text (the template's own instructions for what belongs in each section)

### 5.2 Content Shape Detection

For each section, determine expected shape:

- **Paragraph-based** (overview, narrative)
- **Table-based** (test cases, requirement mappings, RACI)
- **List-based** (assumptions, risks, references)
- **Hybrid**

### 5.3 Guideline Extraction

For each section, extract the template's embedded guidance:

- What the section should contain
- What decisions or information are needed
- Any examples or formatting expectations

This per-section guidance drives the interview questions in Phase 4.

---

## 6. Phase 3 — Build Plan & User Confirmation

### 6.1 Present to User

Show:

1. **Extracted section tree** (numbered, with titles)
2. **Coverage assessment** per section:
   - ✅ Strong coverage from inputs — will draft with minimal questions
   - ⚠️ Partial coverage — will need targeted questions
   - ❓ No coverage — will need user input or defaults
3. **Enterprise guidelines detected** (if any) and how they will be applied

### 6.2 Ask User to Confirm

Use `AskUserQuestion` tool. Limit to:

- "Does this section structure look correct, or do you want to add/remove/reorder sections?"
- "Any sections you want to skip or deprioritize?"
- "Any additional context before we begin?"

**Maximum 3 questions in this phase.**

**Do NOT ask about:**
- Formatting preferences
- Document metadata (status, version, approvers — see Section 10)
- Approval workflow

---

## 7. Phase 4 — Section-by-Section Construction Loop

Work through sections **strictly in template order** unless user overrides.

### 7.1 General Loop per Section

For each section:

```
1. State which section is being worked on
2. Show what was extracted from inputs relevant to this section
3. Show the template's guideline for this section (brief summary)
4. IF strong coverage → present draft, ask user to confirm or adjust
5. IF partial coverage → present draft with gaps marked [TBC], ask targeted questions about gaps only
6. IF no coverage → ask focused questions based on template guideline, then draft
7. User confirms or requests changes
8. Finalize section, proceed to next
```

### 7.2 Question Rules (CRITICAL)

Borrowed and adapted from doc-revision controlled interview pattern:

**Per-turn limits:**
- **1–3 focused questions per turn**
- Questions must be **short and high-signal**
- Questions must be **directly derived from the template's section guideline + gaps in source documents**

**Question sourcing priority:**
1. Template section guideline says "this section should describe X" → ask about X if not found in source docs
2. Enterprise guideline requires specific information → ask if not found in source docs
3. Logical gap that would make the section incomplete → ask

**NEVER ask:**
- ❌ Obvious questions answerable from provided inputs
- ❌ Generic filler questions
- ❌ Formatting or style questions
- ❌ Metadata questions (status, approvers, version, dates)
- ❌ Speculative domain expansion ("should we also cover X?")
- ❌ "Is this okay?" — deliver the draft and let user react

**ALWAYS prefer:**
- ✅ Showing a drafted attempt and asking user to confirm/adjust
- ✅ Offering a reasonable default and asking "does this apply, or should I adjust?"
- ✅ Combining related questions into one turn

### 7.3 Table Sections (Row-Level Mode)

If a section contains a large table (e.g., test cases, requirement traceability):

- Treat each row (or logical group of rows) as an atomic unit
- Draft a batch of rows (3–5), present for review
- Iterate until table is complete

**Never attempt to generate an entire large table in one shot without review.**

### 7.4 Handling Enterprise Guidelines

When enterprise guidelines impose requirements on a section:

- State the constraint: "Per [Guideline Name], this section must include X."
- Apply it in the draft
- If guideline conflicts with template structure, flag to user and ask for resolution

### 7.5 Safe Defaults

When user cannot answer or chooses to skip:

- Apply a conservative default
- Mark with **[TBC — Default Applied]** so reviewers can identify
- State the default chosen and rationale briefly

---

## 8. Phase 5 — Assembly & Output

### 8.1 Combine All Sections

Assemble the full document in template order.

### 8.2 Quality Check

Before delivering, verify:

- All template sections are present (or explicitly marked as skipped by user)
- No orphan [TBC] markers without explanation
- Enterprise guideline requirements are addressed
- Tables are consistently formatted
- Heading hierarchy is correct and consistent
- Cross-references between sections are valid

**Domain-specific quality checks** (apply only when relevant):

| Document Type | Additional Checks |
|---------------|-------------------|
| Test Strategy/Plan | Happy path covered? Negative paths? Edge cases? Integration impact? Entry/exit criteria defined? Regression scope? |
| Requirements Doc | Acceptance criteria present? Requirements uniquely identifiable? Traceability possible? |
| Release Plan | Rollback plan present? Dependency list complete? |

### 8.3 Output Format

**Confluence-ready Markdown:**

- Proper heading hierarchy (## / ### / ####)
- Clean tables (pipe-delimited, aligned)
- Numbered/bulleted lists where appropriate
- Bold for emphasis per template conventions
- No raw formatting artifacts
- Copy-paste ready for Confluence

### 8.4 Deliver

- Present the complete document
- State: **"This document is a DRAFT and requires review before approval."**
- List any [TBC] items that need resolution
- Use `docx` skill if user requests Word export

---

## 9. Interaction with AskUserQuestion Tool

Use `AskUserQuestion` for all structured questions during the build loop.

**Patterns:**

| Situation | Approach |
|-----------|----------|
| Binary choice | Single question, 2 options |
| Small set of options | Single question, 3–4 options |
| Need specific content | Ask in prose (open-ended cannot use widget) |
| Section confirmation | Show draft + ask "Confirm, adjust, or rewrite?" |

**Pacing:**
- Phase 3 (Build Plan): Max 3 questions total
- Phase 4 (per section): 1–3 questions per turn, aim for 1 where possible
- If user seems impatient or says "just do it" / "use defaults" → switch to aggressive-default mode: draft all remaining sections with safe defaults, present for bulk review

---

## 10. Metadata Handling

**CRITICAL: Never ask users about document metadata.**

| Field | Strategy |
|-------|----------|
| Status | Always **"DRAFT"** |
| Version | **1.0** (first version) or extract from user prompt |
| Author | Leave blank or extract from user prompt |
| Approvers / Reviewers | Leave blank |
| Last Modified | Current date |
| Next Review Date | Leave blank |

**Rationale:** Generated documents are always first drafts requiring human review. Approval workflows are outside this tool's scope.

---

## 11. Strict Constraints

1. **Never auto-generate entire document at once.** Always work section by section with user confirmation.
2. **Never over-question.** Prefer drafting with defaults over asking unnecessary questions.
3. **Never ask about metadata** (status, approvers, version, dates).
4. **Never introduce sections not in the template** unless user explicitly requests.
5. **Always preserve template structure and style.**
6. **Always treat output as DRAFT.**
7. **Always mark uncertain content with [TBC].**
8. **Keep reasoning internal.** Show final structured output, not deliberation.
9. **Never ask "Is this okay?"** — deliver the draft and let user provide feedback.
10. **Never ask formatting preferences** — follow the template.
11. **Always cross-reference enterprise guidelines** when provided.
12. **If source documents are insufficient and user cannot clarify, use safe conservative defaults and mark [TBC].**

---

## 12. Error Handling

| Situation | Response |
|-----------|----------|
| No template provided | Ask user to provide one, or offer to use a generic structure for the document type |
| No source documents provided | Warn that output will be heavily reliant on user interview; proceed with question-driven approach |
| Template has no section guidelines | Extract section titles, infer purpose from title and position, proceed with best-effort |
| Source documents contradict each other | Flag contradiction to user, ask for resolution before drafting affected section |
| Enterprise guideline conflicts with template | Present conflict, ask user which takes precedence |
| User wants to skip all questions | Switch to full-default mode, generate complete draft, present for bulk review with all gaps marked [TBC] |

---

## 13. Integration with Other Skills

| Skill | When Used |
|-------|-----------|
| `pdf` | Parsing PDF inputs (requirements, guidelines, templates) |
| `docx` | Parsing Word inputs; generating Word export if requested |
| Other document skills | As needed for specific input formats |

**Do NOT depend on external template files or prebuilt libraries.**

---

## 14. Version History

**Current Version: v2.0**

**v2.0 (2025-02-19):**
- Complete redesign from knowledge-driven to input-driven generation
- Template-aware: extracts structure and per-section guidelines from provided template
- Source document cross-referencing for content population
- Enterprise guideline integration as constraints layer
- Controlled section-by-section interview (adapted from doc-revision patterns)
- Coverage assessment (strong / partial / none) drives question density
- Row-level mode for large table sections
- Aggressive-default mode for impatient users
- Domain-specific quality checks applied contextually
- Confluence-ready Markdown output

**v1.0 (2025-02-13):**
- Initial release with knowledge-driven defaults
- Normal mode + QA-Senior mode

---

## Appendix A: Recommended User Prompt Template

Provide this to users who ask how to get the best results:

```
Document type: [e.g., Test Strategy and Plan]
Project/Feature name: [name]
Scope summary: [1–3 sentences describing what is being built/changed]
Known constraints or risks: [anything already identified]
Special instructions: [areas of emphasis, things to skip, etc.]

Attached files:
- [Template]: <filename>
- [Requirements]: <filename>
- [Guidelines]: <filename> (optional)
```

---

## END OF SKILL
