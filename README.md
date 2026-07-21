# Marco Life Dashboard

A single-file personal planning dashboard for managing weekly priorities, an Eisenhower Matrix, daily tasks, dated journal entries, career progress, a job-search proof portfolio, training, nutrition, and the July–December 2026 roadmap.

The entire application remains in `index.html`. It is designed to work as a static website with no build step or package manager.

## Current experience

### Access and account roles

The login screen supports two dashboard accounts:

| Role | Username | Password | Access |
| --- | --- | --- | --- |
| Admin | `` | `` | Full editing, local saving, and JSONBin synchronization |
| Guest | `guest` | `123` | Read-only preview, with the Journal protected by its separate password |

The active role is kept in `sessionStorage`, so signing out or closing the browser session returns the viewer to the login screen. A role badge is always visible in the dashboard header, and guest sessions display a read-only banner. Journal access has its own session-only unlock flag and is cleared on sign-out or when the browser session closes.

Guest mode can browse the weekly plan, Priority Matrix, tasks, schedule, Roadmap, portfolio, Career Tracker, notes, and settings. The Journal remains locked until its separate password is entered, after which guests can browse entries in read-only mode. All mutation paths are also guarded in JavaScript: guests cannot complete, move, add, or delete Priority Matrix tasks; complete, reorder, hide, or add daily tasks; edit schedules or Roadmap content; change networking selections; complete portfolio milestones; edit Journal entries, career data, or notes; save settings; write to `localStorage`; or send updates to JSONBin. Guests may still load the latest cloud data for viewing.

> **Security note:** This is client-side access control for a static personal dashboard, not server-backed authentication. The journal password is stored as a SHA-256 digest rather than readable text, but the gate can still be bypassed by a technically advanced visitor because the journal data and access logic ultimately run in the browser. The password itself is intentionally not printed in this public README. Use server-side authentication and a data proxy if the dashboard ever contains information that requires strong confidentiality.

### This week

- Weekly progress for checklist completion, applications, outreach, training, and nutrition.
- Career, training, target-company, and networking priorities shown at a glance, with the job hunt organized around a **45% Nuclear Project Delivery / 30% Insurance / 25% Healthcare Analytics** allocation.
- The current portfolio phase, weekly deliverable, due date, and progress shown directly below the focus strip.
- Seven-day picker with a generated daily schedule and task list.
- Schedule editor opened through the pencil button.
- Daily tasks can be checked off, reordered, hidden for one day, restored, or supplemented with custom tasks.
- Drag-and-drop works with a mouse or touch; keyboard users can move tasks with the arrow keys.
- Task order, hidden tasks, and completion state persist across devices.
- Generated work tasks name concrete planning or close-out actions instead of vague daily productivity prompts.
- Applications are distributed across Monday, Thursday, and Sunday; Tuesday and Thursday lunch windows support lane-specific outreach or booked calls.
- Portfolio sessions run from July 20 through October 25 on Wednesday, Saturday morning, and Sunday. They keep the same reorder, hide, completion, and sync behavior as every other task.
- Friday after work and Saturday after 2:00 PM remain protected by the default schedule.

### Priority Matrix

The Priority Matrix tab adds a dedicated Eisenhower Matrix for work that needs prioritization before it becomes part of a specific day's plan.

- Four clear quadrants: **Do**, **Schedule**, **Delegate**, and **Delete**.
- Add tasks directly inside any quadrant.
- Mark tasks complete, permanently delete them, or move them with the native quadrant selector.
- Open and completed totals appear for the full matrix and each quadrant.
- Data is stored in `state.priorityMatrix.tasks`, saved locally, and included in the existing JSONBin payload for cross-device sync.
- Guests can view task placement and completion but all matrix controls remain read-only.
- The board is 2×2 on larger screens and stacks into one comfortable column on iPhone.


### Journal

The Journal tab provides a calm, date-based writing space for daily thoughts, ideas, and reflections.

