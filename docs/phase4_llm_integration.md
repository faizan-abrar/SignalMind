# Phase 4: LLM Integration — Turning Anomalies into Plain-English Explanations

## What This Phase Is About
Up to this point, the system could tell us *that* something unusual happened — a row flagged as an anomaly, with some numbers attached. But a raw number doesn't mean much to someone who isn't looking at the data directly. This phase adds a layer that takes each detected anomaly and explains it in plain language, the way a person would if they were looking at the readings and telling you what they noticed.

## Approach
- Used Google's Gemini API (gemini-3.6-flash) to generate explanations.
- For each anomaly, built a prompt containing: the timestamp, the actual reading, the 24-hour rolling average at that time, and the change from the previous hour — giving the model enough context to reason about *why* the reading looks unusual, not just that it does.
- Instructed the model to write for a non-technical audience (an operations manager), in 2-3 sentences, without jargon.

## Example Output
For the December 21, 2013, 8:00 PM anomaly (temperature 82.29°F vs a 24-hour average of 78.60°F), the model produced:

> "At 8:00 PM on December 21, the temperature rapidly spiked to 82.3°F, rising nearly 2.4 degrees in a single hour to sit well above the day's average of 78.6°F. The system flagged this because temperatures rarely jump this quickly on a winter evening, when room conditions should be stable or cooling down. This sudden rise likely points to a heating system malfunction, an unmonitored heat source near the sensor, or an external vent or door left open."

This shows the model isn't just restating numbers — it's reasoning about what's normal for the time of day and season, and suggesting plausible real-world causes.

## Scaling and a Real-World Constraint
The explanation logic was built as a reusable function so it can run on any anomaly row, not just one. When applied to a wider set (a mix of the December event, the January event, and a random sample of other flagged points), the free tier of the Gemini API hit its daily request quota (20 requests/day) partway through.

Rather than delay the project waiting for quota reset, the explanations already generated (5 anomalies, all successful) were kept as the working result for this phase, and documented honestly as a rate-limit constraint rather than a design flaw. The same function and prompt structure scale directly to the full anomaly set given a paid tier or a longer processing window — this is a common trade-off in real projects working with metered APIs, and something worth being upfront about rather than hiding.

## What's Saved From This Phase
- `anomaly_explanations.csv` — timestamp, value, key features, and generated explanation for each processed anomaly, saved in the `data/` folder

## What Comes Next
Phase 5 builds a Streamlit dashboard that displays the temperature data, highlights detected anomalies, and shows these plain-English explanations — turning the whole pipeline into something a non-technical person could actually open and use.