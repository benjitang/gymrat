# Workout App — Product Notes

**Positioning:** *"Hevy's polish, Fitbod's brain, without the noise."*

---

## 1. Design Philosophy

- Polished, modern UI — inspired by Hevy.
- Fast, frictionless logging is priority #1. Never let a "smart" feature slow down set entry.
- Generous free tier — direct response to the most common complaint across competitors (limited free versions).
- Deliberately **not** building (for now, maybe later):
  - Apple Watch / wearable support
  - Social features (feed, following, comparisons) — research shows over-indexing on social is a recurring complaint, not a draw.
- Core trust assumption: the app can't verify effort, so it should be built around **multi-week trends**, not single-session judgments. Nudge good habits (e.g. target RPE/rep ranges) rather than police individual workouts.

---

## 2. Exercise & Logging Core

- Extensive built-in exercise library + user-created custom exercises.
- **Variants per exercise** (see Section 3) — up to 3 on free tier, more on paid.
- Set types:
  - **Normal sets** — full tracking (weight, reps, RPE optional).
  - **Warm-up sets** — indicator/flag only, doesn't count toward analytics/progression.
  - **Drop sets** — indicator flag; the drop's specific weight doesn't need precise tracking.
  - **Supersets** — supported. Tri-sets/complex circuits explicitly out of scope for now.
- **Unilateral exercises** — track weight + reps independently per side (left/right), never merged or averaged. The gap between sides is the point.
- **Warm-up set calculator** — auto-suggests warm-up progression into a working weight.
- **Smart rest timer** — adjusts suggested rest based on exercise type/intensity, not a fixed default.
- **Offline-first logging** — sets save locally and sync when connectivity returns (gyms/basements often have poor signal).
- **RPE/RIR** — optional field per set; feeds fatigue scoring and progression logic more accurately than reps/weight alone.

---

## 3. Exercise Variants (physical machine, not location)

- A variant = a specific physical machine/equipment instance, not a location. (Two cable stacks at the same gym can differ; same logic covers home vs. other gyms.)
- Free-text naming, user-defined — don't force a category (location/equipment/grip); let people name it however makes sense to them.
- Default: every exercise starts with **1 invisible variant** — most users never see this system at all.
- Limit: **3 variants on free tier**, higher/unlimited on paid.
- Auto-detect nudge: if a logged weight is a big outlier vs. history, prompt "different machine, or same?"
- Merging/renaming variants must be easy (users will mis-tag early and want to fix it later).

