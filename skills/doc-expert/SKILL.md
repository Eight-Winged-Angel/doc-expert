# Doc-Expert Skill

**Input-Driven Enterprise Document Generation (Optimized for Speed & Reproducibility)**

Doc-Expert is a Claude Code skill that generates enterprise-ready draft documents by combining a document template, source materials, and an optional set of enterprise guidelines. Designed for PM, BA, and QA Lead roles who need structured first drafts with minimal cognitive load and maximum speed.

**Version 2.1** focuses on reproducibility and minimal turnaround time while maintaining document quality.

---

## When to Use Doc-Expert

**Use doc-expert when:**
- You have a document template (with section guidelines) and source materials (PRD, BRD, feature specs)
- You want the skill to extract structure from the template and populate it from your inputs
- You need a structured first draft quickly and reproducibly
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

## High-Level Workflow (v2.1 - Optimized)

Three core phases (non-negotiable for quality):

```
Phase 1: Input Analysis & Template Structure Extraction
  ├─ Parse all inputs (template, requirements, guidelines)
  ├─ Extract section tree from template
  ├─ Map requirements content to sections
  └─ Assess coverage per section
  Time: ~25 seconds

Phase 2: Batch Section Generation
  ├─ Generate all sections with strong coverage
  ├─ Generate sections with partial coverage (use safe defaults)
  ├─ Skip user confirmation for standard templates
  └─ Mark gaps with [TBC — Default Applied]
  Time: ~30-45 seconds

Phase 3: Quality Validation & Output
  ├─ Check all sections present
  ├─ Validate guideline compliance
  ├─ Check formatting consistency
  └─ Deliver Confluence-ready Markdown
  Time: ~5 seconds

Total time: ~60-75 seconds
```

**[FUTURE VERSION - v3.0]** Additional optimizations planned:
- Template structure caching for repeat use
- Preset modes: `--fast`, `--standard`, `--thorough`
- Project-specific input templates
- Requirements pre-processing with summary extraction
- Batch processing of multiple documents
- `--repeat-last` command for same-template workflows

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

**Step 2: Doc-Expert Analyzes Inputs (Phase 1)**
```
Claude: [Parsing template → extracting section tree]
Claude: [Parsing requirements → building content map]
Claude: [Parsing guidelines → identifying constraints]
Claude: [Assessing coverage per section]
```

**Step 3: Batch Generation (Phase 2)**
```
Claude: "Generating document sections..."
[Generates all sections in batch]
[Applies safe defaults where needed]
[Marks gaps with [TBC — Default Applied]]
```

**Step 4: Quality Check & Delivery (Phase 3)**
```
Claude: [Validates all sections present]
Claude: [Checks guideline compliance]
Claude: [Validates formatting]
Claude: [Delivers complete Confluence-ready Markdown]
Claude: "This document is a DRAFT and requires review before approval."
Claude: [Lists any [TBC] items needing resolution]
```

**Step 5: Optional — Word Export**
```
User: "Give me a Word version"
Claude: [Uses docx skill to export]
```

**Total interaction time:** ~60-75 seconds with zero questions asked (unless critical gaps exist)

---

## Phase Details

### Phase 1 — Input Analysis & Template Structure Extraction (~25 seconds)

**CORE - NON-NEGOTIABLE**

**Template Structure Extraction:**
- Parse template file (PDF via `pdf` skill; Word via `docx` skill; Markdown directly)
- Extract section hierarchy (1 / 1.1 / 1.1.1)
- Extract per-section guidance text
- Detect content shape per section (paragraph / table / list / hybrid)
- Time: ~10 seconds
- **Why essential:** Ensures output matches expected enterprise format

**Requirements Content Mapping:**
- Parse source documents (PRD, BRD, specs)
- Extract: features, requirements, user stories, acceptance criteria, system components, integrations, actors, constraints, risks
- Map content to template sections
- Internally classify coverage: **strong / partial / none**
- Time: ~15 seconds
- **Why essential:** Populates sections with actual project data

**Guidelines Compliance Check:**
- Parse enterprise guidelines
- Extract: constraints, standards, mandatory inclusions, checklists
- Register as validation rules
- Time: ~5 seconds (included in above)
- **Why essential:** Ensures document meets enterprise standards

### Phase 2 — Batch Section Generation (~30-45 seconds)

**CORE - NON-NEGOTIABLE (optimized for speed)**

For each section in the template:

**Strong Coverage Sections:**
- Generate complete section from extracted content
- No user confirmation needed
- Move to next section

**Partial Coverage Sections:**
- Generate section with safe defaults for gaps
- Mark gaps with `[TBC — Default Applied]` + rationale
- No user confirmation needed
- Move to next section

