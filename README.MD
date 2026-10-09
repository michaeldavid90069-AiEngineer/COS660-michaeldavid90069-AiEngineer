Markdown
Hello Dr. Leis, Dr. Kachirski, and Gary Alvarez,

Please find attached my Milestone 2 screencast demonstrations (3-minute deep dive and 90-second highlight reel) and GitHub push screenshot for the Audio Synchronizer Engine.

1. Build Version & Iteration to Evaluate:
   - Build Version: v0.2.0-milestone2 (Tasks T7-T10 ML Audio Classifier, Non-Maximum Suppression Logic, Automated pytest V&V Suite, and Telemetry Visualizer)

2. Direct Link to GitHub Repository (Version Control):
   - https://github.com/michaeldavid90069-AiEngineer/COS660-michaeldavid90069-AiEngineer

3. Direct Link to Jira Project Management Board:
   - https://student-team-m1ljtdc1.atlassian.net/jira/software/projects/MAAN/boards/34?filter=&groupBy=none

4. Technical Summary of Milestone 2 Progress (Tasks T7-T10):
   - T7 (Feature Extraction): Built `extract_stft_mfcc_features` utilizing Librosa to extract an 18-dimensional STFT/MFCC matrix (Spectral Flux, Centroid, Rolloff, ZCR, RMS, and 13 MFCCs) per frame.
   - T8 (Classification & NMS Logic): Trained SVM (RBF) and kNN classifiers. Engineered a Non-Maximum Suppression (NMS) algorithm with a 250ms cooldown to isolate local RMS energy peaks, successfully eliminating clustered false-positive predictions.
   - T9 (Model Serialization & Inference): Serialized the best-performing SVM model using `joblib` and implemented `load_and_predict_timestamps` to return a 30 FPS frame-snapped array of beat cuts.
   - T10 (Pacing Density Filter & Engine Mode Router): Built routing gates to apply pacing filters ("High" vs "Low" density) and route execution modes.
   - Automated V&V Telemetry: Rewrote the `pytest` suite (`tests/test_milestone2.py`) utilizing a ±25 ms evaluation tolerance window. Passed all 4 unit tests at 100%. The serialized SVM (RBF) model exceeded all High-Level Design V&V thresholds: 80.52% Accuracy, 1.00 Precision, 0.84 Recall, and a 21.57 ms inference speed. Appended a `matplotlib` telemetry plot to verify ground-truth alignment.

5. AI Level 3 Disclosure:
   - Prompts used during code documentation, test-suite refinement, and matplotlib plotting: "t8 failed again... 0.666666 precision", "part of me thinks the Doctors are expecting a plotted chart", and "Generate the description test text for the GitHub commit". Original ML architecture design and pseudo-code are archived in the High-Level Design Document in the GitHub repository.

Best regards,
Michael David

Markdown
Hello Dr. Leis, Dr. Kachirski, and Gary Alvarez,

Please find attached my Milestone 1 screencast demonstration and GitHub push screenshot for the Audio Synchronizer Engine.

1. Build Version & Iteration to Evaluate:
   - Build Version: v0.1.0-milestone1 (Tasks T1-T6 Heuristic Baseline Engine, Automated pytest V&V Suite, Onset Telemetry Visualizer, and High-Level Design Document Draft 1)

2. Direct Link to GitHub Repository (Version Control):
   - https://github.com/michaeldavid90069-AiEngineer/COS660-michaeldavid90069-AiEngineer

3. Direct Link to Jira Project Management Board:
   - https://student-team-m1ljtdc1.atlassian.net/jira/software/projects/MAAN/boards/34?filter=&groupBy=none

4. Technical Summary of Milestone 1 Progress (Jira Issues MAAN-29, MAAN-30, MAAN-36, and MAAN-39 through MAAN-44 / Tasks T1-T6):

   - T1 / MAAN-39 (Environment & Packaging): Configured the isolated virtual environment (/content/venv), .gitignore, and locked dependencies (librosa 0.11.0, scikit-learn 1.6.1, moviepy 1.0.3, numpy 2.0.2, pytest 8.4.2) in requirements.txt with zero version conflicts.
   - T2 / MAAN-40 (Local Asset Ingestion): Implemented SignalProcessor.scan_asset_directory() using os.path to scan /assets and return absolute file paths for .mp4, .mp3, and .wav media.
   - T3 / MAAN-41 (Binary Header Validation): Implemented try-except binary signature validation (verifying RIFF, ID3/MPEG, and MP4 ftyp headers) that halts safely in < 500 ms when encountering corrupted or zero-byte files.
   - T4 / MAAN-42 (1D Mono Waveform Ingestion): Implemented SignalProcessor.ingest_mono_audio() via librosa.load() to flatten audio into a 1D float32 numpy array (shape=(110250,), sr=22050 Hz).
   - T5 & T6 / MAAN-43 & MAAN-44 (100ms Moving-Average Heuristic Engine & Frame-Snapped Timestamps): Configured Librosa onset strength extraction (n_fft=512, hop_length=256, center=False) with a 100ms sliding moving-average threshold and 30 FPS frame-interval snapping. All 4 automated pytest V&V unit tests pass (100% in 4.42s), completing beat detection in 34.61 ms and exporting exact ascending timestamps: [0.5, 1.0, 1.5, 2.0, 2.5, 3.0, 3.5, 4.0, 4.5].

5. AI Level 3 Disclosure:
   - Prompts used during code documentation and test-suite refinement:
 "lets take a look at Milestone (3 or 1)" and "can we use google colab to build this". 
Original COS550 baseline design and pseudo-code are archived in Appendix B of Gonzales_HighLevelDesignDocument_Draft1.pdf in the GitHub repository.

Best regards,
 David

 # Audio Synchronizer Engine (COS660 Capstone)
**Developer:** Michael David  
**Institution:** Full Sail University (Software Engineering)

## Project Overview & Hypothesis
The Audio Synchronizer Engine is an automated Python pipeline designed to ingest audio media, detect rhythmic beat transients, and dynamically composite video clips synchronized to those beats. 

**Core Hypothesis:** A supervised Machine Learning pipeline utilizing STFT/MFCC feature extraction and a Support Vector Machine (SVM) classifier can match or exceed the accuracy, precision, and recall thresholds of a traditional 100ms moving-average Heuristic DSP baseline for automated video synchronization.

---

## 🗂️ File Structure & Architecture
The repository is organized to separate source logic, serialized machine learning models, testing suites, and media assets.

```text
COS660-michaeldavid90069-AiEngineer/
│
├── assets/                  # Directory for reference .mp4, .mp3, and .wav media
├── models/                  # Serialized machine learning models
│   └── audio_classifier.pkl # Best-performing SVM-RBF model saved via joblib
│
├── src/                     # Core application logic
│   ├── ml_classifier.py     # ML feature extraction, NMS logic, and inference routing
│   └── signal_processor.py  # Binary validation and 1D mono waveform ingestion
│
├── tests/                   # Automated V&V test suites
│   ├── test_milestone1.py   # Heuristic baseline validation (±25ms tolerance)
│   └── test_milestone2.py   # ML pipeline validation (±25ms tolerance)
│
├── requirements.txt         # Locked environment dependencies (Librosa, Scikit-Learn, etc.)
└── README.md                # Project documentation
