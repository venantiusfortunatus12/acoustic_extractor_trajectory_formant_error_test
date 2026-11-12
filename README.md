# 100-point Acoustic Trajectory Extractor

A Streamlit app for extracting FPCA-ready acoustic trajectories from paired
audio and Praat TextGrid files.

The default automatic formant audit evaluates 28 Burg LPC settings per
recording:

- maximum formant: 4000–5500 Hz in 250-Hz steps
- maximum number of formants: 4.0, 4.5, 5.0, and 5.5

Candidates are ranked using missing measurements, frame-to-frame jumps,
formant-order violations, excessive bandwidth, and sensitivity to neighboring
settings. F2–F3 proximity is reported separately and is not automatically
treated as an error. The selected setting is then used for the final 100-point
trajectory extraction.

For every interval in the selected tier, the app writes 100 normalized time
points containing:

- absolute time (`time_s`, `time_ms`)
- within-segment time (`segment_ms`, `time_percent`)
- F0 (`f0_hz`) and a binary voicing mask (`voiced`)
- intensity (`intensity_db`)
- F1–F3, F1–F5, or F1–F6, depending on the selected setting

Unvoiced F0 is stored as missing data rather than 0 Hz. The app does not
linearly extrapolate F0 through unvoiced regions.

The 100 normalized rows are evaluation points, not 100 independent acoustic
observations. Preserve token and speaker identifiers when fitting an FPCA or
other functional model.

## Local run

Python 3.10 or newer is required.

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
streamlit run app.py
```

## Streamlit Community Cloud

1. Push this directory to a GitHub repository.
2. In Streamlit Community Cloud, create an app from that repository.
3. Set the main file path to `app.py`.
4. Deploy. Dependencies are installed from `requirements.txt`.

## Output

CSV contains the complete trajectory table. Excel contains three sheets:

- `trajectories`: one row per interval and normalized point
- `interval_qc`: voicing and measurement coverage by interval
- `formant_parameter_audit`: all 28 candidates and their diagnostic metrics
- `recommended_settings`: selected setting and recording grade by file
- `metadata`: analysis parameters and package versions

The CSV is UTF-8 with BOM for reliable opening in Excel. If the trajectory
table exceeds Excel's row limit, the app keeps CSV download enabled and
disables Excel output.
