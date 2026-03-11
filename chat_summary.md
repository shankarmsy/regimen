Here's a full summary you can paste into a new window:

---

## Regimen App — Session Summary (Mar 5–11, 2026)

### What is Regimen?
A personal health tracker web app built as a single `index.html` file. Live at `https://shankarmsy.github.io/regimen`. Repo: `github.com/shankarmsy/regimen` (public). Currently at **v1.20**.

---

### Tech Stack
- Single `index.html` — no build step, no frameworks
- **Hosting:** GitHub Pages (free, auto-deploys on push)
- **Data sync:** GitHub API → `data.json` in same repo. Every save = a git commit. PAT stored in localStorage only, never committed
- **Auth:** Fine-grained GitHub PAT, Contents read/write on regimen repo only
- **Local dev:** Open `index.html` directly in browser, push when ready

### Git workflow
```bash
git add index.html CHANGELOG.md data.json chat_summary.md
git commit -m "v1.20 — description"
git push --force
```

---

### App Structure
- **Today tab** — main daily logging view with date bar (Today first, 7 pills descending left→right), day type toggle, checklist, meals, drinks/water
- **Track tab** — body measurements, sleep, shower, steps charts
- **Plan tab** — meal plan schedule, meal cards, rules

### Day Type Logic
Schedule defaults: Mon/Wed/Fri = Workout, Tue/Thu = Run, Sat = Sat (workout+run), Sun = Sport/Volleyball
- Day type is derived from `defaultTypeForDate()` using this map
- Type only persists to storage when real data is saved (`saveTodayData()`) — clicking a tab without entering data does NOT lock the type
- On page load and after GitHub pull, `hasData` guard re-derives type: only restores saved type if `done > 0`, `water > 0`, or meals logged

### GitHub Sync
- `pullFromCloud()` — authenticated GET to GitHub API on load, 8s timeout, decodes base64 content, merges with localStorage (cloud wins)
- `pushToCloud()` — debounced 2s after save, fetches SHA first, PUT to GitHub API with base64-encoded JSON
- Sync vars declared before IIFE
- `initWithSync()` called from `DOMContentLoaded`

---

## v1.20 Changes (Mar 10–11, 2026)

### Sleep Log — Track Tab (Yesterday's Sleep)
- **Hours slept:** Number input (0-12 hours, 0.25 increments)
- **Sleep time:** Time input (HH:MM format) — e.g., 23:15 for 11:15pm
- **Sleep score:** Number input (0-100 scale) — replaces quality buttons
- All three fields required to save
- Data saved: `{hrs, time, score, date}`
- **Sleep Trend Chart:** Shows last 30 days with:
  - Green bars = hours slept (target 7.5h baseline)
  - Rust line with dots = sleep score trend (0-100)

### Water Tracking
- **Units changed:** 300ml → 500ml per checklist item
- **Auto-sync:** Checking "Water 500ml" items automatically adds 500ml to water counter
- **Uncheck:** Unchecking water items subtracts 500ml (min 0)
- Manual +/- buttons (Cup 500ml, Bottle 1L) still work independently

### Eggs Tracking (2 Places)
- **Checklist:** Counter below water section with +/− buttons
  - Auto-populates from breakfast selection (Omelette→4, Bread+eggs(4)→4, Bread+eggs+avocado→3, Rice+eggs→3, Eggs only→5)
  - Deselecting breakfast resets to 0
  - Persists in storage under `eggs` key
