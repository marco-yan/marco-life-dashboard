# Marco Life Dashboard — Update Log

## [v2.1] – 2026-07-13

**Key improvements:**

Added click-to-edit weekly Career and Training roadmap entries.
Saves edits to state.careerContent.weeklyFocus and syncs through JSONBin.
Updated the “This week” focus strip automatically, with hardcoded fallbacks.
Added an “Edit in roadmap” shortcut.
Improved mobile layout, navigation, accessibility, empty states, and overall polish.
Strengthened schedule sorting, sync queuing, and safe text rendering.
Preserved the password, JSONBin configuration, single-file structure, and exact color palette.

The pushed file was fetched back and verified byte-for-byte.

## [v2.0] – 2026-07-10

**What changed:**
- **Switched from GitHub to JSONBin sync** – No more GitHub tokens required for anyone. Just open the link, enter the password, and use the dashboard.
- **Auto-sync works for everyone** – Anyone with the link and password (`RockOn123`) can edit and their changes sync automatically to the cloud.
- **Simplified settings** – Removed GitHub token fields. Added simple "Sync Now" and "Load from Cloud" buttons.
- **Zero setup for new users** – Share the link and password. That’s it.

## [v1.5] – 2026-07-10

**What changed:**
- **Career Tracker is now fully editable** – Company lists, search lanes, and the 24-week strategic plan can be edited directly in the dashboard using the "Edit Career Content" toggle. No HTML editing required.
- **Removed all emojis** – Cleaner, more professional UI with text-only labels and buttons.
- **Auto-sync preserves career content** – All career data (including your custom companies and plan) syncs via GitHub across devices.

## [v1.3] – 2026-07-10

**What changed:**
- **Auto-sync via GitHub** – Dashboard now syncs automatically across all devices (phone, tablet, desktop).
- **New settings section** – Add your GitHub token once, and all data (tasks, checkmarks, schedule edits, settings) syncs to a `data.json` file in your repo.
- **Sync buttons** – "Sync Now" pushes your data to GitHub; "Load from GitHub" pulls it down to any device.
- **Debounced saves** – Changes are saved locally and auto-synced 3 seconds after your last action (no constant API spam).
- **Status indicators** – See real-time sync status (syncing, success, error) in the Settings panel.
---
## [v1.2] – 2026-07-10

**What changed:**
- **Schedule editor** – Click the pencil icon (✏️) next to any date to edit that day's hourly schedule.
- **Add/delete blocks** – In the modal, delete existing time blocks or add new ones (time + label).
- **Reset to default** – Revert any edited day back to the original auto-generated schedule.
- **Persists per day** – Changes save to local storage for each day independently (overrides don't affect other days).

## [v1.1] – 2026-07-10

**What changed:**
- **New color palette** – Replaced blue/grey with custom earth tones (ivory, forest green, sage, antique gold).
- **Password protection** – Added a simple lock screen with password `R` (session-based, no logout on refresh).
- **UI polish** – Cards, shadows, typography, and spacing overhauled for a cleaner, more premium feel.
- **Day pills** – Rounded day buttons with better hover/selected states.
- **Focus strip** – Distinct background to highlight weekly priorities.
- **Checkboxes** – Larger, with forest green accent color.

---

## [v1.0] – 2026-07-10

**Initial release:**
- Weekly dashboard with stats (completion, applications, training, nutrition).
- Interactive day picker with auto-generated schedule.
- Task tracking with checkboxes and local storage.
- Add/remove custom tasks.
- July–December roadmap view.
- Settings panel for race target, quotas, training dates, and nutrition goals.
- Responsive layout for mobile/tablet.