- Opening the tab first shows a separate, mobile-friendly password gate; journal entry data is not populated into the interface before it is unlocked.
- A correct password unlocks the Journal once per browser session for either account role. A wrong password shows an inline error and leaves every entry blocked.
- Journal access is stored only in `sessionStorage` and is cleared by dashboard sign-out or when the browser session closes.
- Open today or select any earlier date with the date picker.
- Move one day at a time with the previous and next controls, or return directly to today.
- Browse all written dates from the **Past entries** list, with a short preview of each entry.
- Writing is stored in `state.journal.entries` by ISO date, such as `2026-07-17`.
- Entries save automatically after a short typing pause, persist locally, and use the existing JSONBin synchronization path.
- Erasing all text for a date removes that empty entry from the history list.
- Word count and save status remain visible without adding a manual Save button.
- Guests can browse synced entries only after unlocking the Journal, and the writing area remains read-only.
- On iPhone, the writing page and history stack vertically, with date controls and history rows retaining touch-friendly targets.

### Google Calendar

The selected day's Google Calendar events appear directly below the dashboard's editable daily schedule. The integration uses a small, owner-authorized Google Apps Script web app as a read-only feed rather than short-lived browser OAuth tokens.

- Events load for the selected dashboard week and update when the week changes.
- The open dashboard refreshes the calendar automatically every 10 minutes and after returning to a stale browser tab.
- Timed, all-day, overnight, and multi-day events are supported.
- Event title, time, location, and source calendar are rendered with the dashboard's existing palette and responsive layout.
- The **Open** button opens Google Calendar on the selected day; the adjacent refresh control provides an optional immediate retry.
- Calendar events are runtime-only and are never copied into local storage or JSONBin.
- Only the Apps Script deployment URL is stored in `state.settings.calendarFeedUrl`, so the connection follows the admin across devices through the existing JSONBin sync.
- Guests can view and refresh events but cannot change the feed URL or save calendar settings.

The Apps Script deployment executes as its owner, so it can continue reading the configured calendars without asking the dashboard user to renew a browser access token. The deployment remains active until the owner revokes its authorization, deletes it, or changes its access settings.

## Google Calendar setup

```javascript
const CALENDAR_IDS = [];
const MAX_RANGE_DAYS = 14;

function doGet(e) {
  const parameters = e && e.parameter ? e.parameter : {};
  const prefix = String(parameters.prefix || '');

  // Only accept the callback names generated by the dashboard.
  if (!/^__marcoCalendarCallback_[A-Za-z0-9_]+$/.test(prefix)) {
    return ContentService
      .createTextOutput('throw new Error("Invalid calendar callback.");')
      .setMimeType(ContentService.MimeType.JAVASCRIPT);
  }

  let payload;
  try {
    const startMillis = Number(parameters.start);
    const endMillis = Number(parameters.end);
    const maxRange = MAX_RANGE_DAYS * 24 * 60 * 60 * 1000;

    if (!Number.isFinite(startMillis) || !Number.isFinite(endMillis) ||
        endMillis <= startMillis || endMillis - startMillis > maxRange) {
      throw new Error('Invalid calendar range.');
    }

    const start = new Date(startMillis);
    const end = new Date(endMillis);
    const events = [];

    getCalendars_().forEach(function(calendar) {
      calendar.getEvents(start, end).forEach(function(event) {
        events.push({
          id: calendar.getId() + ':' + event.getId() + ':' + event.getStartTime().getTime(),
          title: event.getTitle() || 'Busy',
          start: event.getStartTime().toISOString(),
          end: event.getEndTime().toISOString(),
          allDay: event.isAllDayEvent(),
          location: event.getLocation() || '',
          calendar: calendar.getName() || ''
        });
      });
    });

    events.sort(function(a, b) {
      return a.start.localeCompare(b.start) || a.title.localeCompare(b.title);
    });

    payload = {
      ok: true,
      generatedAt: new Date().toISOString(),
      events: events
    };
  } catch (error) {
    console.error(error);
    payload = {
      ok: false,
      error: 'The Apps Script calendar feed failed. Check the Apps Script Executions page.',
      events: []
    };
  }

  return ContentService
    .createTextOutput(prefix + '(' + JSON.stringify(payload) + ');')
    .setMimeType(ContentService.MimeType.JAVASCRIPT);
}

function getCalendars_() {
  if (!CALENDAR_IDS.length) return [CalendarApp.getDefaultCalendar()];
  return CALENDAR_IDS
    .map(function(id) { return CalendarApp.getCalendarById(id); })
    .filter(function(calendar) { return Boolean(calendar); });
}

function authorizeCalendar() {
  console.log(getCalendars_().map(function(calendar) {
    return calendar.getName();
  }));
}
```

