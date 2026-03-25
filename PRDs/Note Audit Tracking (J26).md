# Note Audit Tracking (J26)

| Field | Value |
|-------|-------|
| **Product Owner** | @A.J. Meeks |
| **Helios Product** | Helios Core |
| **Helios Domains** | All Domains |
| **Product Theme** | Note Audit Tracking |
| **Related Tickets** | [Links or N/A] |

---

## Problem Statement

Note fields across Helios display inconsistent audit trail information — some note components surface a "Note History" table with context-specific columns (e.g., Status/Status Reason or Determination), while others show no history at all. This fragmented approach produces an unreliable audit record: the tracked fields differ by form, the format is not standardized, and large portions of the note surface have no tracking whatsoever. The result is an incomplete and inconsistent paper trail that makes it difficult to establish accountability, reconstruct documentation history during handoffs, or satisfy compliance and audit requirements.

---

## Objective

Replace existing context-specific Note History tables and add a standardized, inline expandable audit trail panel to note components across Helios. The panel captures and surfaces CRUD operations, record views, and template usage events with user identity and timestamp — providing a consistent, modern audit history at the note level regardless of note format or context. Lays the behavioral foundation for future RBAC-gated audit visibility.

---

## Designs & User Flows

| Screen | File |
|--------|------|
| Note component with collapsed audit panel | `~/Documents/Teminals/Claude/assets/Note-Audit-Tracking/Notes Refresh - Living Document (A2, J26).png` |
| Note component with expanded audit panel | `~/Documents/Teminals/Claude/assets/Note-Audit-Tracking/Notes Refresh - Living Document (A2, J26)-2.png` |
| Audit event row detail | `~/Documents/Teminals/Claude/assets/Note-Audit-Tracking/Notes Refresh - Living Document (A2, J26)-3.png` |

---

## Release Notes

> Click to expand for full release details

**Note Audit Tracking (J26)**

**Feature Summary:** Adds an inline, expandable audit trail panel to note components across Helios, capturing who created, viewed, edited, deleted, or applied a template to a note and when — surfacing a complete documentation history without interrupting the note-entry workflow.

**Key Capabilities:**
1. Replace all existing Note History table implementations with a standardized audit trail panel, consistent across all note formats
2. Display a collapsible audit trail panel on every note component, accessible inline without leaving the current screen
3. Capture and surface CRUD events (Created, Viewed, Edited, Deleted), including user name and timestamp
4. Track template usage events, recording which template was applied and by whom
5. Display an empty state for notes with no audit history

**User Impact:** Staff and administrators gain a clear, consistent record of note activity directly within the note context — eliminating the need to cross-reference disjointed audit data or escalate to engineering when documentation accountability is in question.

---

## User Stories & Requirements

### Story 1: Note Audit Trail Panel - UI

*Location(s): Any note component across Helios*

> **Scope note:** This story applies to standalone note components (plain textarea, rich text editor, and existing Note History pattern variants). Inline note fields embedded within larger forms (e.g., Driver Notes, Memo, Additional Info) are out of scope pending a separate research effort to determine applicable note locations and formats. *(Follow-on ticket: [TBD — link once created])*

**Requirements:**

1. Replace existing Note History tables with the standardized audit trail panel
   - Remove all existing "Note History" table implementations from note components across Helios
   - Migrate any existing Note History data (Date Created, By, context-specific columns) into the standardized audit event model via a one-time data migration script; migration must be idempotent and run without data loss
   - Existing entries map to `Created` or `Edited` events using the Date Created and By fields; context-specific columns (Status, Determination, etc.) are preserved as event metadata displayed in the event row
   - Migration script should be reviewed and approved before deployment; a rollback path must be defined in case of failure

2. Add an inline audit trail panel to all note components
   - Display a collapsible panel section below the note body on every note component
   - Panel toggle label: "Audit Trail" with a chevron icon indicating collapsed/expanded state
   - Default state: collapsed
   - Expanded state: displays full audit event list (Story 2)
   - Panel renders within the note component; does not open a modal or navigate away

3. Display audit event list in expanded state
   - Render events in reverse chronological order (most recent first)
   - Each audit event row displays:
     - Event type label (e.g., `Created`, `Viewed`, `Edited`, `Deleted`, `Template Applied`)
     - User full name in Last, First format (e.g., `Martinez, Rosa`)
     - Timestamp in user's locale format (e.g., `03/17/2026 at 2:14 PM`)
   - For `Template Applied` events, append the template name after the event label (e.g., `Template Applied — /contactvm`)
   - Event type labels styled as pill badges; color-coded by event category:
     - `Created` — green
     - `Viewed` — gray
     - `Edited` — blue
     - `Deleted` — red
     - `Template Applied` — purple
   - For notes with large audit histories, display the most recent 50 events by default with a "Load more" control to fetch additional events in batches; prevents performance degradation on high-activity notes

4. Display empty state when no audit events exist
   - Message: "No audit history available"
   - Subtext: "Activity on this note will appear here"

5. Display event count on collapsed panel toggle
   - When events exist, append count to toggle label: "Audit Trail (12)"
   - When no events exist, display: "Audit Trail" with no count

**Acceptance Criteria:**

