# Changelog

All notable changes to Shankar's Regimen tracker are documented here.  
Format: `[vX.Y] YYYY-MM-DD — description`

## [v1.20] 2026-03-10 — Eggs tracking in checklist + Habits chart, 500ml water units, yesterday sleep/steps, badminton sleep auto-adjust

- **Sleep time auto-adjust:** If Badminton checked (8-10:30pm), sleep time updates to 11:15pm (rust color). Default 10:15pm (purple).
  - Works for built-in Badminton items (Sat day type, Workout/Run days)
  - Works for Extra Badminton (optional item added if not in plan)
  - Display shows in Track > Sleep log section
- **Eggs tracking (2 places):**
  - **Checklist:** Counter below water with +/− buttons; auto-populates from breakfast meal selections
  - **Habits:** Weekly egg count chart (Last 30 Days) shows total eggs per week, calculated from logged meals
- **Water intake:** Updated from 300ml to 500ml per check-in; checklist water items now AUTO-INCREMENT water counter by 500ml when checked
- **Water uncheck:** Unchecking water items subtracts 500ml from counter (min 0)
- **Sleep & Steps:** Changed to track yesterday's data (not today's). Tab labels updated: "Tonight's Sleep" → "Yesterday's Sleep", "Today's Steps" → "Yesterday's Steps"
- **Meals updated:**
  - Removed "Bread+eggs (4)" (3-egg option only)
  - Removed "Avocado+nuts" snack option
  - Removed "Skipped" option from snacks
  - All A2 milk replaced with Almond milk in Whey shake recipes and Oatmeal
  - EGG_MEALS updated to track correct egg counts per meal
