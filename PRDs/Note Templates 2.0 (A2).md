#  Note Templates (A2)

| Field | Value |
|-------|-------|
| **Product Owner** | @A.J. Meeks |
| **Helios Product** | Helios Core |
| **Helios Domains** | All Domains |
| **Product Theme** | Note Templates |
| **Related Tickets** | TBD |

---

## Problem Statement

Documentation within Helios is a high-friction, manual process that relies on individual user discretion for formatting and content. Free-form note entry averages ~2 minutes per note, creates inconsistent terminology (e.g., "LVM," "left msg," "no answer" for the same event), and frequently omits mandatory compliance data points. Without structured entry, notes produce "dark data" that is difficult to audit, extract for reporting, or hand off between staff — creating legal, clinical, and operational risk.

## Objective

Integrate a structured Template Framework into the existing note-taking interface, enabling slash-command triggers with auto-populated tokens, tab-through placeholder navigation, and a tag taxonomy that ties into the RBAC Field Permissions Criteria Builder. This provides the foundation for compliant, standardized documentation at scale while reducing average note creation time from ~2 minutes to under 20 seconds.

---

## Release Notes

**Feature Summary:** Structured note templates with slash-command triggers, auto-populated tokens, tab-through placeholders, and a tag system integrated with RBAC field permissions.

**Key Capabilities:**
1. Configure note templates with slash-command triggers, bracketed token placeholders, and tag assignments from a dedicated Super Admin settings screen
2. Invoke templates in any Notes field via `/` trigger with real-time fuzzy search and keyboard-first selection
3. Auto-populate `[DATE]`, `[TIME]`, and `[NAME]` tokens on insertion; tab through remaining placeholders
4. Manage a controlled tag vocabulary applied to templates and surfaced on rendered notes

**User Impact:** Eliminates "blank-page syndrome" for documentation. Staff type a slash command, select a template, tab through fields, and save — replacing 2-minute manual entries with ~15-second structured completions. Tags enable downstream reporting, filtering, and role-based access control over note content.

---

## User Stories & Requirements

### Story 1: Template Configuration Screen Update - UI

*Location(s): Super Admin > Settings > System Settings > Note Templates*

#### 0. Note Templates Version Toggle
- Add a version toggle with the following options:
   - Legacy
   - New

***The following updates should only be visible when a Super Admin sets Version = "New"***

#### 1. Note Templates Main Screen Update
- Currently the Note Templates main screen appears as follows:
      
   [Legacy Note Templates Screen]

- Clicking edit on the existing screen displays the following:
      
   [Legacy edit Note Template Screen]

- **UPDATE** existing configuration screen as follows:
   - Settings > Note Template will now display the following tabs:
      - Templates (Existing)
      - Tags (New - Created in Story 2)

      [New Main Screen]

#### 2. Note Templates - Templates Tab Table Update
   - **Template Name** (Required): Displays trigger name prefixed with / (e.g., /contactvm)
      - Set to <> for any existing note Templates 
   - **Template Text**: Displays template body as preview text, truncated with "..." if it exceeds column width
      - Set to <> for any existing Note Templates
   - **Template Tags**: Displays assigned tags as pill badges (e.g., "External", "Provider")
      - Should be blank for existing templates by default - only applies if tags are added after feature release
   - **Actions**: No change to existing options

#### 3. Note Templates - Create or Update Templates Form
   - Currently the create Notes Template screen appears as follows:

      [Legacy Create Note Template]
   
   - **UPDATE** existing Add/Edit template form as follows:

      [New Add Template Form]

   - Create/Edit form header behavior:
      - Clicking [+ Add New Template] from main page titles the form "Add Template"
         - New templates will have all functionality outlined below.
         - Editing a legacy template will populate Template Name with current name, and Template Text with name.
            - Legacy templates will not have tags associated, and template tags field will be blank
      - Clicking [Edit ] from actions column titles the form "Edit Template"
   - Create/Edit Form Fields:
      - **Template Name** (Required): Text input field, no spaces allowed.
         - Placeholder: "e.g. contactvm (no spaces)"
         - Helper text below field: "Called in the Notes field with /templatename" (Auto-generates from input)
      - **Template Text** (Required): Textarea, resizable, Rich Text.
         - Placeholder: "Enter template text here..."
         - Reference guide below textarea: "[DATE] → today's date [TIME] → current time [NAME] → selected client [ANY LABEL] → editable tab-stop field"
      - **Template Tags**: List of tags that are selected for the template. Shown as blue pills. List of tags updated from Tags tab. 