| ID | Acceptance Criteria |
|----|---------------------|
| AC-1.1 | Verify all existing Note History table implementations are removed and replaced with the standardized audit trail panel |
| AC-1.2 | Test that existing Note History records are migrated and surfaced as audit events with correct user and timestamp |
| AC-1.3 | Verify audit trail panel renders below the note body on all in-scope note components across Helios |
| AC-1.4 | Test panel defaults to collapsed state and expands/collapses on toggle click |
| AC-1.5 | Verify expanded panel displays events in reverse chronological order with event type, user name, and timestamp |
| AC-1.6 | Test `Template Applied` event rows display the template name appended to the event label |
| AC-1.7 | Verify event type pills are color-coded correctly per event category |
| AC-1.8 | Test empty state displays correct message and subtext when no audit events exist |
| AC-1.9 | Verify collapsed panel toggle displays event count when events exist and no count when empty |
| AC-1.10 | Verify context-specific column values (e.g., Status, Determination) from migrated Note History records are preserved as event metadata and visible in the migrated event rows |
| AC-1.11 | Verify the audit event list defaults to the 50 most recent events and renders a "Load more" control when additional events exist; confirm subsequent batches load correctly |

---

### Story 2: Audit Event Capture - Behavior

*Location(s): Any note component across Helios*

**Requirements:**

1. Capture `Created` event
   - Record a `Created` event when a new note is saved for the first time
   - Capture: user identity, timestamp of save action

2. Capture `Viewed` event
   - Record a `Viewed` event when a note's content is rendered and visible to a user
   - Capture: user identity, timestamp of view
   - **Session definition:** A session is bounded by the authenticated user's login — it begins at login and ends at logout or session expiration (idle timeout or forced expiry). A new login after logout or expiration constitutes a new session.
   - A single session opening the same note multiple times records one `Viewed` event per session
   - Each authenticated user session is tracked independently — concurrent users viewing the same note simultaneously each generate their own `Viewed` event

3. Capture `Edited` event
   - Record an `Edited` event when an existing note's body or fields are modified and saved
   - Capture: user identity, timestamp of save action
   - Creating a note does not also generate an `Edited` event

4. Capture `Deleted` event
   - Record a `Deleted` event when a note is soft- or hard-deleted
   - Capture: user identity, timestamp of delete action
   - Audit events must be stored in an independent audit log table that is not coupled to the note record's lifecycle — this ensures the audit trail survives hard deletion
   - For soft-deleted notes, the audit trail remains accessible when the note record is viewed (e.g., in audit views or restored records)
   - For hard-deleted notes, the audit trail is retained in the audit log table and accessible to authorized users via audit tooling; it is not surfaced in the inline panel (since the note no longer exists)

5. Capture `Template Applied` event
   - Record a `Template Applied` event when a template is injected into the note body via slash command
   - Capture: user identity, timestamp of template injection, template name (trigger name, e.g., `/contactvm`)
   - If a user applies a second template to the same note, record a second `Template Applied` event

6. Associate user identity with all events
   - User identity resolves to the authenticated user performing the action
   - Display format: Last, First (e.g., "Martinez, Rosa") — consistent with production application name formatting
   - When user cannot be resolved: display "Unknown User"

**Acceptance Criteria:**

| ID | Acceptance Criteria |
|----|---------------------|
| AC-2.1 | Verify `Created` event is recorded with correct user and timestamp when a new note is saved |
| AC-2.2 | Test `Viewed` event is recorded when a note is rendered; verify only one event per session per user |
| AC-2.3 | Verify concurrent users viewing the same note simultaneously each generate an independent `Viewed` event |
| AC-2.4 | Verify `Edited` event is recorded on save of an existing note; confirm no `Edited` event fires on initial creation |
| AC-2.5 | Test `Deleted` event is recorded with correct user and timestamp on note deletion |
| AC-2.6 | Verify `Template Applied` event is recorded with user, timestamp, and template name on slash-command injection |
| AC-2.7 | Test that applying two templates to the same note generates two separate `Template Applied` events |
| AC-2.8 | Verify user resolves to authenticated user's full name; confirm "Unknown User" displays when user cannot be resolved |
| AC-2.9 | Verify `Deleted` event and full audit trail are preserved in the audit log table after hard deletion and remain accessible via audit tooling independent of the note record |

---

## Nice to Have

1. **Audit event filtering** — Add filter controls to the expanded audit panel to narrow events by type (e.g., show only `Edited` events) or by user.
   - Filter pills or dropdown above the event list

2. **Audit export** — Enable export of a note's audit trail to CSV from the expanded panel. (Not sure if applicable, could be achieved in a report or in system settings. Not sure of the need at the note field level)
   - "Export CSV" button in panel header; exports user, event type, template name (if applicable), timestamp

---
## Future State

1. **RBAC-gated audit visibility** — Restrict audit trail panel visibility by role using the RBAC Criteria Builder. Future state: audit panel renders only for roles with explicit audit trail view permission.
   - Depends on RBAC Field Permissions infrastructure

---

## Feature Flag Requirements

> Click to expand feature flag details

**Is a feature flag required?** YES

- **Flag Name:** NOTE_AUDIT_TRACKING
- **Flag Purpose:** Controls rollout of the note audit trail panel and event capture across all note components. Enables staged deployment to validate event capture accuracy and panel performance before full release. 
   - Enable will show audit panel across the application. 
   - Disable will hide audit panel across the application
- **Flag Owner:** @A.J. Meeks
