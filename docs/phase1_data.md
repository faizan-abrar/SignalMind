# Phase 1: Data Loading & Exploration

## Dataset
- Source: NAB (Numenta Anomaly Benchmark) - numenta/NAB GitHub repo
- File: ambient_temperature_system_failure.csv
- 7267 hourly readings, 2 columns: timestamp, value (temperature)

## Steps Taken
1. Loaded CSV using pandas
2. Converted `timestamp` column from string to datetime type
3. Visualized the full time series with matplotlib
4. Checked summary statistics with describe()

## Findings
- No missing values in the dataset
- Mean temperature: ~71.24, std: ~4.25
- Max value (86.22) is ~3.5+ standard deviations above the mean — likely anomaly
- Visual spike observed around December (end of year)
- Overall trend shows seasonal variation (higher in summer months, lower in later months)

## Next Steps
- Feature engineering (rolling averages, lag features)
- Apply anomaly detection algorithm (Isolation Forest)