**Analytics split — the core rule:**
| Type | Scope |
|---|---|
| 1RM, PR, strongest lift, progression | **Per-variant** (numbers aren't comparable across machines) |
| Volume, fatigue, muscle balance, consistency | **Aggregated** across variants (training stress is the same regardless of machine) |

- "All variants" combined chart view available, clearly labeled as not directly comparable.
- Auto-progression algorithm must run **per-variant** (a new machine = no valid history yet → treat as a fresh baseline, don't carry over another variant's numbers).

---

## 4. Intelligence Layer

- **Auto-progression** — recommend next week's weight per lift (per variant), Boostcamp-style.
- **AI Coach** — pattern-based notifications, e.g.:
  - Stall detection: *"Your squat has stalled for 3 weeks."*
  - Long-term positive framing: *"Your bench is up 32 lb over 9 months."*
  - Proactive **deload suggestion** when fatigue/stall signals stack up across multiple lifts.
- **Injury flag** — user can flag an injury/body part. When active:
  - Pause stall/decline notifications for affected lifts.
  - Suggest substitute exercises avoiding that movement pattern.
  - Exclude the affected period from long-term trend framing (a forced regression shouldn't read as "you're getting weaker").
- **AI program templates** — lightweight generator (e.g. "4-day PPL"), inspired by Boostcamp/Fitbod but simpler in scope.
- **Exercise substitution suggestions** — e.g. no barbell available → suggest dumbbell/machine equivalent targeting the same muscles.

---

## 5. Analytics Dashboard

- Strength over time / estimated 1RM trends (per-variant)
- Muscle volume per week (aggregated)
- Recovery score / fatigue (training-load based — no wearables needed for MVP)
- Consistency streaks
- Average workout duration
- Strongest lifts ever (labeled with variant, e.g. "450 lb — Gym B, Selectorized")
- Weakest muscle groups
- Training balance (push/pull/legs)
- **Variant comparison insight** (novel): e.g. *"You lift ~15% more on Leg Press at Gym B than at Home."*
- **Unilateral imbalance insight** (novel): e.g. *"Right side ~12% stronger on single-arm rows, consistent for 6 weeks."*

---

## 6. Gamification

**Per-muscle-group leveling**
- Every muscle group has its own level/XP track (chest, quads, lats, etc.), not one global level.
- Points/progress based on **effort/consistency relative to the user's own history**, not raw weight — otherwise variant differences (light vs. heavy machine) unfairly skew scoring.
- Avoid punishing gaps: levels should **decay gradually** toward neutral rather than reset to zero after time off (injury, busy week, etc.).
- Track both a `current_level` (can drop after sustained inactivity) and a `peak_level` (all-time best, never decreases) — inactivity dents your current standing without erasing what you've already proven.
- Streaks/consistency should probably be measured in **weeks trained**, not consecutive days — more forgiving, matches how lifters actually think.

**Badges — flat, binary achievements (GitHub-style)**
- Deliberately **separate from leveling**. Levels are the "real" progression system; badges are lightweight, surprising little rewards layered on top — not a second grind system.
- **Binary, no progress bars**: a badge is either earned or not. No "62/100 days" tracker UI to build or maintain — this keeps them feeling like fun surprises rather than another checklist to grind.
- Can be scoped **per muscle group** (e.g. "Reach Level 10 in Chest") or **overall/account-wide** (e.g. "7 days of working out," regardless of which muscle groups were trained).
- **No tier system** (bronze/silver/gold). If an achievement should escalate, it's a separate badge per threshold (e.g. distinct "7-Day Streak," "30-Day Streak," "90-Day Streak" badges) rather than one badge with tiers — keeps each badge a distinct, nameable moment rather than a ladder.
- Some badges are **time-bound/seasonal** (e.g. "100 Days in 2026") and won't recur automatically — a fresh badge gets defined for the next year/period if wanted, rather than building generic repeat logic for every badge type.
- Flexible, rule-based criteria (streaks, cumulative counts, thresholds, level milestones) so new badges can be added without schema changes — evaluated against existing workout/set/level data rather than maintaining a separate running tally.
- "New badge unlocked" notification moment matters — a small deal is made of it, similar to how GitHub surfaces earned badges.

---

## 7. Platform / Extras

- **Desktop version** — bigger-screen visualization of workouts/analytics.
- **CSV export** — data ownership/trust angle, also useful for the generous-free-tier positioning.
- **Video analysis / form-checking** — flagged as a stretch/v2+ feature, not MVP. Real pose-estimation problem (angle variability, occlusion), good as a separate technical deep-dive/hackathon-style prototype. Frame as "form feedback," not "injury prevention," to avoid overpromising.

---

## 8. Open Design Questions / Things to Revisit

- Exact gamification point formula (especially interaction with variants).
- Cold-start problem: what does the app recommend before any workout history exists? (Needs a short onboarding/self-reported baseline flow.)
- How aggressive should the "different machine?" auto-detect prompt be before it gets annoying.
- Whether drop-set weight tracking should be revisited later for more detail.
- Initial badge set / launch list — which achievements ship in MVP vs. get added later.
- Whether any badges should be repeatable (e.g. recurring monthly badges) or all one-off, given the "flat, no-tier" direction.

---

## 9. Explicitly Out of Scope (for now)

- Apple Watch / wearable integration
- Social features (feed, following, friend comparisons)
- Tri-sets / complex circuits beyond supersets
- Real-time video form analysis (deferred to v2+)
- Badge tier system (bronze/silver/gold) — escalating achievements are handled as separate distinct badges instead
- Badge progress bars — badges are binary earned/not-earned, no partial-progress display

---

## Suggested Build Phasing

**MVP:** Polished UI, fast logging, exercise library + variants (basic), progress charts, generous free tier, auto-progression, AI coach text insights, core analytics, CSV export, offline-first sync.

**V2:** Recovery/fatigue scoring refinements, desktop app, warm-up/plate calculators, exercise substitution, gamification (per-muscle-group leveling + badges), AI program generator.

**V3 / Stretch:** Video form analysis, wearable integration, social features.