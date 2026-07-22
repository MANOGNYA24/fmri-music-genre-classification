FMRI MUSIC GENRE CLASSIFICATION — PHASE 1
==================================================

PROJECT OVERVIEW
----------------
This project evaluates whether the genre of a 15-second music clip can be
decoded from an individual participant's whole-brain fMRI response.

The analysis uses five participants, ten music genres, and twelve training
runs per participant. After event-file auditing, 40 valid clips are retained
from each run, producing 480 observations per participant.

DATA SOURCES
------------
1. OpenNeuro dataset ds003720, version 1.0.1
   https://openneuro.org/datasets/ds003720/versions/1.0.1

2. Preprocessed fMRI response arrays
   Zenodo record 8275363
   https://zenodo.org/records/8275363

The dataset files are not included in this repository.

ANALYSIS PIPELINE
-----------------
For each participant:

1. Remove the repeated onset-zero event from every training run.
2. Map the remaining 40 events to non-overlapping 10-TR response blocks.
3. Average the 10 response volumes belonging to each 15-second clip.
4. Use Leave-One-Run-Out cross-validation with run number as the group.
5. Within each training fold:
   - remove zero-variance voxels;
   - select the top 1,300 voxels using an ANOVA F-test;
   - standardize the selected features;
   - train a linear SVM with C = 1.0.
6. Generate out-of-fold predictions for all 480 clips.
7. Evaluate performance using accuracy, balanced accuracy, confusion
   matrices, per-genre metrics, a dummy baseline, bootstrap confidence
   intervals, and run-constrained permutation tests.

PRIMARY RESULTS
---------------
Participant   Accuracy
sub-001       32.50%
sub-002       34.17%
sub-003       40.00%
sub-004       27.08%
sub-005       35.83%

Mean accuracy: 33.92%
Nominal chance level: 10%
Dummy-classifier accuracy: 10% for every participant

All five participant-level results were significant under 1,000
run-constrained permutations, with empirical p = 0.001 for each participant.

REPOSITORY CONTENTS
-------------------
FMRI_Music_Genre_Classification_Phase1.ipynb
    Fully executed analysis notebook.

FMRI_Music_Genre_Classification_Phase1_Report.pdf
    Final project report.

requirements.txt
    Python packages required to run the notebook.

outputs_corrected_phase1/
    Saved result tables, figures, predictions, permutation results, and
    analysis_configuration.json.

EXPECTED LOCAL DATA STRUCTURE
-----------------------------
Place the notebook beside the five subject folders:

FMRI_Classification/
|-- FMRI_Music_Genre_Classification_Phase1.ipynb
|-- 001/
|   |-- sub-001_Resp_Training.npy
|   |-- sub-001_task-Training_run-01_events.tsv
|   |-- ...
|   `-- sub-001_task-Training_run-12_events.tsv
|-- 002/
|-- 003/
|-- 004/
`-- 005/

The notebook attempts to detect this structure automatically. If the subject
folders are stored elsewhere, update PROJECT_ROOT in the configuration cell.

INSTALLATION
------------
The completed analysis was run with Python 3.13.2.

Create and activate a virtual environment:

macOS or Linux:
    python3 -m venv fmri_env
    source fmri_env/bin/activate

Windows:
    python -m venv fmri_env
    fmri_env\Scripts\activate

Install the required packages:

    pip install -r requirements.txt

RUNNING THE NOTEBOOK
--------------------
Start JupyterLab:

    jupyter lab

Open FMRI_Music_Genre_Classification_Phase1.ipynb and run the cells in order.

The full run-constrained permutation analysis uses 1,000 permutations for
each participant and may take several hours depending on the computer.

REPRODUCIBILITY
---------------
The notebook saves the final methodological settings and software versions to:

    outputs_corrected_phase1/analysis_configuration.json

The submitted notebook keeps RUN_NESTED_CV = False because the reported result
is based on a fixed, pre-specified linear-SVM baseline. Nested cross-validation
is only required for a later model-tuning comparison.

SCOPE OF THE RESULT
-------------------
The reported result measures within-participant genre decoding across unseen
runs. It does not establish generalization to unseen participants or
performance on the separate official test runs.
