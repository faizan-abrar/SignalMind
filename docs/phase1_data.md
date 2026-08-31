# Phase 1: Data Loading & Exploration

## Dataset
- Source: NAB (Numenta Anomaly Benchmark) - numenta/NAB GitHub repo
- File: ambient_temperature_system_failure.csv
- 7267 hourly readings, 2 columns: timestamp, value (temperature)
- Chose this dataset because it's a widely-used, real-world benchmark for anomaly detection rather than synthetic data — gives more credible, defensible results.

## Steps Taken
1. Loaded CSV using pandas
2. Converted `timestamp` column from string to datetime type (was stored as text, needed to be datetime for any time-based analysis)
3. Visualized the full time series with matplotlib
4. Checked summary statistics with describe()
5. Filtered data for values > 80 to pinpoint exact anomaly timestamps
6. Applied 24-hour rolling average to smooth daily fluctuations and see the real underlying trend, separate from normal day/night variation

## Findings
- No missing values in the dataset — clean starting point
- Mean temperature: ~71.24, std: ~4.25
- Max value (86.22) is ~3.5+ standard deviations above the mean — statistically this stands out as an outlier, not just visually
- Overall trend shows seasonal variation (higher in summer/fall months, declining from March 2014 onward)

### Anomaly Windows Identified
- **Major anomaly**: Dec 21–25, 2013 — sustained elevated temperature (80–86.22) lasting ~4 days. Initially expected a single spike, but the data shows this was a multi-day sustained event, more consistent with an actual system failure than a sensor glitch.
- **Minor anomaly**: Jan 12, 2014, 8–11 PM — short 4-hour spike (80+), then back to normal. Different pattern from the December event, which is useful — the dataset has both short-burst and long-sustained anomalies to test against.
- The rolling average confirms the December anomaly is a genuine trend shift, not just hourly noise — this matters because it means a simple threshold on raw hourly data isn't enough; a detection approach needs to account for sustained deviation, not just single high points.

## Next Steps
- Feature engineering: create rolling average, rolling std, and lag features as model inputs (Phase 2)
- Apply anomaly detection algorithm — Isolation Forest (Phase 3)