# Marco Life Dashboard

A single-file personal planning dashboard for managing weekly priorities, daily tasks, career progress, training, nutrition, and the July–December 2026 roadmap.

The entire application remains in `index.html`. It is designed to work as a static website with no build step or package manager.

## Current experience

### This week

- Weekly progress for checklist completion, applications, outreach, training, and nutrition.
- Career, training, target-company, and networking priorities shown at a glance.
- Seven-day picker with a generated daily schedule and task list.
- Schedule editor opened through the pencil button.
- Daily tasks can be checked off, reordered, hidden for one day, restored, or supplemented with custom tasks.
- Drag-and-drop works with a mouse or touch; keyboard users can move tasks with the arrow keys.
- Task order, hidden tasks, and completion state persist across devices.

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
- Custom weekly text is stored in `state.careerContent.weeklyFocus`.
- Hardcoded `weekFocus` values remain as fallbacks when no custom text exists.

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

When adding future features, store user-editable information in `state` or `state.careerContent` and confirm that it remains included in the JSONBin payload.

## Mobile support

The dashboard is designed for regular iPhone use:

- Networking actions and selectors use touch targets of at least 44px.
- Task drag handles and Hide controls are touch-friendly.
- Weekly networking cards stack vertically on smaller screens.
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