#### 4. Enforce template governance rules
   - Trigger uniqueness: When saving, validate that no other template shares the same trigger name
     - Error message: "A template with the trigger `/[name]` already exists"
   - Soft-delete: When "Delete" clicked on a template with usage count > 0, display confirmation: "This template has been used in existing notes and cannot be permanently deleted. Archive instead?"
     - Archive sets `is_active: false` and removes template from end-user `/` menu
   - Templates with usage count = 0 may be hard-deleted with confirmation: "Delete this template? This action cannot be undone."
   - Save validation: Template Text must contain at least one bracketed placeholder (e.g., `[NAME]`, `[NOTES]`) and non-empty text
     - Error message: "Template must contain at least one placeholder (e.g., [NAME])"

**Acceptance Criteria:**

| ID | Acceptance Criteria |
|----|---------------------|
| AC-1.1 | Verify Note Templates screen accessible from Super Admin > Settings > System Settings with correct header and navigation buttons |
| AC-1.2 | Test template list table displays Template Name (with `/` prefix), Template Text preview, Template Tags as pills, and Edit/Delete actions |
| AC-1.3 | Verify empty state displays when no templates configured with correct messaging and prominent Add button |
| AC-1.4 | Test Create Template form displays all fields with correct placeholders, token reference guide, and tag multi-select |
| AC-1.5 | Verify Edit Template form pre-populates all fields including tags as removable pills |
| AC-1.6 | Test trigger uniqueness validation prevents duplicate template names with correct error message |
| AC-1.7 | Verify soft-delete enforced for templates with usage count > 0 and hard-delete available for unused templates |
| AC-1.8 | Test Save validation rejects templates without at least one placeholder and displays error message |

---

### Story 2: Template Engine Behavior - UI

*Location(s): Any Notes text field across Helios*

**Requirements:**

- Currently the note taking experience is this:
   
   [Legacy Note Process]
- With templates being triggered by typing in the desired field like this:

   [Legacy Templates Triggering]
- **UPDATE** existing application to use "/" as the trigger
   - Typing "/" in a note field will now trigger the list of templates in the system to display the slash command popover

   [New / command popover]

1. Detect slash trigger in Notes fields
   - When cursor is active in any Notes text field and user types `/`, display floating popover menu immediately below cursor position
   - Trigger recognition: leading `/` at start of a new line or after a space
   - Popover appears within <100ms of `/` keystroke (no perceptible typing lag)

2. Display template selection popover
   - Popover displays list of all active templates
   - Each row shows:
     - Template trigger name in bold (e.g., **/contactvm**)
     - Template text preview in gray, truncated with "..." if exceeds row width
   - First row highlighted by default (or most recently used template, if tracked)
   - Popover follows cursor position (context-aware placement)

3. Enable real-time fuzzy filtering
   - As user types additional characters after `/` (e.g., `/fol`), popover filters in real-time
   - Matching applies fuzzy search against template trigger names and labels
   - When no templates match filter: display "No templates found" in popover

4. Enable keyboard and mouse selection
   - Arrow Up/Down: Navigate through template list, highlight moves accordingly
   - Enter or Tab: Select highlighted template ("commit")
   - Mouse click: Select clicked template
   - Esc: Dismiss popover without inserting text
   - Deleting the `/` character: Dismiss popover

5. Inject template on selection
   - On commit, delete trigger text (the `/` and all typed filter characters) from the Notes field
   - Replace with the full template body
   - Auto-populate reserved tokens at moment of insertion:
     - `[DATE]` → current date in user's locale format (e.g., 03/13/2026), rendered in blue text
     - `[TIME]` → current time in user's locale format (e.g., 12:30 PM), rendered in blue text
     - `[NAME]` → selected client name from current record context, rendered in blue text

