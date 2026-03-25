# Note Audit Tracking (J26)

| Field | Value |
|-------|-------|
| **Product Owner** | @AJ Meeks |
| **Helios Product** | Helios Core |
| **Helios Domains** | All Domains |
| **Product Theme** | Notes |
| **Related Tickets** | N/A |

---

## Problem Statement

Note fields across Helios capture entered-by timestamp data but do not surface it in the user interface, leaving care managers, utilization managers, and client services staff without visibility into who documented what and when. Without a standardized audit display, note history is inconsistent — some fields expose a partial "Note History" table while others show nothing — creating gaps in accountability, auditability, and clinical handoff accuracy.

---

## Objective

Standardize a collapsible Audit Trail panel across every Notes field in Helios that displays a chronological record of all note events — creation, edits, views, and template applications — attributed to the entering user with date and time. This provides a consistent, trustworthy audit surface for all roles and establishes the foundation for compliance reporting and downstream access control over note history.

---

## Designs & User Flows

*Screenshots stored in `~/Documents/Claude/assets/note-audit-tracking/`*

| Screen | File |
|--------|------|
| Current Note History table (legacy) | `~/Documents/Claude/assets/note-audit-tracking/note-history-legacy.png` |
| Audit Trail panel — expanded state | `~/Documents/Claude/assets/note-audit-tracking/audit-trail-expanded.png` |

---

## Release Notes

> Click to expand for full release details

**Note Audit Tracking**

**Feature Summary:** A standardized, collapsible Audit Trail panel rendered below every Notes field across all Helios domains, displaying a reverse-chronological log of note events attributed to the entering user with full date and time.

**Key Capabilities:**
1. Display a collapsible Audit Trail panel with event count badge below every Notes field across all Helios domains
2. Render per-event rows with color-coded action badges (`Created`, `Edited`, `Viewed`, `Template Applied`), user name, and timestamp
3. Preserve both original creation and all subsequent edit timestamps as permanent, read-only records
4. Capture and display `Template Applied — /[templatename]` events when a Notecue template is injected (Story 1, per Notecue A2)
5. Replace all legacy "Note History" table implementations with the standardized Audit Trail panel

**User Impact:** Every staff member interacting with a note — regardless of domain or screen — gains immediate visibility into who created, edited, or viewed a note and when. Eliminates inconsistent note history displays and removes ambiguity during clinical handoffs, audits, and compliance reviews.

---

## User Stories & Requirements

### Story 1: Audit Trail Panel — UI

*Location(s): All Notes fields across all Helios domains*

**Requirements:**

1. Display Audit Trail panel below all Notes fields
   - Render a collapsible Audit Trail panel directly below the note input area on every Notes field across all Helios domains
   - Panel header label: "Audit Trail"
   - Display a shield icon to the left of the "Audit Trail" label
   - Display a count badge (blue pill) to the right of the "Audit Trail" label showing the total number of audit events for that note (e.g., `5`)
   - Display a collapse/expand chevron (▲/▼) in the top-right corner of the panel header
   - Panel defaults to expanded state on load
   - Replace all existing "Note History" table implementations across all domains with this standardized Audit Trail panel — no per-domain variations in markup, styling, or behavior

2. Display audit event rows
   - Render each audit event as a single row containing:
     - Action badge (left-aligned): color-coded pill with action label
       - `Created` — green badge
       - `Edited` — blue badge
       - `Viewed` — gray badge
       - `Template Applied — /[templatename]` — purple badge, appending the slash-command trigger name used (logic in Story 1, Notecue A2)
     - User name (adjacent to badge): displayed in Last name, First name format (e.g., *Martinez, Rosa*)
     - Timestamp (right-aligned): date and time in user's locale format (e.g., `03/17/2026 at 11:04 AM`)
   - Sort rows in reverse chronological order (most recent event at top)

3. Preserve original and all subsequent timestamps
   - The `Created` event row reflects the original save timestamp and is always present on any saved note
   - Each subsequent save generates a new `Edited` event row with its own timestamp
   - All `Created` and `Edited` rows are permanently retained — no row is overwritten or removed
   - Timestamps are read-only; no user role can modify or delete audit entries

4. Capture Template Applied events
   - When a note template is injected via slash command (Notecue A2, Story 2), generate a `Template Applied — /[templatename]` audit event at the moment of injection
   - Record this as a distinct event type, separate from `Created` or `Edited`

5. Display empty state
   - When a note has not yet been saved, display inside the Audit Trail panel: *"No history yet. Audit trail will appear after the note is saved."*

**Acceptance Criteria:**

| ID | Acceptance Criteria |
|----|---------------------|
| AC-1.1 | Verify Audit Trail panel renders below every Notes field across all Helios domains |
| AC-1.2 | Test panel header displays shield icon, "Audit Trail" label, blue count badge reflecting total event count, and collapse/expand chevron |
| AC-1.3 | Verify panel defaults to expanded state on load |
| AC-1.4 | Test chevron collapses and expands the panel, toggling between ▲ and ▼ states |
| AC-1.5 | Verify `Created` badge renders green, `Edited` blue, `Viewed` gray, and `Template Applied` purple with correct trigger name appended |
| AC-1.6 | Test each row displays action badge, user name in Last, First format, and right-aligned timestamp in locale format |
| AC-1.7 | Verify rows are sorted in reverse chronological order with most recent event at top |
| AC-1.8 | Verify `Created` event row is always present on any saved note and reflects the original save timestamp |
| AC-1.9 | Test each subsequent save generates a new `Edited` row without overwriting prior entries |
| AC-1.10 | Verify `Template Applied — /[templatename]` event row is generated on slash-command template injection and displays the correct trigger name |
| AC-1.11 | Verify audit entries are read-only and no user role can modify or delete rows |
| AC-1.12 | Test empty state message *"No history yet. Audit trail will appear after the note is saved."* displays when note has not been saved |
| AC-1.13 | Verify all legacy "Note History" table implementations are replaced by the standardized Audit Trail panel with no per-domain variations |

---

## Feature Flag Requirements

> Click to expand feature flag details

**Is a feature flag required?** YES

- **Flag Name:** NOTE_AUDIT_TRAIL
- **Flag Purpose:** Controls rollout of the standardized Audit Trail panel across all Notes fields. Enables gradual deployment to validate consistent rendering across domains and confirm legacy Note History table replacement before full release.
- **Flag Owner:** @AJ Meeks
