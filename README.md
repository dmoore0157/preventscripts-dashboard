# PreventScripts — Program Performance Dashboard

Internal executive briefing dashboard for PreventScripts RPM &amp; lifestyle-medicine program performance. Prepared for CEO Brandi Harless; distributed to COO David Moore and Jay.

> ⚠ **Internal &amp; confidential.** This repository contains aggregated program performance data from the PreventScripts production database, including named clinical partners, cohort sizes, revenue-opportunity analysis, and other commercially-sensitive metrics. Keep this repository **private**. Do not fork to a public remote. Not for external distribution.

---

## What this is

A single-file HTML dashboard covering 12 metric families across the PreventScripts RPM panel, with age × self-reported-condition small-multiples grids, multi-year trend charts, and drill-down detail tables.

**Report date:** 2026-04-21 · **Data range:** 2023 – April 2026 · **Version:** v3

### Metric families

1. Health Assessments — completion funnel
2. Patient Stratification — risk-tier × age
3. RPM Enrollment — active panel + 34-month flow
4. Churn &amp; Retention — monthly rate vs 2.08% target
5. Usage — reading activity, age × condition grid
6. Data-Transmission Billing — CPT 99445 (new for CY 2026) + 99454
7. Blood Pressure Outcomes — age × condition grid
8. Weight-Loss Outcomes — 180-day change, age × condition
9. Blood-Sugar Outcomes — T2DM + pre-diabetes cohorts
10. Lifestyle Plans &amp; Goal Tracking — coverage + goal distribution
11. Weekly Survey Engagement — response rate + sentiment
12. MA-750 Forced-Onboarding Scheduling — pre/post Feb 11, 2026

### Interactive filters

- **Period** — CY 2024 / CY 2025 / CY 2026 YTD / All periods
- **Age** — &lt;50 / 50–64 / 65+ / All
- **Condition** — All / HTN / T2DM / Obesity / Multi-morbid (self-reported)
- **Sex** — disabled; not captured in source schema (see Data Source Notice below)

---

## Data source notice

**Chronic-condition cohorts are derived from patient self-report** collected via the PreventScripts health-risk assessment instrument. These cohort assignments have **not** been adjudicated against payer claims, provider EHR problem lists, ICD-10 diagnostic coding, or third-party clinical registries. Condition prevalence, cohort sizes, and all condition-segmented metrics should be interpreted as **program-reported** rather than **clinically confirmed**. Independent validation is required for payer negotiation, risk-adjustment modeling, or clinical attribution.

**Sex / biological gender is not currently captured** in the PreventScripts production schema. Sex-stratified analysis is not possible from the current data source. Recommend adding a sex field to the intake assessment.

All other metrics (enrollment, retention, device-reading activity, session completion, vital-sign measurements) are drawn directly from operational production data.

---

## How to view

The dashboard is a single self-contained HTML file. Open `index.html` in any modern browser — no build step, no server, no dependencies beyond a CDN call for Google Fonts.

```bash
# From repo root
open index.html            # macOS
xdg-open index.html        # Linux
start index.html           # Windows
```

Or host behind an internal auth-protected static host (Cloudflare Access, internal S3 + Cloudfront, etc.) if team-wide access is needed.

---

## Refresh workflow

Data is embedded inline as JS arrays near the bottom of `index.html`. To refresh for a new report date:

1. Re-run the SQL queries documented in `docs/queries.md` (to be added) against the `ps-clinical-nerd` MySQL production database
2. Update the embedded data arrays (`assess`, `enr`, `reads`, `bp`, plus the static grid cells in sections 5, 7, 8, 9, 10, 11)
3. Update the masthead `Report date` and the footer `Next refresh` line
4. Commit with a dated tag: `git tag v3.1-2026-05-21 -m "May refresh"`

## Data-quality flags carried in the dashboard

- Assessment "% completed" pre-Dec '24 reads as 100% — platform didn't persist "sent but not completed" rows until Dec 2024. Post-Dec '24 is the reliable baseline.
- `user_events.fk_user_id` NULL across all rows — mobile-app adherence unmeasurable. Engineering flag open.
- `user_visits` has no session-duration field — 99457 20-min threshold is not directly measurable.
- No HbA1c table in schema — T2DM outcomes limited to fingerstick glucose.
- Weight data contains suspected unit-entry outliers (lb vs kg or typo) — input validator recommended.
- 114 historical un-enrollments (pre-2025) lack captured reason.
- Depression is not in the self-reported condition taxonomy.
- Weekly-survey response rate is 2.8% all-time — channel effectively non-functional for data collection.

## Version history

- **v3** (2026-04-21) — Full scope restored: age × condition grids on every segmentable metric; new sections for Weight Loss, Blood Sugar, Plans &amp; Goals, Weekly Surveys; filter bar for Period / Age / Condition; formal sex-data-gap disclosure.
- **v2** (2026-04-21) — Added CPT 99445 2026 billing-opportunity analysis; multi-year data with year selector; 6 inline SVG trend charts; drill-down accordions.
- **v1** (2026-04-21) — Initial 8-section dashboard; single age × condition grid on Usage.

## License

Proprietary. Internal use by PreventScripts only. Not licensed for external distribution.
