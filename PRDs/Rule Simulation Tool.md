# Rule Simulation Tool

| Field | Value |
|-------|-------|
| **Product Owner** | @A.J. Meeks |
| **Helios Product** | Helios Hub |
| **Helios Domains** | [Comma-separated domains] |
| **Product Theme** | Rule Simulation Tool |
| **Related Tickets** | [Links or N/A] |

---

## Problem Statement

Administrators, Business Analysts, and Systems Analysts configuring rules in Helios Hub have no mechanism to test rule combinations or visualize impacted record volumes before activating a configuration. Without simulation, there is no way to assess whether a rule will have the intended impact, fire too broadly, or produce no effect at all — creating risk of unintended workflow disruption, misconfigured automation, and configurations that require costly rollback after the fact.

---

## Objective

Add a Rule Simulation Tool to Helios Hub that enables users to simulate rule firing sequences against live data before implementation, surfacing a flow diagram visualization of each rule step, a count of affected records, and a paginated list of impacted records. Accessible both as a standalone testing tool within Settings and as a pre-save prompt inline during rule creation, giving users confidence that configurations will behave as intended before they go live.

---

## Designs & User Flows

*Screenshots stored in `~/Documents/Claude/assets/rule-simulation-tool/`*

| Screen | File |
|--------|------|
| Standalone simulation tool — pre-run state | `~/Documents/Claude/assets/rule-simulation-tool/simulation-tool-empty.png` |
| Flow diagram with results panel — simulation scanning (live counter) | `~/Documents/Claude/assets/rule-simulation-tool/flow-diagram-scanning.png` |
| Flow diagram with results panel — simulation in progress (node color coding) | `~/Documents/Claude/assets/rule-simulation-tool/flow-diagram-running.png` |
| Flow diagram with results panel — simulation complete | `~/Documents/Claude/assets/rule-simulation-tool/flow-diagram-complete.png` |
| Impacted records panel — count and paginated list | `~/Documents/Claude/assets/rule-simulation-tool/impacted-records.png` |
| Inline simulation prompt in rule creation flow | `~/Documents/Claude/assets/rule-simulation-tool/inline-simulation-prompt.png` |

---

## Release Notes

> Click to expand for full release details

**Rule Simulation Tool**

**Feature Summary:** Enables Super Admins, Business Analysts, and Systems Analysts to simulate rule firing sequences against live data before implementation — surfacing a real-time flow diagram, a total affected record count, and a paginated list of impacted records. Available both as a standalone tool in Settings and as a pre-save step in the rule creation flow.

**Key Capabilities:**
1. Run simulations against any existing rule or combination of rules from a dedicated simulation screen within Settings
2. Visualize rule execution as a color-coded flow diagram in a non-blocking side panel — diagram stays fully visible as results load alongside it
3. Display a live scanning counter as the simulation runs ("1,234 records evaluated · 87 matched"), resolving to a final total count on completion
4. Browse impacted records in a paginated list (10 records per page) with a direct link to each record
5. Prompt users to simulate a rule as a pre-save step at the end of the rule creation flow before confirming

**User Impact:** Eliminates the guesswork of rule configuration. Admins and analysts can validate that rules fire on the right records, at the right scope, before any workflow is affected — and present simulation results visually to non-technical stakeholders without requiring a live deployment.

---

## User Stories & Requirements

### Story 1: Rule Simulation Tool - Standalone UI

*Location(s): Helios Hub > Settings > [Rules] > Rule Simulation*

**Requirements:**

1. Add a Rule Simulation screen to Settings
   - New screen accessible from the Settings area alongside existing rule configuration screens
   - Screen header: "Rule Simulation"
   - Display "< Back to Settings" button (gray, secondary) and "Run Simulation" button (blue, primary) in the header area

2. Enable rule selection for simulation
   - Display a "Select Rule(s)" multi-select dropdown above the simulation panel
   - Populate dropdown with all active configured rules, grouped by rule type
   - Allow selection of one or multiple rules to simulate in combination
   - Display selected rules as removable pills beneath the dropdown

3. Display simulation results in a non-blocking side panel
   - On "Run Simulation", open a results panel to the right of the flow diagram
   - Flow diagram remains fully visible and unobscured while the results panel is open
   - Results panel contains: live scanning counter (during run), final count, and impacted records list (after run completes)
   - Panel can be collapsed via a chevron toggle to restore full-width diagram view after review

4. Display simulation flow diagram
   - Render a flow diagram representing each configured step in the selected rule(s) in the main canvas area
   - Each step in the rule renders as a labeled node in the diagram, connected by directional arrows indicating sequence
   - Color-code each node in real time as the simulation runs:
     - Yellow — step is currently processing
     - Green — data successfully flowed through this step
     - Red — step produced an error or no records matched
   - When simulation completes, all nodes display their final state (Green or Red); no nodes remain Yellow
   - Flow diagram is static after simulation completes — suitable for presenting to non-technical stakeholders without further interaction

5. Display live scanning counter during simulation
   - While the simulation is running, display a real-time counter in the results panel: "[N] records evaluated · [N] matched"
   - Both values increment in real time as the simulation scans data
   - Counter provides immediate feedback that the simulation is active, particularly useful for large datasets
   - When simulation completes, counter resolves to the final summary state (requirement 6)

