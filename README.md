# fMRI Music Genre Classification — Phase 1

## Project Overview

This project evaluates whether the genre of a 15-second music clip can be decoded from an individual participant’s whole-brain fMRI response.

The analysis includes:

- 5 participants
- 10 music genres
- 12 training runs per participant
- 40 valid clips per run
- 480 observations per participant

---

## Data Sources

1. **OpenNeuro dataset ds003720, version 1.0.1**  
   https://openneuro.org/datasets/ds003720/versions/1.0.1

2. **Preprocessed fMRI response arrays — Zenodo record 8275363**  
   https://zenodo.org/records/8275363

The dataset files are not included in this repository.

---

## Analysis Pipeline

For each participant:

1. Remove the repeated onset-zero event from every training run.
2. Map the remaining 40 events to non-overlapping 10-TR response blocks.
3. Average the 10 response volumes corresponding to each 15-second clip.
4. Use Leave-One-Run-Out cross-validation, with the run number used as the grouping variable.
5. Within each training fold:
   - remove zero-variance voxels;
   - select the top 1,300 voxels using an ANOVA F-test;
   - standardize the selected features;
   - train a linear SVM with `C = 1.0`.
6. Generate out-of-fold predictions for all 480 clips.
7. Evaluate performance using:
   - accuracy;
   - balanced accuracy;
   - confusion matrices;
   - per-genre metrics;
   - dummy-classifier baseline;
   - bootstrap confidence intervals;
   - run-constrained permutation tests.

---

## Primary Results

| Participant | Accuracy |
|---|---:|
| sub-001 | 32.50% |
| sub-002 | 34.17% |
| sub-003 | 40.00% |
| sub-004 | 27.08% |
| sub-005 | 35.83% |

- **Mean accuracy:** 33.92%
- **Nominal chance level:** 10%
- **Dummy-classifier accuracy:** 10% for every participant

All five participant-level results were significant under 1,000 run-constrained permutations, with empirical `p = 0.001` for each participant.

---

## Repository Contents

```text
FMRI_Music_Genre_Classification_Phase1.ipynb
    Fully executed analysis notebook.

report/
    FMRI_Music_Genre_Classification_Report.pdf

requirements.txt
    Python packages required to run the notebook.

outputs_corrected_phase1/
    Saved result tables, figures, predictions, permutation results,
    and analysis_configuration.json.
