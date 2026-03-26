# Enhanced Client Search (O3)

| Field | Value |
|-------|-------|
| **Product Owner** | @A.J. Meeks |
| **Helios Product** | Helios Core |
| **Helios Domains** | All Domains |
| **Product Theme** | Client Identification |
| **Related Tickets** | TBD |

---

## Problem Statement

The existing client select dropdown searches and displays only a member's name, making it difficult for users to locate the correct member when two or more members share the same or similar names. There is no way to search by alternative identifiers such as Member ID, date of birth, or plan information, forcing users to rely solely on name recognition. This creates friction and the risk of selecting the wrong member — a significant concern in a healthcare context where acting on the wrong member record can have clinical and compliance consequences.

---

## Objective

Enhance the existing client select dropdown component across all locations in Helios to support multi-identifier search, returning results matched against name, Member ID, date of birth, SSN (last 4), plan ID, and phone number. Result rows will display a richer set of identifying fields so that users can confidently distinguish between members with similar or identical names.

---

## Designs & User Flows

*Screenshots TBD*

| Screen | File |
|--------|------|
| Enhanced client search dropdown — empty/default state | TBD |
| Enhanced client search dropdown — results with enriched row display | TBD |
| Enhanced client search dropdown — no results state | TBD |

---

## Release Notes

**Enhanced Client Search**

**Feature Summary:** Multi-identifier search for the client select dropdown, enabling users to search by name, Member ID, date of birth, SSN (last 4), plan ID, and phone number, with enriched result rows to distinguish members with similar or identical names.

**Key Capabilities:**
1. Search for members by name, Member ID, date of birth, SSN (last 4), plan ID, or phone number from any client select dropdown in Helios
2. Result rows display Name, Member ID, and Date of Birth (at minimum) to enable confident member identification
3. Backend search queries the full member database for accurate, real-time matching

**User Impact:** Users can quickly and accurately locate the correct member regardless of the identifier they have on hand. Members with identical or similar names are easily distinguishable via the enriched result row display, reducing the risk of selecting the wrong member record.

---

## User Stories & Requirements

### Story 1: Multi-Identifier Client Search — Backend

*Location(s): Member search API endpoint consumed by all client select dropdowns*

**Requirements:**

1. Support multi-field search query
   - API endpoint accepts a single search string and matches against the following member fields:
     - Full name (first, last, or full)
     - Member ID
     - Date of birth (accepts common formats: MM/DD/YYYY, MMDDYYYY, YYYY-MM-DD)
     - SSN last 4 digits
     - Plan ID
     - Phone number (formatted and unformatted)
   - Search is case-insensitive and trims leading/trailing whitespace

2. Return required display fields
   - Each result includes at minimum: member full name, Member ID, date of birth
   - Additional member fields returned in the response to support future display enhancements without requiring a new API change

3. Scope results to authorized members
   - Search results respect existing user-level and role-level access controls
   - Users only see members they are authorized to view

4. Enforce result limits and performance
   - Return a maximum of [TBD — confirm with engineering, e.g., 25 or 50] results per query
   - Target response time: under 500ms for typical queries against production data volume

**Acceptance Criteria:**

| ID | Acceptance Criteria |
|----|---------------------|
| AC-2.1 | Verify searching by full name returns correct matching members |
| AC-2.2 | Verify searching by Member ID returns the correct member |
| AC-2.3 | Verify searching by date of birth (multiple input formats) returns matching members |
| AC-2.4 | Verify searching by SSN last 4 returns matching members |
| AC-2.5 | Verify searching by Plan ID returns matching members |
| AC-2.6 | Verify searching by phone number (formatted and unformatted) returns matching members |
| AC-2.7 | Test search results are scoped to members the user is authorized to view |
| AC-2.8 | Verify result set is capped at [TBD] results per query |
| AC-2.9 | Test API response time meets <500ms target under production-representative data volume |

---

### Story 2: Multi-Identifier Client Search — UI

*Location(s): All client select dropdown instances across Helios*

**Requirements:**

1. Update search input placeholder text
   - Change placeholder to: "Search by name, Member ID, DOB, or other identifiers"
   - Communicates to users that multi-field search is supported

2. Display enriched result rows
   - Each result row in the dropdown displays (at minimum):
     - Member full name (bold)
     - Member ID
     - Date of birth (formatted per user locale, e.g., 03/13/1985)
   - Fields displayed on a single row or as a stacked sub-row, visually distinct from one another
   - Layout must remain legible when two or more members share the same full name

3. Trigger backend search on input
   - Search fires on each keystroke after a minimum of 2 characters entered, debounced at ~300ms
   - Results render in the dropdown in real-time as the search response is returned

4. Display loading state during search
   - While the backend search is in progress, show a loading indicator (spinner or skeleton rows) inside the dropdown

5. Display no-results state
   - When no members match the search query, display: "No members found for '[query]'"

6. Preserve existing selection behavior
   - Selecting a result from the dropdown retains current selection behavior — no change to downstream behavior post-selection

**Acceptance Criteria:**

| ID | Acceptance Criteria |
|----|---------------------|
| AC-1.1 | Verify search input placeholder is updated across all client select dropdown instances |
| AC-1.2 | Test result rows display member name (bold), Member ID, and date of birth in a visually distinct layout |
| AC-1.3 | Verify two members with the same full name are distinguishable by the enriched row display fields |
| AC-1.4 | Test search triggers after 2 characters with ~300ms debounce and results render in real-time |
| AC-1.5 | Verify loading indicator displays while the backend search is in flight |
| AC-1.6 | Test no-results state displays correct message when no members match the query |
| AC-1.7 | Verify selecting a result retains existing downstream selection behavior |

---

## Nice to Have

1. **Match indicator on result rows:** Display a subtle label on each result row indicating which field matched the query (e.g., "Matched on Member ID") to further reduce ambiguity during lookup.
2. **Additional identifier fields:** Extend searchable and displayed fields (e.g., address, group number, subscriber ID) as additional identifiers are confirmed during implementation discovery.
3. **Admin Configurable Search Criteria:** Create an admin page so the administrator/users of the system can define what to search for. This allows our clients to be able to focus on the search information they want to use from the OOB list we provide (Example: One client would like to search off of last 4 of SSN, and second client does not want to be able to search off last four of SSN)

---

## Feature Flag Requirements
**Is a feature flag required?** NO