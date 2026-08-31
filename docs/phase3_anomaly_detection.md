# Phase 3: Building the Anomaly Detection Model

## What This Phase Is About
Up to this point, we had cleaned the data and built some helper features from it. This phase is where the project actually starts "thinking" for itself — we built a machine learning model that looks at the temperature data and automatically flags moments that look unusual, without us telling it in advance what "unusual" looks like.

## Why This Approach (Isolation Forest)
We used an algorithm called **Isolation Forest**. In simple terms, it works by trying to separate each data point from the rest of the data as quickly as possible. Points that are easy to separate (because they're clearly different from everything around them) get flagged as anomalies. Points that blend in with the crowd don't.

We chose this method for a few reasons:
- It doesn't need us to manually label which moments were "anomalies" beforehand — it figures that out on its own, which fits our situation since we don't have pre-labeled data.
- It's a well-established, widely used technique in real-world systems like fraud detection and server monitoring, so it's a credible, industry-relevant choice rather than something built just for a class project.
- It can look at multiple signals at once (not just the raw temperature, but also how it's been trending, how volatile it's been, and how much it changed hour to hour), which gives it a more complete picture than a simple "if temperature > X" rule would.

## What We Fed the Model
The model was trained on five signals for every hour of data:
- The raw temperature reading
- A 24-hour rolling average (the general trend)
- A 24-hour rolling standard deviation (how much the readings were bouncing around)
- The reading from one hour earlier
- The change from one hour to the next

Together, these give the model a sense of not just "what is the value" but "is this value part of a stable pattern or a sudden shift."

## What We Found

**First attempt:** We initially told the model to expect that roughly 5% of the data would be anomalous. It successfully caught the major event we'd already spotted manually in Phase 1 — a multi-day temperature spike between December 21–25, 2013 — but it also flagged a lot of smaller, everyday fluctuations as anomalies that, on closer inspection, weren't actually unusual. This is a common trade-off in anomaly detection: cast too wide a net, and you catch noise along with the real signal.

**Second attempt:** We tightened the setting so the model would only flag the top 2% most unusual points instead of 5%. This cut down the false alarms considerably while still fully capturing both known events — the major December spike and a smaller spike in mid-January. A handful of borderline flags remained in April and May, which look more like natural seasonal cooling than genuine system problems.

**Final decision:** We kept the 2% version as our working model, since it gives a much cleaner signal without missing anything important.

## Being Honest About the Limitations
No anomaly detection model is perfect, and it's worth being upfront about where this one could be improved:
- The "how much data should be flagged" setting is something we chose manually, not something the model learned on its own — a more advanced setup could adjust this automatically based on the data.
- A few of the flagged points during colder months may just reflect normal seasonal behavior rather than a real problem, and would need a human (or more domain-specific rules) to confirm.

## What's Saved From This Phase
- `anomaly_results.csv` — the full dataset with each row labeled as normal or anomalous, saved in the `data/` folder
- `isolation_forest_model.pkl` — the trained model itself, saved in a new `models/` folder, so it can be reused later without retraining

## What Comes Next
The next phase takes the anomalies this model found and feeds them to a language model (LLM), so instead of just seeing "row 3672 was flagged," we get a plain-English explanation of what happened — something a non-technical person, like an operations manager, could actually read and act on.