**No Coverage Sections:**
- Generate section using safe defaults (see Safe Defaults table)
- Mark with `[TBC — Default Applied]` + rationale
- No user confirmation needed
- Move to next section

**Large Table Sections (e.g., test cases, RACI):**
- Generate 5-10 representative rows
- Mark as `[TBC — Expand as needed]`
- No iterative review in v2.1

**Time: ~30-45 seconds**
**Why essential:** Generates the actual document content
**v2.1 optimization:** Zero questions, batch processing, safe defaults

**[FUTURE VERSION - v3.0]** Section-by-section controlled interview:
- Show draft → ask 1-3 targeted questions for gaps only
- User option: "use defaults" to skip remaining questions
- Row-by-row generation for large tables

### Phase 3 — Quality Validation & Output (~5 seconds)

**CORE - NON-NEGOTIABLE**

Quality checks before delivery:
- ✅ All template sections present (or explicitly noted as skipped)
- ✅ No orphan [TBC] markers without explanation
- ✅ Enterprise guideline requirements addressed
- ✅ Tables consistently formatted
- ✅ Heading hierarchy correct
- ✅ Cross-references between sections valid

Domain-specific checks:

| Document Type | Additional Checks |
|---------------|-------------------|
| Test Strategy/Plan | Happy path? Negative paths? Edge cases? Integration impact? Entry/exit criteria? Regression scope? |
| Requirements Doc | Acceptance criteria? Requirements uniquely identifiable? Traceability possible? |
| Release Plan | Rollback plan? Dependency list complete? |

**Time: ~5 seconds**
**Why essential:** Catches missing sections, formatting issues, compliance gaps

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

Doc-expert never blocks on missing information. All defaults are marked **[TBC — Default Applied]** so reviewers can identify them.

| Missing Info | Default Value | Rationale |
|--------------|---------------|-----------|
| Document status | DRAFT | Standard for all initial documents |
| Version | 1.0 | First version |
| Test environment | Staging (prod-like) | Most common for enterprise |
| Risk level | Medium | Conservative estimate |
| Approvers | [Leave blank] | Requires user input |
| Entry criteria | Build stable, major defects closed | Industry standard |
| Exit criteria | No critical defects, acceptable minor defects | Industry standard |
| Test types | Functional, Integration, UAT | Standard test pyramid |
| Test approach | Black box, requirements-based | Most common for BA/PM |
| RACI - Responsible | QA Lead | Standard assignment |
| RACI - Accountable | Project Manager | Standard assignment |
| Timeline | [Based on requirements complexity] | Inferred from scope |

---

## Metadata Handling

Doc-expert **never asks** about document metadata — all auto-filled:

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
| No source documents | Warn that output will rely on safe defaults; proceed with default-driven approach |
| Template has no section guidelines | Infer section purpose from title and position; proceed with best-effort |
| Source documents contradict each other | Flag contradiction in [TBC] marker; use most recent or most detailed source |
| Enterprise guideline conflicts with template | Flag conflict in [TBC] marker; follow template structure, note guideline requirement |

---

## Tips for Best Results

### 1. Provide a Template with Embedded Guidelines

The richer your template's per-section instructions, the better the output quality.

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

Both work — but the first produces richer, more accurate content.

---

### 2. Provide Source Documents

The skill extracts content from your PRDs, BRDs, and specs. The more you provide, the fewer [TBC] markers you'll see.

---

### 3. Reuse Templates for Reproducibility

Once you have a working template with good section guidelines:
- Save it as your standard template
- Reuse for similar document types
- Consistent structure = consistent output

**[FUTURE VERSION - v3.0]** Template caching:
```
/doc-expert --template-cache test-plan-standard
```
Reuses parsed structure, skips template parsing step (~10 sec saved)

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
| **Questions** | Zero (v2.1 optimized) | 3–5 questions per turn |
| **Metadata** | Auto-fills all metadata | Inherits/auto-increments from previous version |
| **Output** | First draft from inputs | Updated version |
| **Speed** | 60-75 seconds | 5-8 minutes |

**Use doc-expert when:** You have a template and source materials and need a first draft quickly
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

### "The document has too many [TBC] markers"
**Issue:** More gaps than expected
**Fix:** Provide more detailed source documents (PRD, BRD, specs) with richer content. The skill can only populate sections with information from your inputs.

### "The document is missing critical sections"
**Issue:** Important content omitted
**Fix:** Check that your template includes the section. The skill only generates sections present in the template structure.

### "I don't have a template"
**Fix:** Tell doc-expert what document type you need. It will offer to use a generic structure for that type and proceed with a default-driven approach.

### "I need to customize the output format"
**Fix:** Request: "Export as Word document" (uses docx skill) or "Export as PDF" (uses pdf skill)

