# Phase 2: Feature Engineering

## Objective
Transform raw temperature data into features suitable for an anomaly detection model.

## Features Created
1. **rolling_avg_24h** — 24-hour rolling average, smooths daily fluctuations to reveal underlying trend
2. **rolling_std_24h** — 24-hour rolling standard deviation, measures how volatile/spread out the data was in the past 24 hours
3. **lag_1h** — value from 1 hour ago, lets the model see the immediately preceding reading
4. **diff_1h** — difference between current value and lag_1h, directly captures sudden jumps or drops

## Steps Taken
1. Loaded raw dataset
2. Created the 4 features listed above
3. Dropped rows with NaN values (first 23 rows, where rolling calculations had no prior data) — reduced from 7267 to 7244 rows
4. Saved the processed dataset as `processed_temperature_data.csv` for use in later phases

## Notes
- Original raw CSV is kept unchanged; the processed file is separate, so raw data stays available if features need to be recalculated differently later.
- These features together let the model distinguish between normal fluctuation (small diff_1h, low rolling_std_24h) and abnormal behavior (sustained high rolling_avg_24h, high rolling_std_24h, or large diff_1h jumps) — matching the two anomaly types found in Phase 1 (sustained multi-day event vs. short burst).

## Next Steps
- Apply Isolation Forest anomaly detection model on the processed dataset (Phase 3)