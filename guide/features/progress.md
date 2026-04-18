# Progress Tracking

The Progress tab is the analytics center of ASAP. It combines consistency, workload, strength trends, and personal-best history into a single workflow.

## Overview

Progress is powered by backend-first V2 endpoints and organized into focused panels:

- Summary and KPI snapshot
- Consistency calendar with drilldown
- Workload time series and compare mode
- Strength trend (e1RM) by exercise
- Muscle-balance distribution and filters
- PB timeline with jump-to-day behavior

## Getting Started

1. Open **Progress** from the bottom navigation.
2. Pick a training mode: **Strength**, **Balanced**, or **Body**.
3. Pick a range: **1W**, **1M**, **3M**, **6M**, **1Y**, or **ALL**.
4. Click a calendar day to open day-level drilldown.

## Progress Controls

The mode and range selectors are now part of page content (centered near the top), not fixed in the header. They scroll with the page and update all panels together.

## Sticky Header Behavior

The Progress title header is intentionally minimal and hides while scrolling down to maximize chart space on mobile.

- Near top of page: header is visible
- Scrolled down: header collapses
- Returning near top: header reappears

This animation uses Framer Motion for smoother transitions.

## Consistency Calendar

The calendar panel highlights recent training with a compact weekly strip and metrics summary.

- Switch visual metric: sessions, volume, or intensity
- Select a day to drill into sessions/exercise breakdown
- View current and best streak context

On mobile, selecting a day opens a compact **Day Detail** bottom sheet with session/set/volume summary.

## Day Detail Drilldown

Day detail loads only after explicit day selection.

- Session-level list (duration, exercise count, set count)
- Exercise breakdown (sets and volume per exercise)
- Optional muscle-group filter from the insights panel

## Workload Panel

Workload volume is available in day/week/month granularity with optional previous-period comparison.

- Toggle granularity: `day`, `week`, `month`
- Toggle compare mode to view previous period buckets
- Hover/focus bars to inspect exact values

## Strength Trend Panel

Exercise-specific strength progression is computed from estimated 1RM (`e1RM`).

- Select exercise from dropdown
- Inspect latest, best, and change %
- Click/hover points to compare against previous point

## Muscle Balance and Status

Muscle balance shows set distribution by movement group (`push`, `pull`, `legs`, `core`, `other`) and compares it to mode-aware targets.

Training Status adds workload risk signals:

- ACWR
- Ramp rate
- Confidence and recommendation text

## PB Timeline

PB Timeline surfaces PR events across the selected range.

- Click an event to focus that exercise
- Event selection can jump context to the achieved day

## Tips

- Review **weekly trends** instead of day-to-day noise.
- Use calendar drilldown for adherence and recovery patterns.
- Use muscle-balance filters when sessions feel biased toward one movement pattern.

## Next Steps

- [Workout Sessions](/guide/features/sessions)
- [Exercise Library](/guide/features/exercises)
- [Profile & Stats](/guide/features/profile)
- [Progress API](/api/progress)
