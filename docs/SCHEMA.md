# Database Schema Reference

This document explains each table in the ERD: what it stores, why it exists, and what it powers in the actual app UI (screens, charts, features).

---

## Users & Billing

### `users`
The core identity table — one row per account.

**Fields of note:**
- `deleted_at` — soft delete, so historical data (workouts, PRs, analytics) isn't orphaned or lost if a user deletes their account or it's flagged for review.

**Powers in the app:**
- Login/auth, profile screen, display name shown across the app.
- Every other table hangs off this one via `user_id`.

---

### `subscriptions`
Tracks billing/plan status separately from the user record — standard SaaS pattern (same reasoning Stripe, RevenueCat, etc. use).

**Powers in the app:**
- Gating logic: variant limits (3 free, more on paid), Pro-only analytics, AI program generation limits, etc.
- Settings screen: "Manage Subscription," renewal date, upgrade prompts.
- Kept separate from `users` so plan history/renewals/payment webhooks have their own timeline instead of overwriting a single field.

---

## Exercise Library

### `muscle_groups`
A lookup table — one row per muscle group (chest, quads, lats, etc.), tagged with a broader `category` (push/pull/legs/core).

**Powers in the app:**
- Muscle heatmap / training balance chart (push vs. pull vs. legs).
- Filters in the exercise library ("show me all chest exercises").
- Backbone for weekly volume-per-muscle-group analytics and gamification levels.

---

### `exercises`
The exercise library itself — both the global default library and user-created custom exercises live here (`user_id` is `null` for global, set for custom).

**Powers in the app:**
- Exercise picker when building a routine or logging a workout.
- Exercise detail page: instructions, demo media, muscle targets.
- Foundation everything else (sets, routines, variants) attaches to.

---

### `exercise_muscle_groups`
A join table connecting exercises to the muscle groups they train, with a `role` (primary vs. secondary).

**Powers in the app:**
- Muscle-group volume calculations (primary contributes more weight than secondary in the math).
- "What does this exercise work?" display on the exercise detail screen (e.g., a small diagram highlighting primary/secondary muscles).

---

### `variants`
Your standout feature — lets a single exercise have multiple user-defined sub-versions (different physical machines, different gyms, whatever the user decides). Free tier capped at 3, unlimited/higher on paid (enforced in app logic, not the schema).

**Powers in the app:**
- The "which machine?" picker shown during live logging once an exercise has more than one variant.
- Per-variant progress charts and 1RM trends (since numbers aren't directly comparable across machines).
- The "variant comparison" insight (e.g., "You lift 15% more at Gym B than at Home").

---

## Routines / Programs

### `routines`
The **plan** — a reusable workout template (e.g., "Push Day A"), not a record of something that actually happened. Can be user-built or AI-generated (`is_ai_generated` flag).

**Powers in the app:**
- Routines tab / library of saved templates.
- "Generate 4-day PPL" AI program feature writes directly into this table.
- Starting a routine pre-loads target sets/reps into a new `workouts` session.

---

### `routine_exercises`
The exercises that make up a routine, with target sets/reps/RPE and ordering.

**Powers in the app:**
- Routine builder screen (drag-and-drop ordering).
- Pre-filling a new workout session with planned targets when a user starts a routine.

---

## Logged Workouts

### `workouts`
The **actual, historical record** of a training session — what really happened, on a specific date, with a real duration. Optionally references the `routine` it was based on, but can also be fully freeform.

**Powers in the app:**
- Workout history/calendar view.
- "Average workout duration" analytics stat.
- Anchors every logged `set` to a specific date/session.

---

### `sets`
The atomic unit of everything — every individual set logged, tied to an exercise, a variant, and a workout. Carries weight, reps, RPE, set type (normal/warmup/dropset), superset grouping, side (left/right for unilateral), and a `is_pr` flag.

**Powers in the app:**
- Live logging screen (the core "add a set" interaction).
- 1RM trend charts (Epley/Brzycki calculated from weight+reps here).
- Volume-per-muscle-group charts (weight × reps, joined through `exercises → exercise_muscle_groups`).
- Strongest lifts ever / PR history.
- Unilateral left/right imbalance insights.
- PR celebration notifications, triggered the moment `is_pr` flips to true.

---

### `body_weight_logs`
Independent, user-entered body weight over time — not tied to a workout session at all, since people log weight on rest days too.

**Powers in the app:**
- Body weight trend chart (separate from lift-strength charts).
- Optional overlay on strength charts (e.g., "is my bench going up relative to my body weight?").
- Kept as its own lightweight table (not folded into `daily_metrics`) since it's raw user input, not a computed value, and can be logged multiple times a day.

---

## Health / Injury

### `injury_flags`
Lets a user flag an injury tied to a muscle group and/or specific exercise, with a start date and optional resolution date.

**Powers in the app:**
- Suppresses "your squat has stalled" style notifications for affected lifts while a flag is active.
- Drives exercise substitution suggestions (avoid the flagged movement pattern).
- Excludes the injury period from long-term trend framing, so a forced regression doesn't get reported as "you're getting weaker."

---

## Derived / Computed Data

### `daily_metrics`
A **computed daily snapshot**, not raw input — one row per user per day, storing recovery score, fatigue score, and total volume. Generated by a scheduled job, not calculated live on every page load.

**Powers in the app:**
- Recovery/fatigue trend charts on the Home/Progress dashboard.
- Deload-week suggestions (triggered when fatigue trends high over multiple days).
- Total volume trend line (overall, not per muscle group — that's `muscle_group_scores`).

---

### `insights`
A log of AI Coach-generated events — stalls, PR milestones, consistency streaks, level-ups, deload suggestions. Each row is a discrete, timestamped event a user can view or mark as read.

**Powers in the app:**
- Home screen "AI Coach" feed (e.g., "Your squat has stalled for 3 weeks," "Bench up 32 lb in 9 months").
- PR celebration notifications.
- Achievement/badge history — doubles as your gamification milestone log, so no separate rewards table is needed.

---

## Gamification

### `muscle_group_scores`
A weekly computed snapshot per user per muscle group — points, this period's volume, the user's own rolling average (for relative comparison), plus two separate level fields:
- `current_level` — reflects present training state; **can decrease** after sustained inactivity (real detraining).
- `peak_level` — all-time best; **never decreases**, so past achievements are never erased.

**Powers in the app:**
- Muscle-group progress bars on the gamification screen.
- "Which muscle groups are gaining vs. lacking" view.
- Points calculated relative to the user's own history (not raw weight), so results stay fair across different variants/equipment.

---

## Design Principles Reflected in This Schema

- **Computed vs. raw data are separate tables.** Anything the app calculates (`daily_metrics`, `muscle_group_scores`, `insights`) is stored as periodic snapshots, not recalculated live on every screen. Anything the user directly enters (`sets`, `body_weight_logs`, `injury_flags`) is raw input.
- **Variant-level vs. aggregate-level analytics is a deliberate split.** Progression, 1RM, and PRs are scoped per variant (numbers aren't comparable across machines). Volume, fatigue, and muscle balance are aggregated across variants (training stress is the same regardless of which machine caused it).
- **Soft deletes everywhere on user-generated content**, so historical analytics never break because a user deleted a custom exercise or variant.
- **Tier/limit enforcement lives in application logic, not the schema** — the free-tier variant cap, for example, isn't a hard constraint in the database, so pricing changes never require a migration.