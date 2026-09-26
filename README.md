# Cognitive Offloading Memory Task
### Investigating the relationship between Working Memory Load and Reliance on External Aids

A complete, self-contained, browser-based psychology experiment for studying **cognitive offloading** (the use of external memory aids) under varying working memory load. Built for scientific precision with millisecond-accurate timing and automated data pipeline integration. Built with pure HTML, CSS (Tailwind CSS), and JavaScript — no frameworks, no server required, mobile and desktop compatible.

---

## Overview

Participants complete a memory challenge in which they memorize lists of words (4 or 8 words) and are then tested on recognition. Before each test, they decide whether to "keep" the list in an external aid that they may consult during the test. The task measures **how much participants rely on external aids under low versus high working memory load**, and how that reliance affects accuracy and speed.

The participant-facing interface is framed as a simple **memory game** — no experimental terminology is exposed to participants.

---

## Experimental Design

| Component | Detail |
|---|---|
| **Design** | Within-subjects (each participant does both conditions) |
| **Independent Variable** | Working Memory Load: **Low (4 words)** vs. **High (8 words)** |
| **Total Main Trials** | 10 (5 low-load + 5 high-load, randomized order) |
| **Practice Phase** | 2 trials (1 low-load, 1 high-load) with clear practice labeling |
| **Stimuli** | 80 standardized, high-frequency, high-concreteness English nouns (concrete nouns are the standard for verbal memory tasks, per dual-coding theory — Paivio, 1986) |
| **Item Sampling** | Random selection without replacement from the pool across trials; pool refills only if exhausted |

### Per-Trial Procedure

1. **Fixation Cross** — centered `+` for 1000 ms.
2. **Encoding** — 4 or 8 words displayed for a fixed 5000 ms (auto-advance with a progress bar).
3. **Offload Decision** — self-paced: *"Would you like to keep this list available to view during the task?"* with two **visually neutral** buttons (no color bias). Reaction time from screen appearance to click is recorded.
4. **Recognition / Retrieval** — a grid of all target words plus 8 distractors (12 buttons for low-load, 16 for high-load). Participants select all words they remember.
   - If the participant chose **YES**, a "VIEW LIST" button opens a modal showing the saved word list. Every open is counted, and cumulative time inside the modal is tracked precisely.
   - If the participant chose **NO**, the button is hidden.
   - The **Submit** button is disabled until at least one word is selected.

---

## Measures (Dependent Variables)

Recorded per trial:

| Field | Description |
|---|---|
| `offload_choice` | `"saved"` or `"not_saved"` — offloading frequency |
| `decision_rt` | Offload decision reaction time (ms) |
| `aid_used` | Yes/No — whether the aid modal was actually opened |
| `aid_views` | Number of times the aid modal was opened |
| `aid_duration` | Cumulative time spent inside the aid modal (ms) |
| `hits` | Correctly identified target words |
| `false_alarms` | Distractor words incorrectly selected |
| `accuracy` | Hits ÷ total targets × 100 |
| `task_rt` | Total recognition-screen time **minus** time spent inside the aid modal (ms) |

Plus per-participant demographics: `name`, `age`, `gender`, `education`, `subject_id`, and a `timestamp` for each row.

---

## Data Collection

### Primary: Google Sheets (automatic)
The experiment automatically uploads all main-trial data to a linked Google Sheet via a Google Apps Script Web App the moment a participant finishes. No manual export needed.

**To connect your own sheet:**
1. Open a Google Sheet → **Extensions → Apps Script**.
2. Paste a `doPost(e)` function that parses `e.postData.contents` as JSON and appends each row (header row added automatically on first run).
3. **Deploy → New deployment → Web app**, *Execute as: Me*, *Who has access: Anyone* → **Deploy** and authorize.
4. Copy the Web App URL (ends in `/exec`) and paste it into the `GOOGLE_SCRIPT_URL` constant at the top of the `<script>` block in `index.html`.

> **Note:** Participants only ever see the hosted experiment page (e.g., GitHub Pages). The Google Script URL is called silently in the background, so they never see the "content is user-generated and unverified" Google banner.

### Backup: Local CSV
After the final screen, participants (or the experimenter) can download a local CSV backup of the full session, including practice trials.

---

## Running the Experiment

**Option A — Local:** Just open `index.html` in any modern browser (Chrome, Edge, Firefox, Safari).

**Option B — GitHub Pages (recommended for participants):**
1. Create a public GitHub repository (e.g., `memory-task`).
2. Add this `index.html` (it must be named exactly `index.html`) and this README.
3. **Settings → Pages → Branch: `main` → Save**.
4. Share the generated URL: `https://<your-username>.github.io/memory-task/`

Works on desktop and mobile browsers; timing is recorded with `performance.now()` for millisecond precision.

---

## Technical Notes

- **Timing:** All reaction times and durations use `performance.now()` (millisecond precision). `task_rt` = (recognition screen end − recognition screen start) − cumulative aid modal time, clamped at 0.
- **Aid modal timer:** Starts when the modal opens, stops when it closes, and accumulates across multiple opens within a trial.
- **Counterbalancing:** The 5 low + 5 high main trials are fully randomized per participant; practice trials are always low then high for consistent training.
- **Anti-bias UI:** Dark, low-stress theme; decision buttons are identically styled to avoid signaling a "correct" choice; participant-facing language is game-like.
- **No external data leaves the browser** except the single POST to your Google Apps Script URL on completion.
