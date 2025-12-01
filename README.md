📌 Cable Tracking Case Study — Spaarnwoude Experiments

This repository contains my full analysis of hydrographic survey data collected in Spaarnwoude.
The goal was to:

Decode the meaning of each column in the VCoG and CCoG files

Understand the coordinate system

Investigate the GPS heading issue mentioned in the email

Reconstruct cable world coordinates

Visualize before/after comparisons

Verify alignment, direction consistency, and data quality

All work was done from scratch, using only the raw CSV files and the experiment description.

🚀 1. Column Identification (Data Intuition)
📌 Time Columns (Columns 0 and 1)

Column 0 is Unix time (seconds since 1970).
We confirmed this because:

It increases smoothly without jumps

Values fall exactly within valid 2025 Unix timestamps

Column 1 is a human-readable HHMMSS.sss timestamp, which jumps at every minute (e.g., …59 → …100).

📌 World Coordinates (Columns 2, 3, 4)

From the email we knew the exact test location.
We reverse-engineered the coordinate system:

Tried multiple CRS conversions on the Spaarnwoude lat/lon

Found that EPSG:32631 (WGS84 / UTM zone 31N) perfectly matched the data

Therefore:

Column 2 = World X (Easting)

Column 3 = World Y (Northing)

Column 4 = Height (Netherlands vertical datum)

📌 Heading Column (VCoG only)

Column 5 in VCoG = heading angle in degrees.
Ranges confirm this:

Experiment	Heading Range
Exp1	-177° to 179°
Exp2	-95° to -78°
Exp3	+81° to +108°
Exp4	-98° to -70°
Exp5	+83° to +105°

This matches exactly with:

Some experiments running East→West

Others running West→East

📌 Cable Offset Columns (CCoG only)

CCoG files have 12 columns.
We identified:

Column 5 = strongly correlated with Δy

Column 7 = strongly correlated with Δz

Columns 0–1 = time

Columns 2–3–4 = world coordinates

Last column = quality flag (1 = good fix), mentioned explicitly in the email

🧪 2. Data Cleaning & Flag Alignment

Both VCoG and CCoG include a "good solution" flag in the last column.

We verified that:

VCoG and CCoG have the same number of flagged rows

All zero-flag rows occur on identical indices

This ensures:
✔️ Perfect alignment
✔️ We can safely drop these rows

🧭 3. Heading Correction Investigation

The email mentioned a heading issue in earlier experiments.
We mathematically analyzed it.

The proposed correction was:
corrected_cable = 2 * cart_position - cable_position


This is equivalent to a 180° flip of the cable vector relative to the cart.

Results:

Exp1: No visible change (path is a full loop—symmetric both ways)

Exp2: Slight improvement but also slightly noisier

Exp3–4–5: Applying a correction produced huge shifts, proving that these experiments already have correct heading

🔍 Final interpretation:

Exp1 and Exp2 may have had a heading issue (as email hinted)

Exp3, Exp4, Exp5 are definitely correct in raw form

Therefore we do NOT apply heading correction globally

🖼️ 4. Visual Diagnostics

The notebook includes plots for:

✔️ Time columns (to identify Unix & HHMMSS.sss)
✔️ World X/Y/Z (raw vs direction-normalized)
✔️ VCoG vs CCoG comparisons
✔️ Per-experiment cable XY paths
✔️ Before/after heading correction
✔️ Experiments aligned in a common direction
✔️ Cross-experiment overlays for stability comparison

These visualizations guided all our reasoning.

🧩 5. Final Conclusions

Columns successfully decoded

Coordinate system identified unambiguously

Heading issue confirmed only for early experiments

Direction normalization improves cross-experiment overlay

Cable tracking stability decreases with increasing cable distance, which matches physical expectations

Data appears clean, consistent, and well structured after flagged rows are removed

🎥 6. Video Walkthrough (≤7 minutes)
[video1880073505.mp4.zip](https://github.com/user-attachments/files/23850794/video1880073505.mp4.zip)