By default, `CALENDAR_IDS` is empty and the feed reads the account's primary calendar. To include several calendars, replace the empty array with their IDs, for example:

```javascript
const CALENDAR_IDS = [
  'your-email@gmail.com',
  'example@group.calendar.google.com'
];
```

Calendar IDs are available in Google Calendar under **Settings → Settings for my calendars → Integrate calendar → Calendar ID**.


### Privacy and deployment access

The deployed `/exec` URL behaves like a private feed link. Anyone who obtains it can request the limited event fields returned by the script, and the Guest account can see events because Guest mode intentionally previews the full dashboard. The supplied script excludes descriptions, attendees, conferencing details, and other sensitive event metadata. Do not add those fields unless that exposure is intentional.

If **Who has access: Anyone** is unavailable because of a Workspace administrator policy, use a personal Google account for this feed or move the integration behind a server-side authenticated proxy. A browser-only OAuth fallback is intentionally not used because Google access tokens are short-lived and must be renewed through a user-driven authorization flow.

### Three-person networking triangle

Networking is part of weekly planning rather than a separate static contact table.

Each week has three planned contacts:

1. **Monday — Peer or recent hire**
2. **Tuesday — Manager or senior team member**
3. **Thursday — Recruiter or connector**

The weekly networking cards show:

- Contact name, title, and company.
- The best type of question to ask.
- A direct LinkedIn profile button.
- Shared completion status with the corresponding daily task.

The default roster contains 29 curated contacts. Future weekly assignments follow the 45 / 30 / 25 Nuclear, Insurance, and Healthcare allocation; Banking contacts remain available in the roster but are not part of the default campaign. Contact data is stored in:

- `state.careerContent.networkingContacts`
- `state.careerContent.weeklyNetworking`

Changing a weekly contact in the Roadmap immediately updates both the weekly networking card and that day's to-do item.

### Roadmap

- Covers every week from July through December 2026.
- Career and Training text can be edited directly by clicking the text.
- Weekly networking contacts can be changed with the Peer, Team Lead, and Connector selectors.
- July 20–October 25 rows show the linked portfolio deliverable, project progress, and a shortcut to the full plan. Later rows show the development freeze and interview mode.
- Custom weekly text is stored in `state.careerContent.weeklyFocus`.
- Hardcoded `weekFocus` values remain as fallbacks when no custom text exists.

### Job-search proof portfolio

The Portfolio tab tracks four recruiter-facing deliverables and the final response-led refinement sprint. Each card shows the target lane, tool stack, date window, estimated effort, current status, progress, next milestone, and a measurable checklist.

#### Project 1 — Nuclear Project Controls & Risk Forecasting Command Center

- **Lane:** Nuclear Project Delivery / Project Controls
- **Window:** July 20–August 9, with a thin MVP by August 2 (14–18 hours)
- **Tools:** Primavera P6 or Microsoft Project, Excel, Power Query, Python, pandas, NumPy, Power BI, and DAX
- **Proof:** A mock nuclear-refurbishment WBS and logic-linked schedule, baseline and critical path, earned-value measures, P50/P80 risk simulation, risk heatmap, Power BI reporting, and an executive narrative

