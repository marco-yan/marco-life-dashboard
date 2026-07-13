# Marco Life Dashboard

A single-file personal planning dashboard for managing weekly priorities, daily tasks, career progress, a job-search proof portfolio, training, nutrition, and the July–December 2026 roadmap.

The entire application remains in `index.html`. It is designed to work as a static website with no build step or package manager.

## Current experience

### This week

- Weekly progress for checklist completion, applications, outreach, training, and nutrition.
- Career, training, target-company, and networking priorities shown at a glance.
- The current portfolio phase, weekly deliverable, due date, and progress shown directly below the focus strip.
- Seven-day picker with a generated daily schedule and task list.
- Schedule editor opened through the pencil button.
- Daily tasks can be checked off, reordered, hidden for one day, restored, or supplemented with custom tasks.
- Drag-and-drop works with a mouse or touch; keyboard users can move tasks with the arrow keys.
- Task order, hidden tasks, and completion state persist across devices.
- From September 1 through November 22, portfolio sessions appear as normal Monday, Wednesday, Saturday, and Sunday tasks and keep the same reorder, hide, completion, and sync behavior.

### Three-person networking triangle

Networking is part of weekly planning rather than a separate static contact table.

Each week has three planned contacts:

1. **Monday — Peer or recent hire**
2. **Wednesday — Manager or senior team member**
3. **Friday — Recruiter or connector**

The weekly networking cards show:

- Contact name, title, and company.
- The best type of question to ask.
- A direct LinkedIn profile button.
- Shared completion status with the corresponding daily task.

The default roster contains 29 curated contacts across the Insurance, Infrastructure, Healthcare Analytics, and Banking lanes. Contact data is stored in:

- `state.careerContent.networkingContacts`
- `state.careerContent.weeklyNetworking`

Changing a weekly contact in the Roadmap immediately updates both the weekly networking card and that day's to-do item.

### Roadmap

- Covers every week from July through December 2026.
- Career and Training text can be edited directly by clicking the text.
- Weekly networking contacts can be changed with the Peer, Team Lead, and Connector selectors.
- September–December rows show the linked portfolio deliverable, project progress, and a shortcut to the full plan.
- Custom weekly text is stored in `state.careerContent.weeklyFocus`.
- Hardcoded `weekFocus` values remain as fallbacks when no custom text exists.

### Job-search proof portfolio

The Portfolio tab tracks four recruiter-facing deliverables and the final response-led refinement sprint. Each card shows the target lane, tool stack, date window, estimated effort, current status, progress, next milestone, and a measurable checklist.

#### Project 1 — Commercial Underwriting Pricing & Portfolio Workbench

- **Lane:** Insurance / Underwriting
- **Window:** September 1–10 (10–14 hours)
- **Tools:** Excel, Power Query, Power Pivot, PivotTables, LET, XLOOKUP, Solver, and data tables
- **Proof:** Claims import, frequency × severity pricing, expenses and risk load, underwriting decisions, portfolio dashboard, scenario comparison, and Solver optimization

#### Project 2 — Project Controls & Risk Forecasting Command Center

- **Lane:** Infrastructure / Project Controls
- **Window:** September 15–October 4 (14–18 hours)
- **Tools:** Excel, Power Query, Python, pandas, NumPy, Power BI, and DAX
- **Proof:** Eight work packages, twelve periods, earned-value measures, P50/P80 simulation, risk heatmap, Power BI reporting, and an executive narrative

#### Project 3 — Pharma Commercial Effectiveness & Channel Optimization

- **Lane:** Healthcare Commercial Analytics
- **Window:** October 5–25 (18–24 hours)
- **Tools:** SQL, Python/pandas, Excel, Power BI, and DAX
- **Proof:** Relational commercial data, joins/CTEs/window functions, control-vs-exposed simulation, confidence intervals, ROI, and budget-allocation recommendations

#### Project 4 — Shared Public Portfolio Site

- **Lane:** Shared proof layer
- **Window:** September 11–14 for the first public shell; October 26–November 8 for the complete site (6–10 hours total)
- **Tools:** HTML, CSS, basic JavaScript, Git/GitHub, Power BI embeds, downloads, and short walkthrough videos
- **Proof:** An early live Insurance case study for September outreach, followed by three concise case studies with working artifacts, architecture context, synthetic-data disclosures, mobile QA, and fallback media where live embedding is unavailable

#### Refinement and freeze

- **November 9–22:** Improve only the project receiving the strongest response, tighten documentation and résumé proof, run final QA, and tag the releases.
- **After November 22:** Freeze development and use the finished work for applications and interviews.

The protected weekly build rhythm is 1 hour Monday, 1.5 hours Wednesday, 3 hours Saturday, and 1.5 hours Sunday. Monday's live applications and Wednesday's networking action stay first; the portfolio uses the remainder of those existing focus blocks. Saturday deep work ends at 6:30 PM, Sunday is limited to QA/documentation, and Friday remains open. Milestone completion is stored in `state.careerContent.portfolioProgress.completedMilestones`.

The release order is intentionally tied to the job-search funnel:

1. Ship the Insurance MVP and place it on a live portfolio shell by September 14.
2. Release Project Controls before the early-October recruiter and interview push.
3. Release Pharma by October 25 while using current WebHealth experience as the healthcare bridge.
4. Finish the complete public presentation layer by November 8.
5. Refine only the proof point receiving meaningful response, then freeze development.

Live application deadlines, interview preparation, warm follow-ups, and the three-person networking triangle take priority over portfolio polish.

### Career Tracker

- Editable target companies, search lanes, and strategic plan.
- Application log with company, role, status, date, and notes.
- Networking log with contact, company, status, date, and notes.
- Tracker content and log entries continue to sync automatically.

### Notes and settings

