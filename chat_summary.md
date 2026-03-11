Here's a full summary you can paste into a new window:

---

## Regimen App — Session Summary (Mar 5–10, 2026)

### What is Regimen?
A personal health tracker web app built as a single `index.html` file. Live at `https://shankarmsy.github.io/regimen`. Repo: `github.com/shankarmsy/regimen` (public). Currently at **v1.19**.

---

### Tech Stack
- Single `index.html` — no build step, no frameworks
- **Hosting:** GitHub Pages (free, auto-deploys on push)
- **Data sync:** GitHub API → `data.json` in same repo. Every save = a git commit. PAT stored in localStorage only, never committed
- **Auth:** Fine-grained GitHub PAT, Contents read/write on regimen repo only
- **Local dev:** Open `index.html` directly in browser, push when ready

### Git workflow
```bash
git add index.html CHANGELOG.md
git commit -m "vX.XX — description"
git push
```
Force push needed if app has auto-saved data to repo since last push: `git push --force`

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
- `pullFromCloud()` — authenticated GET to GitHub API on load, 8s timeout, decodes base64 content, merges with localStorage (cloud wins), re-derives `currentType` after merge
- `pushToCloud()` — debounced 2s after save, fetches SHA first, PUT to GitHub API with base64-encoded JSON
- Sync vars (`syncCfg`, `GH_API`, etc.) declared before IIFE
- `initWithSync()` called from `DOMContentLoaded`, never from IIFE

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

### Version History (this session)
- **v1.12** — Day type only persists on actual data save
- **v1.13** — pullFromCloud: authenticated API + 8s timeout
- **v1.14** — Fix currentType reset after GitHub pull on Netlify
- **v1.15** — Fix IIFE day type defaults, showMain syncs buttons
- **v1.16** — Production release, debug logs removed
- **v1.17** — Date bar: Today first, older dates descending right
- **v1.18** — Calorie target 1,900–1,950, new snack options
- **v1.19** — Meal card updates (avocado additions, dal rice, removed Oatmeal+fruit)

---

### Known Good Patterns
- Always validate with `node -e "new Function(...)"` after JS changes
- Use `playwright` headless browser for behavioral testing
- Body measurement subtext says "Saturday" (was Sunday)
- Netlify was deleted — GitHub Pages is the only host now
- JSONBin was never used (replaced before any data entered)