6. Display affected record count on completion
   - When simulation completes, replace the live counter with the final count: "You will affect [N] records"
   - Count reflects the full result set before pagination
   - When no records are affected: display "No records would be affected by this rule"

7. Display impacted records list
   - Below the count, display a paginated table of impacted records
   - Table columns: Record ID, Record Name, Entity Type, Status, View Record
   - "View Record" column displays a text link that navigates to the full record
   - Pagination: 10 records per page
   - Display pagination controls: Previous / Next buttons and current page indicator (e.g., "Page 2 of 48")
   - When no records are affected: display empty state below count message; no table rendered

8. Display empty state before simulation is run
   - When no simulation has been run yet: display placeholder message in the results panel area: "Select a rule and run a simulation to see results"

**Acceptance Criteria:**

| ID | Acceptance Criteria |
|----|---------------------|
| AC-1.1 | Verify Rule Simulation screen is accessible from Settings alongside rule configuration with correct header and navigation |
| AC-1.2 | Test rule selection multi-select populates all active rules grouped by type and displays selections as removable pills |
| AC-1.3 | Verify "Run Simulation" opens the results panel to the right of the flow diagram without obscuring the canvas |
| AC-1.4 | Test results panel can be collapsed via chevron toggle to restore full-width diagram view |
| AC-1.5 | Verify flow diagram renders with correctly labeled nodes and directional arrows on simulation start |
| AC-1.6 | Test flow diagram nodes color-code in real time: Yellow during processing, Green on pass, Red on error |
| AC-1.7 | Verify all nodes resolve to Green or Red on simulation completion; no nodes remain Yellow |
| AC-1.8 | Test live scanning counter displays and increments in real time during simulation: "[N] records evaluated · [N] matched" |
| AC-1.9 | Verify live counter resolves to final "You will affect [N] records" summary on simulation completion |
| AC-1.10 | Verify "No records would be affected by this rule" displays when result count is zero |
| AC-1.11 | Test impacted records table renders with correct columns: Record ID, Record Name, Entity Type, Status, View Record |
| AC-1.12 | Verify "View Record" link navigates to the correct full record |
| AC-1.13 | Test pagination displays 10 records per page with Previous/Next controls and correct page indicator |
| AC-1.14 | Verify empty state displays in results panel before any simulation is run |

---

### Story 2: Inline Simulation Prompt - Rule Creation Flow

*Location(s): Helios Hub > Settings > [Rules] > Create Rule — final step before save*

**Requirements:**

1. Add a simulation prompt as the final step of the rule creation flow
   - After a user completes all required rule configuration fields and before the final save confirmation, display a simulation step
   - Step header: "Test Your Rule Before Saving"
   - Display "Run Simulation" button (blue, primary) and "Skip & Save" button (gray, secondary)

2. Display simulation results inline within the creation flow
   - When "Run Simulation" is selected, render the flow diagram, affected record count, and impacted records list inline within the creation step (same behavior and layout as Story 1)
   - After simulation completes, display "Save Rule" button (green, primary) and "Back to Edit" button (gray, secondary) alongside results
   - "Save Rule" confirms and saves the rule; "Back to Edit" returns the user to the rule configuration step without losing their input

3. Enable skip behavior
   - When "Skip & Save" is selected, save the rule immediately without running a simulation
   - No simulation data is required to save a rule

**Acceptance Criteria:**

| ID | Acceptance Criteria |
|----|---------------------|
| AC-2.1 | Verify simulation prompt appears as the final step of the rule creation flow before save confirmation |
| AC-2.2 | Test "Run Simulation" renders flow diagram, affected record count, and paginated impacted records list inline |
| AC-2.3 | Verify "Save Rule" button appears after simulation completes and saves the rule on click |
| AC-2.4 | Test "Back to Edit" returns user to rule configuration with all previously entered values intact |
| AC-2.5 | Verify "Skip & Save" saves the rule immediately without requiring simulation |

---

## Nice to Have

1. **Simulation history** — Retain a log of past simulation runs per rule, showing who ran the simulation, when, and what the result count was at that time.
   - Useful for tracking configuration drift over time

2. **Export simulation results** — Enable export of the impacted records list to CSV from the simulation results panel.
   - "Export CSV" button above paginated table; exports Record ID, Record Name, Entity Type, Status

3. **Side-by-side rule comparison** — Run two rule configurations simultaneously and display results side by side to compare impact before deciding which to save.

4. **Scheduled simulation** — Allow users to schedule a simulation to re-run on a recurring basis and alert when affected record count changes beyond a configured threshold.

---

## Feature Flag Requirements

> Click to expand feature flag details

**Is a feature flag required?** YES

- **Flag Name:** RULE_SIMULATION_TOOL
- **Flag Purpose:** Controls access to the Rule Simulation Tool across both the standalone Settings screen and the inline rule creation prompt. Enables staged rollout to Super Admins, Business Analysts, and Systems Analysts before broader availability.
- **Flag Owner:** @A.J. Meeks