- Notes save automatically.
- Settings control job-search quotas, race preference, bike and swim start dates, and nutrition targets.
- Password protection, locking, sync status, manual cloud sync, and cloud reload remain available.

## Saving and synchronization

The dashboard saves to local storage immediately and uses JSONBin for cross-device synchronization.

The sync payload includes:

- Settings
- Completed tasks
- Custom task order
- Hidden daily tasks
- Custom tasks
- Schedule overrides
- Notes
- Career applications
- Networking-log entries
- Editable career content
- Weekly Roadmap edits
- Networking roster and weekly contact selections
- Portfolio milestone completion

When adding future features, store user-editable information in `state` or `state.careerContent` and confirm that it remains included in the JSONBin payload.

## Mobile support

The dashboard is designed for regular iPhone use:

- Networking actions and selectors use touch targets of at least 44px.
- Task drag handles and Hide controls are touch-friendly.
- Weekly networking cards stack vertically on smaller screens.
- Portfolio projects, timeline phases, and weekly focus content collapse to a single-column layout.
- Portfolio milestone rows and all portfolio actions use touch-friendly controls of at least 44px.
- Roadmap fields and contact selectors switch to a single-column layout.
- Horizontal day navigation remains available without compressing the labels.

## Repository structure

```text
marco-life-dashboard/
├── index.html   # Complete application: HTML, CSS, data, and JavaScript
└── README.md    # Project guide and update history
```

There is intentionally no separate CSS file, JavaScript bundle, framework, or build process.

## Safe editing rules

Future updates should preserve these project constraints:

1. Keep the application as one `index.html` file.
2. Keep the existing palette:
   - Background: `#F7F6F0`
   - Text: `#171A18`
   - Primary: `#214E3B`
   - Secondary: `#82906A`
   - Highlight: `#C5A46D`
3. Preserve password protection and JSONBin synchronization.
4. Preserve schedule sorting and the pencil-button schedule editor.
5. Preserve task completion, touch reordering, daily hiding, and restoration.
6. Preserve Roadmap editing, Career Tracker logs, Notes, and Settings.
7. Store new persistent data inside `state`.
8. Keep interactive mobile controls at least 44px tall.
9. Avoid unnecessary third-party dependencies or abstractions.

## Update history

### v2.5 — 2026-07-13

- Accelerated the portfolio plan so all three lane-specific MVPs ship by October 25 and development freezes November 22.
- Moved the thin public portfolio shell to September 11–14 so the Insurance case study can support active outreach immediately.
- Added a later October 26–November 8 site-polish phase for the complete case-study hub, embeds, downloads, and walkthroughs.
- Expanded the build cadence from 6 to 7 hours while keeping applications and networking first in the existing Monday and Wednesday focus blocks.
- Added the Monday build sprint and placed the Saturday deep-work and Sunday QA blocks directly in the generated schedule.
- Updated every project milestone, weekly Roadmap focus, due date, current-phase label, and post-freeze message to match the faster release plan.

### v2.4 — 2026-07-13

- Added a dedicated Portfolio tab for the three lane-specific projects, shared public site, and final refinement sprint.
- Added 29 measurable milestones with per-project and overall progress, due dates, status, tools, and next-action guidance.
- Added the September 1–December 6 build timeline and post-freeze interview mode.
- Linked portfolio deliverables into the This week focus area and September–December Roadmap rows.
- Added Wednesday, Saturday, and Sunday portfolio work sessions to the existing reorderable and hideable daily task system.
- Stored milestone completion in `state.careerContent.portfolioProgress` for JSONBin synchronization.
- Added responsive portfolio cards, timeline, progress bars, and 44px mobile controls.

### v2.3 — 2026-07-13

- Integrated the three-person networking triangle into weekly planning.
- Added a 29-person curated contact roster and plans for all 26 Roadmap weeks.
- Added visible weekly contact cards, LinkedIn links, outreach questions, and completion controls.
- Added named networking tasks on Monday, Wednesday, and Friday.
- Linked weekly-card completion to the corresponding daily checkbox.
- Added Roadmap contact selectors and JSONBin-backed networking state.
- Preserved outreach metrics by counting named networking actions toward the existing weekly quota.
- Added responsive layouts and 44px networking controls for iPhone use.

### v2.2 — 2026-07-13

- Added mouse and touch drag-and-drop task reordering.
- Added keyboard reordering through the task handle.
- Added one-tap Hide controls and per-day Restore hidden behavior.
- Stored task order and hidden-task state in the JSONBin payload.
- Increased task-row spacing and mobile touch-target sizes.

### v2.1 — 2026-07-13

- Added direct editing for weekly Career and Training Roadmap entries.
- Linked Roadmap edits to the This week focus strip.
- Stored edits in `state.careerContent.weeklyFocus` with hardcoded fallbacks.
- Improved mobile layout, navigation, accessibility, empty states, schedule sorting, sync queuing, and safe text rendering.

### v2.0 — 2026-07-10

- Moved cross-device data synchronization to JSONBin.
- Added automatic syncing, Sync Now, Load from Cloud, and status feedback.
- Removed the need for users to provide GitHub tokens.

### v1.5 — 2026-07-10

- Made target companies, search lanes, and the strategic career plan editable in the Career Tracker.
- Added persistence for editable career content.

### v1.2 — 2026-07-10

- Added the per-day schedule editor.
- Added, removed, sorted, and reset schedule blocks.
- Stored schedule overrides independently for each date.

### v1.1 — 2026-07-10

- Introduced the current earth-tone palette and password screen.
- Improved cards, typography, day navigation, the focus strip, and checkboxes.

### v1.0 — 2026-07-10

- Launched the weekly dashboard, daily schedule, checklist, progress stats, Roadmap, settings, and responsive layout.
