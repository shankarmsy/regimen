# 🏋️ Shankar's Health & Performance Regimen

A single-file personal health tracker built as a self-contained HTML app. No server, no dependencies, no login — runs entirely in the browser with localStorage for persistence.

---

## What it tracks

### Today
Three subtabs for logging the current day (or any of the past 7 days):

| Tab | What's inside |
|-----|---------------|
| ✅ **Checklist** | Day-type toggle (Workout / Run / Sat / Volleyball / Rest), progress bar, water intake, full time-blocked checklist. Auto-selects correct day type by weekday. |
| 🍽 **Meals** | Breakfast, Lunch, Snack, Dinner — tap to select meal + expand ingredient/macro detail card. |
| ☕ **Drinks** | Coffee, Tea, Mushroom Coffee counters with caffeine limit tracking (max 3/day). |

### Track
Seven subtabs of logged history and charts:

| Tab | What's inside |
|-----|---------------|
| 💪 **Body** | Weight log + 30-day trend chart |
| 🔥 **Habits** | 90-day habit heatmap, meal variety, egg count, CAVA frequency, macro split, daily P/C/F trend, drinks trend |
| 😴 **Sleep** | Hours + quality log, weekly trend chart |
| 🚿 **Shower** | Cold shower log + streak |
| 🏋️ **Gym** | Workout logger with exercise sets/reps/weight, progress charts per exercise |
| 👟 **Steps** | Daily step counter + trend |
| 🏃 **Runs** | Run log (distance, time, pace, feel) + trend charts |

### Plan
Reference tabs for the full protocol:

- **Schedule** — weekly day-by-day plan with daily timelines
- **Daily** — full time-blocked daily template
- **Meals** — complete meal cards with ingredients and macros
- **Workout** — 4-day 2Bros workout plan
- **Supplements** — supplement stack and timing
- **Rules** — eating rules, banana rules, non-negotiables

---

## Key features

- **Single HTML file** — open in any browser, no install
- **localStorage persistence** — data stays across sessions per device
- **7-day date picker** — backfill any of the past 7 days
- **Auto day-type** — Mon/Wed/Fri=Workout, Tue/Thu=Run, Sat=Sat+Run, Sun=Volleyball
- **Extras group** — Badminton and/or Run appear at checklist bottom if not already in that day's plan
- **Day-switch guard** — warns before resetting logged data when changing day type
- **Meal detail cards** — tap to expand ingredients + macros inline (mobile) or hover tooltip (desktop)
- **Macro trend chart** — daily P/C/F calculated from logged meals, last 30 days
- **Drinks trend chart** — Coffee / Tea / Mushroom Coffee per day, last 30 days
- **Cloud sync** — optional, via configurable endpoint

---

## Tech stack

| Layer | Details |
|-------|---------|
| Runtime | Vanilla HTML + CSS + JS, zero frameworks |
| Charts | Hand-rolled SVG |
| Storage | `localStorage` (key: `shankar_health_v1`) |
| Fonts | System monospace + serif stack |
| Size | Single `.html` file, ~3500 lines |

---

## Local setup

```bash
# Just open the file
open Regimen.html

# Or serve locally
npx serve . && open http://localhost:3000/Regimen.html
```

---

## Repo structure

```
regimen/
├── Regimen.html      # The entire app
├── README.md         # This file
└── CHANGELOG.md      # Full version history
```

---

## Changelog

See [CHANGELOG.md](./CHANGELOG.md) for full version history.

---

## Diet protocol summary

- **Eating window:** 8:15am – 6:00pm
- **Calories:** ~1,600/day
- **Protein target:** 130–145g/day
- **Water:** 2.7L+ daily
- **Caffeine:** max 3 drinks, last by 1pm
- **Lights out:** 10pm

*2Bros Plan · Liver-first · Day 1: Mar 4, 2026*