### "The document doesn't match our exact format"
**Fix:** Provide a template file that matches your exact format requirements. The skill follows the template structure precisely.

---

## Version History

**Current Version:** v2.1

**v2.1 (2026-02-24):**
- **OPTIMIZED FOR SPEED & REPRODUCIBILITY**
- Reduced to 3 core phases (from 5)
- Eliminated user confirmation in standard workflows (zero questions)
- Batch section generation with safe defaults
- Target time: 60-75 seconds (from 5-8 minutes)
- All [TBC] markers include rationale
- Future optimizations marked [FUTURE VERSION - v3.0]

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

## Planned Enhancements [FUTURE VERSION - v3.0]

The following features are planned for future releases and marked throughout this document:

### Template Caching
```
/doc-expert --template-cache <template-id>
```
- Cache parsed template structures for reuse
- Skip template parsing on subsequent uses
- Time saved: ~10 seconds per use

### Preset Modes
```
/doc-expert --fast      # Zero questions, all defaults (current v2.1 default)
/doc-expert --standard  # 1 question per section max
/doc-expert --thorough  # Full interview mode (v2.0 behavior)
```

### Project-Specific Input Templates
```
/doc-expert --project lumi --doc-type test-plan
```
Pre-loads:
- Project name, known components
- Default template and guidelines paths
- Only requires: new requirements file

### Requirements Pre-processing
- Extract summary from large PRDs automatically
- Create focused requirement_summary.md
- Reduces parsing time by 60-70%

### Batch Processing
```
/doc-expert --batch requirements/*.pdf --template test-plan.docx
```
Generate multiple documents in one session

### Repeat-Last Command
```
/doc-expert --repeat-last
```
Reuses previous template, guidelines, and project context
Only updates source requirements
Time: 30-40 seconds (50% reduction)

---

## Support & Examples

**Skill Definition:**
- See `skills/doc-expert/SKILL.md` for complete skill specification

**Example Templates:**
- Test Strategy and Plan Template
- Requirements Document Template
- Release Plan Template

**Example Source Documents:**
- Product Requirements Document (PRD)
- Business Requirements Document (BRD)
- Feature Specifications

---

## Quick Reference Card

```
┌─────────────────────────────────────────────────────────────┐
│ DOC-EXPERT SKILL QUICK REFERENCE (v2.1)                     │
├─────────────────────────────────────────────────────────────┤
│ REQUIRED INPUTS:                                            │
│   - Document template (with section guidelines)             │
│   - Source document(s) (PRD, BRD, specs)                   │
│   - Enterprise guidelines (optional)                        │
│                                                             │
│ WORKFLOW (OPTIMIZED):                                       │
│   Phase 1: Parse + extract + map (~25s)                     │
│   Phase 2: Batch generate all sections (~30-45s)           │
│   Phase 3: Quality check + deliver (~5s)                    │
│   Total: ~60-75 seconds, ZERO questions                     │
│                                                             │
│ SAFE DEFAULTS:                                              │
│   - Status: DRAFT                                           │
│   - Version: 1.0                                            │
│   - All gaps marked: [TBC — Default Applied]               │
│   - Rationale included for each default                     │
│                                                             │
│ OUTPUTS:                                                    │
│   - Confluence-ready Markdown                               │
│   - Word export (via docx skill)                            │
│   - PDF export (via pdf skill)                              │
│                                                             │
│ DON'T USE WHEN:                                             │
│   - You have a previous version → use /doc-revision        │
│   - Just need minor edits → edit directly                   │
│                                                             │
│ FUTURE ENHANCEMENTS (v3.0):                                 │
│   - Template caching                                        │
│   - --fast/--standard/--thorough modes                      │
│   - Project templates                                       │
│   - --repeat-last command                                   │
└─────────────────────────────────────────────────────────────┘
```

---

## Core Non-Negotiable Process Summary

For reproducibility and speed, these steps **CANNOT** be skipped:

| Phase | Step | Time | Why Essential |
|-------|------|------|---------------|
| 1 | Template Structure Extraction | ~10s | Ensures output format matches enterprise template |
| 1 | Requirements Content Mapping | ~15s | Populates sections with real project data |
| 1 | Guidelines Compliance Check | ~5s | Validates enterprise standards adherence |
| 2 | Section Generation | ~30-45s | Creates the actual document content |
| 3 | Quality Validation | ~5s | Catches format errors, missing sections, compliance gaps |

**Total minimum time:** 60-75 seconds

All other features (confirmations, interviews, caching, batch modes) are optimizations that can be added in future versions without compromising the core value: **fast, reproducible, compliant first drafts from templates and source materials.**

---

**Ready to generate enterprise documents in under 90 seconds? Try `/doc-expert` now.**