- **Data cleanup:** ONE-TIME on v1.20 upgrade (deletes all data before today, keeps today's data, sets `_v120_cleaned` flag to prevent re-running)
- **Water checklist sync:** Checking "Water 500ml" items in checklist automatically adds 500ml to water counter; unchecking subtracts
- **Bug fixes:** Data cleanup flag prevents daily wipes; full data retention after initial cleanup

---

## [v1.19] 2026-03-05 — Meal card updates

- Bread+eggs (2): added ½ avocado (~350 cal, 18g P)
- Omelette: added optional bread (2 slices) OR ½ avocado
- Dal+veggies+rice: rice increased ½ → ¾ cup (~380 cal)
- Rajma/channa: added optional ½ cup yogurt OR ½ avocado
- Greek yogurt+berries: added optional 10g nuts OR 1 tbsp chia (~260 cal)
- Eggs only: added ½ avocado (~410 cal, 30g P)
- Plain Oatmeal: added 20g mixed nuts (~330 cal)
- Removed: Oatmeal+fruit (from snack options, MEAL_INFO, colors, CARB_MEALS)

---

## [v1.18] 2026-03-05 — Calorie target + new snack options

- Calorie target updated: ~1,600 → 1,900–1,950 cal/day
- Added snack option: Whey shake+avocado (1 scoop whey + ½ avocado + chia, ~320 cal, 30g P)
- Added snack option: Greek yogurt+berries (1 cup Greek yogurt + berries + honey, ~220 cal, 18g P)
- Body measurements subtext updated: Sunday → Saturday

---

## [v1.17] 2026-03-05 — Date bar order: Today first, older dates right

- Reversed date pill order: Today → Wed → Tue → Mon → Sun → Sat → Fri
- Single loop direction change in `buildDateBar()`

---

## [v1.16] 2026-03-05 — Production release: GitHub sync fully working

- Removed debug console logs
- Fixed `syncCfg` ReferenceError: sync variables moved before IIFE
- `initWithSync()` moved to `DOMContentLoaded` — never called from IIFE
- `loadSyncCfg()` made null-safe for early calls
- Repo made public on GitHub to fix Netlify contributor limit on free plan
- GitHub sync, day type defaults, date picker all working on Netlify

---

## [v1.15] 2026-03-05 — Fix day type button and default on page load

- Root cause: two competing init systems (IIFE + DOMContentLoaded) with inconsistent logic
- Old IIFE had its own day-of-week map and set type from store without hasData guard
- Fixed IIFE to use `defaultTypeForDate()` and same hasData guard as rest of app
- `showMain('today')` now explicitly syncs toggle buttons after setting currentType
- Pull-from-GitHub also syncs toggle buttons after merge
- Result: Thu always shows Run Day on load unless workout data was actually saved

---

## [v1.14] 2026-03-05 — Fix day type reset after GitHub pull on Netlify

- On Netlify, `pullFromCloud` was completing but leaving `currentType` as last-clicked tab
- Root cause: pull merged data and called `renderChecklist()` without resetting `currentType`
- Fix: after pull, apply same `hasData` guard to re-derive `currentType` from loaded data
- Toggle buttons now sync to correct day type after every pull
- Works consistently on both local file and Netlify

---

## [v1.13] 2026-03-05 — Fix pullFromCloud hang on Netlify

- Switched pull from raw GitHub URL to authenticated API endpoint (more reliable)
- Added 8-second AbortController timeout — no more indefinite "Loading from GitHub..." 
- Pull now caches SHA from response, making next push faster
- Graceful fallback: "Timed out — using local data" instead of silent hang

---

## [v1.12] 2026-03-05 — Day type only persists when data is actually saved

- Clicking a day type tab without entering data no longer locks that type
- Navigating away and back restores the schedule default (not the last-clicked tab)
- Exception: if you've checked an item, logged water, or selected a meal, that type sticks
- Type is now written to store only inside `saveTodayData()`, not on toggle click
- Confirm dialog for switching types now also checks meals in hasData guard

---

## [v1.11] 2026-03-05 — GitHub repo sync (replaces JSONBin)

- Replaced JSONBin cloud sync with GitHub API sync to `shankarmsy/regimen/data.json`
- Setup modal now asks for GitHub fine-grained PAT (token stays in localStorage only, never committed)
- On load: pulls latest `data.json` from GitHub raw URL, merges with local data
- On every save: debounced 2s push to GitHub API via PUT to Contents endpoint
- First push creates `data.json` automatically (no 404 errors)
- SHA tracking prevents GitHub API conflicts on concurrent saves
- Each auto-save creates a git commit in your repo (`data: auto-save 2026-03-05 14:32`)
- Pull falls back to localStorage if offline
- "Sync Now" button for manual force-sync

---

## [v1.10] 2026-03-05 — Fix date bar on initial page load

- Date bar pills were missing on fresh file open
- Root cause: no boot call on page load — `buildDateBar()` only ran when navigating via tabs
- Fix: added `DOMContentLoaded` listener that calls `showMain('today')` on startup

---

## [v1.9] 2026-03-05 — Auto day-type by weekday

- Default day type now maps to schedule: Mon/Wed/Fri=Workout, Tue/Thu=Run, Sat=Sat, Sun=Sport
- Applies on Today load and when switching dates in the 7-day date bar
- Saved type always takes priority over default

---

## [v1.8] 2026-03-05 — Extras group fix (word-boundary matching)

- Fixed "Run" appearing in Extras even when not needed on Workout day
- Root cause: `includes('run')` matched "c**run**ch" in meal text
- Fixed with `/\brun\b/i` regex for word-boundary matching
- All 5 day types now correctly show only missing activities in Extras

---

## [v1.7] 2026-03-05 — 7-day date picker + Mushroom Coffee legend fix

- Added scrollable 7-pill date bar at top of Today panel (last 7 days)
- Green pills indicate days with existing logged data
- "✎ Editing Wed Mar 4" badge shows when viewing a past day
- All writes (checklist, meals, drinks, water) redirect to selected date key
- Clicking Today resets to normal mode
- Fixed Habits drinks chart subtitle: "Mushroom" → "Mushroom Coffee — daily counts"

---

## [v1.6] 2026-03-05 — Day-type switch guard + Extras group + Mushroom Coffee

- **Day-type switch guard:** confirm dialog fires if switching day type with existing logged data
- **Extras group:** Badminton and/or Run appear at checklist bottom when not in that day's plan
  - Workout → Run in Extras; Run Day → Badminton in Extras; Rest/Sport → both; Sat → neither
- Fixed drinks legend: "Mushroom" → "Mushroom Coffee"

---

## [v1.5] 2026-03-05 — Today subtabs + Drinks tracker + Drinks trend chart

- **Today** restructured into 3 subtabs: ✅ Checklist · 🍽 Meals · ☕ Drinks
- **Drinks tab:** Coffee (☕), Tea (🍵), Mushroom Coffee (🍄) with +/− counters
  - Caffeine limit tracking: Coffee + Tea capped at 3/day with live label and warning
  - Data saved per-day to localStorage
- **Habits:** new Drinks — Last 30 Days line trend chart (3 separate lines + dashed limit line)
- Water tracker and checklist remain in Checklist subtab

---

## [v1.4] 2026-03-05 — Meal select/deselect + inline detail cards

- Click/tap to select a meal option → highlights pill + expands ingredient+macro card inline
- Click/tap again → deselects and collapses card
- Switching to another option → first deselects and closes, new one opens
- Desktop hover still shows floating tooltip; click selects/deselects
- Unified `handleActivate()` function replaces separate click/touch handlers

---

## [v1.3] 2026-03-05 — Daily macro trend chart in Habits

- New "Daily Macros — Last 30 Days" line chart in Habits subtab
- Three lines: Protein (red), Carbs (gold), Fat (blue)
- Dashed 135g protein target reference line
- Today's P/C/F summary with estimated calories shown below chart
- Macros calculated from MEAL_INFO data for each logged meal

---

## [v1.2] 2026-03-04 — Mobile meal interactions

- Mobile (touch): tap meal pill → expands inline detail card; tap again → collapses
- Desktop (hover): floating tooltip follows cursor (unchanged)
- Global tap-outside-to-close for open meal cards
- Detection via `(hover: none) and (pointer: coarse)` media query

---

## [v1.1] 2026-03-04 — Track tab subtabs: Steps + Runs

- Steps subtab: daily step counter with Log + Progress pattern, 30-day bar chart
- Runs subtab: run logger (distance, time, feel) with pace calculation, trend charts
- Body/Sleep/Shower/Gym/Habits all restructured to Log + Progress inner tabs
- `showSubTab()` generic inner-tab switcher

---

## [v1.0] 2026-03-04 — Initial build

- Complete single-file health tracker
- Today: day-type toggle, time-blocked checklist, water tracker, meal logging
- Track: Body, Habits (heatmap), Sleep, Shower, Gym tabs
- Plan: Schedule, Daily template, Meals, Workout, Supplements, Rules
- 90-day habit heatmap (Mar 4 – Jun 1, 2026)
- Meal variety, egg count, CAVA frequency, macro split charts
- Hand-rolled SVG charts throughout
- localStorage persistence with `shankar_health_v1` key