#### Project 2 — Contract Surety Underwriting & Bond Capacity Workbench

- **Lane:** Insurance / Contract Surety
- **Window:** August 10–23 (12–16 hours)
- **Tools:** Excel, Power Query, Power Pivot, PivotTables, LET, XLOOKUP, Solver, and data tables
- **Featured case:** A synthetic Canadian civil contractor requests performance and labour-and-material payment bonds for a C$30M spillway rehabilitation package within a hydroelectric dam refurbishment.
- **Proof:** Refreshable contractor financial statements and WIP data; adjusted working capital, tangible net worth, liquidity, leverage, backlog-to-equity, and remaining-capacity analysis; Capital / Capacity / Character assessment; recommended single and aggregate limits; approve / condition / refer / decline logic; an exposure dashboard; and a one-page underwriting memo.
- **Stress tests:** High-water delay, 10% material inflation, late owner payment, margin fade, a concurrent project win, and specialized-subcontractor failure. Solver tests the maximum additional job size that remains within the model's documented capacity constraints.
- **Scope note:** The hydro package is the featured case study, while the workbook remains reusable for road, bridge, transit, municipal, and other bonded construction work. All contractor data and thresholds are clearly labelled as synthetic educational assumptions.

#### Project 3 — Pharma Commercial Effectiveness & Channel Optimization

- **Lane:** Healthcare Commercial Analytics
- **Window:** August 31–September 27 (18–24 hours)
- **Tools:** SQL, Python/pandas, Excel, Power BI, and DAX
- **Proof:** Relational commercial data, joins/CTEs/window functions, control-vs-exposed simulation, confidence intervals, ROI, and budget-allocation recommendations

#### Project 4 — Shared Public Portfolio Site

- **Lane:** Shared proof layer
- **Window:** August 24–30 for the first public shell; September 28–October 11 for the complete site (6–10 hours total)
- **Tools:** HTML, CSS, basic JavaScript, Git/GitHub, Power BI embeds, downloads, and short walkthrough videos
- **Proof:** Early live Nuclear and Contract Surety case studies, followed by all three concise case studies with working artifacts, architecture context, synthetic-data disclosures, mobile QA, and fallback media where live embedding is unavailable

#### Refinement and freeze

- **October 12–25:** Improve only the project receiving the strongest response, tighten documentation and résumé proof, run final QA, and tag the releases.
- **After October 25:** Freeze development and use the finished work for applications and interviews.

The protected weekly build rhythm is 1.5 hours Wednesday, 3 hours Saturday morning, and 1.5 hours Sunday. Monday remains an application block, Tuesday a nuclear outreach block, and Thursday an insurance outreach/application block. Friday after work and Saturday afternoon stay protected. Milestone completion is stored in `state.careerContent.portfolioProgress.completedMilestones`.

The release order is intentionally tied to the job-search funnel:

1. Ship a thin Nuclear Project Controls MVP by August 2 and the validated v1 by August 9.
2. Release the Contract Surety Workbench by August 23.
3. Publish the first public portfolio shell by August 30.
4. Release Pharma by September 27 while using current WebHealth experience as the healthcare bridge.
5. Finish the public presentation layer by October 11, refine only the proof receiving meaningful response, and freeze development October 25.

Live application deadlines, interview preparation, warm follow-ups, and the three-person networking triangle take priority over portfolio polish.

### Career Tracker

- A campaign summary keeps the 45% Nuclear, 30% Insurance, and 25% Healthcare allocation visible, with Banking explicitly paused except for unusually strong warm leads.
- Application cards are grouped into **Targets**, **In progress**, and **Interviews & outcomes**. Stage and lane selectors move a card immediately without requiring deletion or re-entry.
- Networking cards are grouped into **To contact**, **In motion**, and **Completed & referred**, with directly editable next actions and notes.
- One lane filter narrows both pipelines, and all stage, lane, next-action, and note changes save automatically.
- Existing records are migrated safely with stable IDs and an inferred lane; application and networking history is not discarded.
- Editable target companies, role families, lane allocations, and campaign decision gates remain available in the source-text editor.
- Tracker content and log entries continue to sync automatically through JSONBin, while Guest sees the same pipelines with every mutation control disabled.

