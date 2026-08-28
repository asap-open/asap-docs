# Progress & Analytics

The Progress dashboard in ASAP is designed to provide clean, actionable insights into your training consistency, volume progression, and body metrics.

---

## Dashboard Overview

The dashboard is structured into three clean, focused sections:

1. **Key Metric Cards (KPIs)**
2. **GitHub-Style Consistency Heatmap**
3. **Interactive Progression Chart (Recharts)**

---

## 1. Key Metric Cards

Located at the top of the Progress tab, these 4 cards offer an instant snapshot of your training performance over the selected timeframe:

- **Consistency (%)**: Your workout adherence rate based on active days vs. total days in the period.
- **Active Days**: The total number of unique days you trained.
- **Current Streak**: Your active consecutive training streak.
- **Total Volume**: The cumulative weight lifted (in kg) across all logged sets.

---

## 2. Activity Heatmap

The year-long activity heatmap gives you a visual representation of your workout frequency and consistency, modeled after GitHub's contribution graph.

- **365-Day Timeline**: View all training sessions across the past 52 weeks.
- **Intensity Color Levels**: Squares light up in progressively darker shades of green based on daily session density.
- **Hover Tooltips**: Hover or tap any square to inspect the exact date and number of workouts performed.

---

## 3. Progression Chart

A minimal and interactive chart powered by Recharts with smooth gradient fills and responsive tooltips.

### Chart Controls:
- **Metric Dropdown:** Switch between:
  - **Volume (kg)**: Tracks cumulative tonnage progression over time.
  - **Body Weight (kg)**: Tracks body weight weigh-in logs over time.
- **Tags Filter (Multi-Select):** When viewing Volume, filter the chart by specific `SessionLabel` tags (e.g. `Chest`, `Back`, `Shoulders`, `Arms`, `Legs`, `Glutes`, `Core`, `FullBody`, `Cardio`, `Mobility`, `Stretching`).
  - Supports selecting multiple tags at once to isolate specific muscle groups or split days.
  - Includes quick "Select All" and "Reset" actions.

---

## Time Range Selector

Use the range pill buttons at the top of the dashboard to scope all metrics and charts:

| Range | Timeframe |
| ----- | --------- |
| `1W`  | Past 7 days |
| `1M`  | Past 30 days |
| `3M`  | Past 3 months |
| `6M`  | Past 6 months |
| `1Y`  | Past 12 months |
| `ALL` | Full training history |

---

## Next Steps

- [Workout Sessions](/guide/features/sessions)
- [Exercise Library](/guide/features/exercises)
- [Profile & Stats](/guide/features/profile)
- [Progress API Documentation](/api/progress)
