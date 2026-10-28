# Data Quality Review — Original `Football_Game.ipynb`

## Purpose

This review is based on inspection of the uploaded notebook source. The notebook was not rerun against the remote source data during this review, so source-dependent counts and percentages have not been independently reproduced.

## Issues to Fix Before Publishing

### 1. Inconsistent DataFrame schema across cells — high priority

The data-collection code in one cell adds `First Scorer Red Cards`. A later visualization cell repeats the extraction process and rebuilds `df` with a different set of columns, including `Lead Held?` but not `First Scorer Red Cards`. Subsequent red-card charts expect `df["First Scorer Red Cards"]`.

**Fix:** perform extraction and cleaning once. Reuse the same validated `df` for every chart. Add an assertion that required columns exist before analysis.

### 2. Potential double-counting of red cards — high priority

The first extraction path counts red-card-like events from both `bookings` and `cards`. If both fields describe the same event, a card can be counted twice. It also treats `SY` as a red card without establishing the source's exact convention.

**Fix:** inspect actual source records and documentation, choose one authoritative event representation (or deduplicate by stable event identifiers), and define precisely whether second-yellow dismissals count as red-card dismissals.

### 3. First-goal minute parsing — high priority

`parse_minute` extracts the first integer from a minute string. For example, `45+2` becomes `45`, so ordering can be wrong when events occur during added time. Missing minutes are assigned `999`, which can also make the first scorer uncertain if event data are incomplete.

**Fix:** parse regular and added-time components explicitly, retain the raw minute, and flag matches where the first scorer cannot be determined confidently. Check own goals and penalty goals according to the source schema.

### 4. Score semantics and outcome classification — high priority

The code assumes meanings and precedence for `ft`, `et`, and `p` score fields. These meanings must be verified against source records. The analysis should distinguish the 90-minute score, the extra-time result, and the penalty shoot-out winner without accidentally mixing score formats.

**Fix:** inspect representative group-stage, extra-time, and penalty-shoot-out records; document the source semantics; then test the outcome function on known examples.

### 5. Counts and denominators need explicit checks — high priority

The notebook reports 604 matches in one section, 581 matches after excluding penalty outcomes in another, and 479 matches in a regulation-time comparison. These may reflect different filters, but the denominators are not consistently explained across the notebook.

**Fix:** print a validation summary that shows, in one place, records retrieved, matches with valid score, matches with at least one goal, matches with known first scorer, outcome categories, missing event fields, and each chart's denominator. Assert that category counts sum to the corresponding denominator.

### 6. Coverage and completeness — medium priority

The notebook requests tournament editions from 1990 through 2026. It catches broad exceptions and prints an error, then continues, which can silently produce a partial dataset if a year fails to download or has a schema change.

**Fix:** create a per-year retrieval log (success/failure, records retrieved, records retained), fail loudly or mark the dataset incomplete when a year is unavailable, and verify expected tournament coverage.

### 7. Exclusion of 0–0 matches — medium priority

The analysis excludes matches with no recorded goal. This is reasonable for a first-goal question, but the README and chart titles must state that the population is matches with at least one recorded goal, not all matches.

**Fix:** report the number of excluded 0–0 matches and distinguish the full match dataset from the first-goal analysis sample.

### 8. Interpretation of red-card association — medium priority

The notebook counts red cards for the first-scoring team across the match, not only cards received after that team's first goal. As currently defined, it cannot answer whether a red card received after taking the lead is associated with losing that lead.

**Fix:** either narrow the research question to “match outcome by whether the first-scoring team received a red card at any point” or add event timestamps and explicitly analyze cards occurring after the first goal. Do not claim causation.

## Minimum Acceptance Checks

Before publishing final results, confirm that:

- [ ] The notebook runs top-to-bottom in a fresh kernel.
- [ ] Every chart uses the same cleaned dataset and documented filters.
- [ ] Required columns exist and types are sensible.
- [ ] No duplicate match records remain.
- [ ] Goal and card event handling is consistent with source documentation.
- [ ] Every percentage's numerator and denominator are visible or documented.
- [ ] Counts in plots, tables, and written conclusions agree.
- [ ] Results are reproducible from a fresh download, or the dataset snapshot and retrieval date are documented.