### Notes and settings

- Admin notes save automatically; guests see the synced notes as read-only content.
- Admin settings control job-search quotas, race preference, bike and swim start dates, and nutrition targets; settings controls are disabled for guests.
- Two-account login, signing out, role status, sync status, manual admin cloud sync, and read-only cloud reload remain available.

## Saving and synchronization

Admin sessions save to local storage immediately and use JSONBin for cross-device synchronization. Guest sessions can read local and cloud-backed dashboard data but cannot write to either persistence layer. Both `saveState` and `saveToJSONBin` enforce the admin role before saving.

The sync payload includes:

- Settings
- Completed tasks
- Custom task order
- Hidden daily tasks
- Custom tasks
- Schedule overrides
- Priority Matrix tasks, quadrant placement, and completion
- Dated Journal entries and automatic-save metadata
- Notes
- Career applications, including stable IDs, lane, stage, and notes
- Networking-log entries, including lane, stage, next action, and notes
- Editable career content
- Weekly Roadmap edits
- Networking roster and weekly contact selections
- Portfolio milestone completion
- Google Calendar Apps Script deployment URL

When adding future features, store user-editable information in `state` or `state.careerContent` and confirm that it remains included in the JSONBin payload.

## Mobile support

The dashboard is designed for regular iPhone use:

- Login fields and the sign-in button are at least 48px tall.
- Dashboard buttons and role controls retain touch targets of at least 44px.
- Calendar event rows collapse cleanly on narrow screens, and calendar actions retain 44px touch targets.
- Priority Matrix quadrants stack vertically, while add, move, complete, and delete controls retain 44px touch targets.
- Journal writing and entry history stack into one column, while date navigation and entry buttons retain 44px touch targets.
- The Journal password field and unlock button are at least 48px tall and fit without horizontal scrolling on iPhone.
- Networking actions and selectors use touch targets of at least 44px.
- Career pipeline cards stack into one column; their stage, lane, next-action, notes, and remove controls retain touch-friendly targets.
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
3. Preserve both account roles, the guest read-only guards, and admin JSONBin synchronization.
4. Preserve schedule sorting and the pencil-button schedule editor.
5. Preserve task completion, touch reordering, daily hiding, and restoration.
6. Preserve Roadmap editing, Career Tracker logs, Notes, and Settings.
7. Store new persistent data inside `state`.
8. Keep interactive mobile controls at least 44px tall.
9. Avoid unnecessary third-party dependencies or abstractions.
10. Keep Google Calendar event data read-only and transient; persist only the validated Apps Script `/exec` URL.
11. Keep Priority Matrix tasks in `state.priorityMatrix` and preserve Guest read-only guards for every matrix action.
12. Keep dated Journal entries in `state.journal.entries`, include them in JSONBin synchronization, preserve the Guest read-only guard, and keep the separate journal unlock state session-only.
13. Keep Career Tracker records in `state.careerApps` and `state.careerContacts`; preserve stable IDs, lane/stage fields, direct editing, and Guest read-only guards.

## Update history

### v3.2 — 2026-07-21

- Added a separate password gate inside the Journal tab without changing the main Admin / Guest login.
- Added one-time-per-session journal unlocking through `sessionStorage`, with automatic relocking on sign-out or browser-session close.
- Kept journal entry data out of the interface until unlock and added an inline wrong-password error that leaves access blocked.
- Stored only a SHA-256 password digest in the public single-file application and documented the limits of browser-only access control.
- Preserved Admin editing, Guest read-only behavior, local and JSONBin synchronization, the five-color palette, and iPhone-friendly 48px unlock controls.

### v3.1 — 2026-07-20