6. Enable Variable Mode for remaining placeholders
   - Custom placeholders (e.g., `[NOTES]`, `[TOPIC]`, `[REASON]`) render as visually distinct tab-stop fields:
     - Editable placeholders: orange/highlighted text (e.g., `NAME` when client not auto-resolved)
     - Unfilled placeholders: bordered box with placeholder label text
   - Cursor automatically trapped on first placeholder
   - Tab: Advance to next placeholder
   - Shift + Tab: Return to previous placeholder
   - Typing replaces placeholder content with user input

7. Enable Variable Mode exit behavior
   - When final placeholder is filled and user presses Tab, Variable Mode ends
   - Cursor returns to standard free-form typing position after template content
   - Clicking outside template block also exits Variable Mode
   - Display helper text below field during Variable Mode: "Tab between fields · Esc to edit as plain text"

8. Display character count
   - Show remaining character count below Notes field (e.g., "18911 characters left")
   - Character count updates in real-time as template content and user input populate

**Acceptance Criteria:**

| ID | Acceptance Criteria |
|----|---------------------|
| AC-2.1 | Verify `/` trigger displays floating popover within <100ms with template list below cursor |
| AC-2.2 | Test each popover row displays trigger name in bold and template text preview in gray |
| AC-2.3 | Verify real-time fuzzy filtering narrows template list as user types after `/` |
| AC-2.4 | Test keyboard navigation (Arrow Up/Down, Enter/Tab to select, Esc to dismiss) and mouse click selection |
| AC-2.5 | Verify template injection replaces trigger text and auto-populates `[DATE]`, `[TIME]`, `[NAME]` tokens in blue text |
| AC-2.6 | Test Variable Mode traps cursor on first placeholder with Tab/Shift+Tab navigation between placeholders |
| AC-2.7 | Verify Variable Mode exits on final Tab or click outside, returning cursor to standard typing |
| AC-2.8 | Test character count displays below field and updates in real-time |

---

### Story 3: Note Template Tag Configuration - UI

*Location(s): Super Admin > Settings > System Settings > Note Template Tags*

**Requirements:**

1. Add Note Template Tags screen under System Settings
   - New screen accessible as sibling to Note Templates in Super Admin > Settings > System Settings navigation
   - Screen header: "Settings > Note Template Tags"
   - Display "+ Add Tag" button (blue, primary) and "< Back to Settings" button (gray, secondary) above tag list

2. Display tag list
   - Display all configured tags
   - Each tag entry shows: Tag Name, Actions (Edit / Delete)
   - Out of the box tags: "Internal", "External", "Provider", "Member / Patient", "Care Team", "Third Party", "Authorization", "Appeal", "Grievance", "Billing", "Scheduling", "Referral"

3. Add tag management operations
   - Create: "+ Add Tag" button opens inline form or modal with Tag Name text input (required, unique)
     - Error message on duplicate: "A tag with this name already exists"
   - Edit: "Edit" button enables inline editing of tag name
     - Renaming a tag propagates to all templates and notes referencing that tag
   - Delete: "Delete" button with confirmation
     - When tag is in use by one or more templates: "This tag is assigned to [N] templates. Removing it will unassign the tag from those templates. Continue?"
     - When tag is not in use: "Delete this tag? This action cannot be undone."

4. Display empty state when no tags configured
   - Message: "No template tags configured"
   - Subtext: "Create tags to categorize templates and enable tag-based access controls"
   - "+ Add Tag" button displayed prominently

5. Populate tag vocabulary downstream
   - Tags created here appear as selectable options in the Template Configuration screen (Story 1) Template Tags multi-select dropdown
   - Tags assigned to templates flow through to the note component when templates are invoked (Story 2)
   - Tags available as criteria values in the RBAC Criteria Builder (Story 4)

**Acceptance Criteria:**

| ID | Acceptance Criteria |
|----|---------------------|
| AC-3.1 | Verify Note Template Tags screen accessible from Super Admin > Settings > System Settings with correct header |
| AC-3.2 | Test tag list displays all configured tags with Edit and Delete actions |
| AC-3.3 | Verify Create tag enforces unique name validation with correct error message |
| AC-3.4 | Test Edit tag renames propagate to all templates and notes referencing that tag |
| AC-3.5 | Verify Delete tag shows correct confirmation message based on whether tag is in use |
| AC-3.6 | Test empty state displays when no tags configured with correct messaging |
| AC-3.7 | Verify tags created here appear in Template Configuration multi-select, note component, and RBAC Criteria Builder |

---