- **Habits chart:** Daily Egg Count (last 30 days) — LINE CHART
  - Trend line with dots showing daily egg totals from all logged meals
  - Rust color (#c4440a), x-axis shows date labels every 5 days
  - Auto-calculated from meal data

### Sleep & Steps: Yesterday Tracking
- Both now log yesterday's data (for morning logging)
- `sleepDayKey()` defaults to `yesterdayKey()`
- `stepsKey()` defaults to `yesterdayKey()`
- Labels updated: "Tonight's Sleep" → "Yesterday's Sleep", "Today's Steps" → "Yesterday's Steps"

### Badminton Sleep Auto-Adjust

**Two places track sleep time:**

1. **Checklist (Today tab)** — Shows TODAY's sleep time
   - "Sleep — 7h 30min target" displays 11:15pm or 10:15pm
   - If TODAY has badminton (plan OR extra checked): **11:15pm** (rust)
   - Otherwise: **10:15pm** (purple)
   - Updates when rendering checklist or toggling badminton

2. **Sleep Log (Track tab)** — Shows YESTERDAY's sleep time
   - Top of sleep log section shows bedtime reference
   - If YESTERDAY had badminton (plan OR extra checked): **11:15pm** (rust)
   - Otherwise: **10:15pm** (purple)
   - Called when opening sleep log tab

**Example:**
- TODAY is Wednesday (workout day with badminton) → Checklist shows 11:15pm
- YESTERDAY was Tuesday (run day, no badminton) → Sleep tab shows 10:15pm
- Click "Badminton extra" on non-badminton day → Checklist updates to 11:15pm (sleep tab unaffected)

### Meals Updated
- **Removed:** Bread+eggs (4), Avocado+nuts, Skipped
- **A2 milk → Almond milk:** Whey shake+nuts, Whey shake+avocado, Oatmeal
- **EGG_MEALS map:** Tracks eggs per meal for chart calculation

### Data Cleanup (One-Time on v1.20 Upgrade)
- Runs in `DOMContentLoaded` before app initializes
- Uses `_v120_cleaned` flag to run only once
- Keeps today's data + sets flag; deletes all pre-today entries
- Today's data (meals, water, drinks, checklist) 100% preserved
- Prevents daily wipes on page reload

### Data Persistence
- `saveTodayData()` now preserves `eggs` value alongside `meals` and `drinks`
- `resetAll()` resets `eggsCtr = 0` alongside `waterMl = 0`

---

### Meal Plan / Nutrition
**Calorie target:** 1,900–1,950 cal/day
**Protein target:** 130–145g/day

**Recent meal card updates (v1.19):**
- Bread+eggs (2): + ½ avocado → ~350 cal, 18g P
- Omelette: optional bread or ½ avocado
- Dal+veggies+rice: rice ½ → ¾ cup → ~380 cal
- Rajma/channa: optional ½ cup yogurt or ½ avocado
- Greek yogurt+berries: optional 10g nuts or 1 tbsp chia → ~260 cal
- Eggs only: + ½ avocado → ~410 cal, 30g P
- Plain Oatmeal: + 20g mixed nuts → ~330 cal
- Removed: Oatmeal+fruit

**New snack options added (v1.18):**
- Whey shake+avocado: 1 scoop whey + ½ avocado + chia + A2 milk (~320 cal, 30g P)
- Greek yogurt+berries: 1 cup Greek yogurt + berries + honey (~220 cal, 18g P)

---

### Google Calendar Reminders (created this session, starting Mar 9)
All recurring, Central Time, popup notifications:

| Time | Event | Days |
|------|-------|------|
| 6:00am | 💪 Workout + log it | Mon/Wed/Fri |
| 6:00am | 🏃 Run 25-30 min + log it | Tue/Thu |
| 7:00am | 📏 Body measurements | Sat |
| 8:00am | 💪🏃 Sat workout + run | Sat |
| 8:15am | 🌅 Log breakfast + steps + shower + sleep quality | Daily |
| 10:00am | 💧 Water check | Daily |
| 12:00pm | 💧 Water check (halfway, 1.3L by now) | Daily |
| 1:00pm | 🥗 Log lunch + last caffeine | Daily |
| 2:00pm | 💧 Water check (aim 2L) | Daily |
| 3:00pm | 🍎 Log snack | Daily |
| 4:00pm | 💧 Water check (last push, hit 2.7L) | Daily |
| 6:00pm | 🍛 Log dinner + Metformin + fish oil | Daily |
| 8:00pm | 🏸 Badminton 8–10:30pm | Mon/Wed/Sat |
| 9:00pm | 🥣🥜 Soak chia + nuts + Rosuvastatin + Losartan + Magnesium | Daily |
| 10:00pm | 📋 Review checklist + wind down | Daily |

---

### Version History
- **v1.12** — Day type only persists on actual data save
- **v1.13** — pullFromCloud: authenticated API + 8s timeout
- **v1.14** — Fix currentType reset after GitHub pull
- **v1.15** — Fix IIFE day type defaults, showMain syncs buttons
- **v1.16** — Production release, debug logs removed
- **v1.17** — Date bar: Today first, older dates descending right
- **v1.18** — Calorie target 1,900–1,950, new snack options
- **v1.19** — Meal card updates (avocado additions, dal rice, removed Oatmeal+fruit)
- **v1.20** — Water 500ml units, Eggs checklist + daily chart, Yesterday sleep/steps, Badminton sleep auto-adjust, Data cleanup

---

### Code Locations (v1.20)
- `MEAL_OPTS` — line ~1709 (breakfast/lunch/snack/dinner options)
- `MEAL_INFO` — line ~1190 (ingredient cards)
- `EGG_MEALS` — line ~1741 (egg counts per meal)
- `DAYS` — line ~2239 (checklist schedule per day type)
- `toggleItem()` — water add/subtract logic on check/uncheck
- `addEggs(n)` / `updateEggDisplay()` — egg counter functions
- `yesterdayKey()` — after `todayKey()` definition
- `updateSleepTimeDisplay()` — checks yesterday for badminton, updates sleep time
- `saveTodayData()` — preserves eggs
- `DOMContentLoaded` — one-time data cleanup with `_v120_cleaned` flag

---

### Known Good Patterns
- Always validate JS: `node -e "new Function(require('fs').readFileSync('index.html','utf8').match(/<script[^>]*>([\s\S]*)<\/script>/)[1])"`
- GitHub Pages auto-deploys in ~30 seconds after push
- PAT stored in localStorage only, never committed
- `shankar_health_v1` is the localStorage key
- Force push if app has auto-saved data since last push