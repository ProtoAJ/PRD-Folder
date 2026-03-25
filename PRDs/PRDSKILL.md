
# PRD Writing Skill
> Auto-discovered skill for writing HELIOS Product Requirement Documents

---

## When to Use
Invoke when AJ asks to:
- Write a PRD
- Create a product requirements document
- Document feature requirements
- Spec out a feature

---

## Required Inputs
Before writing, collect the following. If any are missing, ask for all of them in a single message before proceeding:
| Input | Required | Default if Not Provided |
|-------|----------|------------------------|
| Feature Name | Yes | — |
| Ticket ID(s) | Yes | — |
| Helios Product | No | Helios Core |
| Helios Domains | No | All Domains |
| Product Owner | No | @AJ Meeks |
| Related Tickets | No | N/A |
| Problem / Objective description | Yes | — |

---

## PRD Template Structure
```
# [Feature Name] ([Ticket IDs])
| Field | Value |
|-------|-------|
| **Product Owner** | @AJ Meeks |
| **Helios Product** | [Helios Core / Helios UM / Helios CM] |
| **Helios Domains** | [Comma-separated domains] |
| **Product Theme** | [Theme name] |
| **Related Tickets** | [Links or N/A] |

---

## Problem Statement
[Current state limitation in 1-2 sentences]. [Specific pain points with examples]. [Consequence/risk if not addressed].

---

## Objective
[Action verb] + [what we're building] + [how it works at high level]. [Business value or foundation for future work].

---

## Designs & User Flows
[Screenshots/mockups placeholder]

---

## Release Notes
> Click to expand for full release details
**[Feature Name]**
**Feature Summary:** [1-2 sentence summary]
**Key Capabilities:**
1. [Capability]
2. [Capability]
**User Impact:** [How this changes workflows]

---

## User Stories & Requirements
### Story N: [Story Name] - [UI/Backend/Frontend]
*Location(s): [Navigation path using > separators]*
**Requirements:**
1. [Primary requirement]
   - [Sub-requirement]
   - [Sub-requirement]
**Acceptance Criteria:**
| ID | Acceptance Criteria |
|----|---------------------|
| AC-N.1 | [Verify/Test/Validate] + [testable statement] |

---

## Feature Flag Requirements
> Click to expand feature flag details
**Is a feature flag required?** [YES / NO / TBD]
- **Flag Name:** [Name]
- **Flag Purpose:** [Why]
- **Flag Owner:** @[Owner]
```
---

## Writing Style Rules
### Voice
- Use "Display...", "Add...", "When X, Y..." phrasing
- Never say "users want" or "users should see"
- Use "Enable X capability" not "Allow users to do X"
- **Write to build, not audit:** "Add X where not already exposed" instead of "Apply X" or "Expose X" — assume implementation, not inventory
### Formatting
- Location paths: `Admin portal > Settings > Permissions`
- Field labels in quotes: `"Configuration Name"`
- States in title case: `Active`, `Inactive`, `Show`, `Hide`
- Conditional logic: `When X selected:` followed by bullets
- Cross-references: `(Story 2)`, `(logic in Story 3)`
- Modal dimensions: `~80% screen width`
- **Tables vs bullets:** Use bulleted lists for simple domain/item lists. Save tables for when multiple columns add meaningful info.
- **Nice to Have / Deferred sections:** Use numbered bullets with sub-items (same structure as stories), not H3 headings with separate sections
### Requirements
- Numbered top-level (1. 2. 3.)
- Bulleted sub-requirements (-)
- Every requirement must be testable
- Specify exact labels, placeholder text, error messages
- Reference related stories inline
### Acceptance Criteria
- Table format with ID column (AC-[Story].[Number])
- Use "Verify...", "Test...", "Validate..." language
- One testable statement per criterion
- Cross-reference shared behavior
### What to Always Include
- Empty states with exact message text
- Error messages with exact text
- Conditional behaviors (when X, then Y)
- Edge cases
- Performance requirements where relevant
- Audit/logging requirements
### Output Behavior
- **No preamble:** Do not introduce the PRD before writing it. Output the document directly.
- **No postamble:** Do not summarize what was written after completing the PRD. Stop at the last line of the document.
- **Write in one pass:** Write the complete document without pausing between sections for confirmation unless explicitly asked to do so.
### What to Avoid
- Time estimates
- Vague requirements ("improve performance")
- "The user should see..." language
- Repeating requirements across stories (cross-reference instead)
- Emdashes
- **Separate backend storage stories:** Data models, indexes, foreign keys are engineering's domain. Stories should be UI + behavior only. Implementation details don't belong in PRDs.

---

## Reference PRDs
Location: `/Users/andrewmeeks/Documents/Teminals/Claude/PRD-Folder/PRDs`
| PRD | Key Patterns |
|-----|--------------|
| Note Audit Tracking (J26) | Standardization of note auditing |
| Note Templates 2.0 (A2) | Snippets to speed up text input in note fields |

---

## Output Location
Save PRDs to: `/Users/andrewmeeks/Documents/Teminals/Claude/PRD-Folder/PRDs/[Feature Name].md`