- Reframed the generic commercial-pricing project as a **Contract Surety Underwriting & Bond Capacity Workbench**.
- Added a synthetic C$30M hydroelectric spillway rehabilitation bond request as the featured case without making the underlying workbook project-specific.
- Replaced claims frequency-and-severity pricing with contractor financial-statement, WIP, backlog, Capital / Capacity / Character, bond-limit, and remaining-capacity analysis.
- Added a one-page underwriting memo, hydro and contractor stress cases, and a Solver test for maximum additional job size.
- Preserved the existing project dates, milestone IDs, completion history, Portfolio layout, mobile behavior, and synchronization paths.

### v3.0 — 2026-07-17

- Reorganized the job hunt around a visible 45% Nuclear Project Delivery, 30% Insurance / Underwriting, and 25% Healthcare Analytics campaign; paused Banking by default.
- Rebuilt the application and networking logs as responsive three-stage pipelines with direct stage movement, lane changes, next actions, notes, filtering, and automatic JSONBin synchronization.
- Added a safe strategy migration that preserves existing records and milestone progress while replacing outdated campaign text and future networking defaults.
- Moved the Nuclear Project Controls project to the front, added Primavera P6 / Microsoft Project schedule proof, set a thin MVP for August 2, and accelerated the overall development freeze to October 25.
- Reworked the weekly rhythm around Monday applications, Tuesday nuclear outreach, Wednesday build work, Thursday insurance activity, Sunday review, a protected Friday evening, and a protected Saturday afternoon.
- Removed vague generated WebHealth prompts and replaced them with concrete Monday planning and Friday close-out actions.

### v2.9 — 2026-07-17

- Added a dedicated Journal tab with a calm, date-focused writing surface.
- Added a date picker, previous/next day navigation, a Today shortcut, and a browsable history of written dates.
- Added debounced automatic saving, word count, save feedback, and blank-entry cleanup without requiring a Save button.
- Stored dated entries in `state.journal.entries` for local persistence and JSONBin synchronization.
- Preserved Guest as a read-only journal viewer and added responsive iPhone layouts with touch-friendly controls.

### v2.8 — 2026-07-14

- Added a dedicated Priority Matrix tab with the four Eisenhower quadrants: Do, Schedule, Delegate, and Delete.
- Added per-quadrant task creation, completion, permanent deletion, and reliable mouse/iPhone movement through native selectors.
- Added overall and per-quadrant open/completed counts, empty states, and accessible status announcements.
- Stored matrix tasks, quadrant placement, and completion in `state.priorityMatrix` for local and JSONBin synchronization.
- Preserved Guest as a fully read-only preview and added responsive 2×2-to-single-column behavior with 44px controls.

### v2.7 — 2026-07-13

- Added selected-day Google Calendar events beside the existing dashboard schedule.
- Added a palette-matched, responsive event list for timed, all-day, overnight, and multi-day events.
- Added an admin-only Apps Script feed setting, connection test, validation, and JSONBin-backed cross-device configuration.
- Added automatic 10-minute refresh, stale-tab refresh, selected-week loading, manual retry, and a selected-day Google Calendar shortcut.
- Kept calendar event data transient and preserved Guest as read-only while allowing full calendar preview access.
- Documented the complete one-time Apps Script authorization and deployment flow, multi-calendar configuration, privacy tradeoffs, and troubleshooting path.

### v2.6 — 2026-07-13

- Replaced the single-password gate with username-and-password login for Admin and Guest accounts.
- Preserved full editing, local persistence, and JSONBin synchronization for the Admin account.
- Added a read-only Guest preview with access to every dashboard tab.
- Disabled or hid guest editing controls for tasks, schedules, Roadmap content, networking, portfolio milestones, career data, notes, and settings.
- Guarded `saveState` and JSONBin writes so guest sessions cannot persist changes even if an event handler is triggered directly.
- Added session-based role handling, a persistent role badge, a guest-mode banner, and a clearer Sign out action.
- Kept login and dashboard controls at iPhone-friendly 44–48px touch sizes.

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
