# Audio Synchronizer Engine (AI vs. Heuristic Benchmark)

**Current Build:** `v0.1.0-milestone1` | **Automated V&V Status:** `4/4 Passing (100% in 4.42s)`  
**Author:** Michael David Gonzales  
**Program:** Artificial Intelligence Graduate Certificate Capstone (COS660) — Full Sail University  
**Capstone Advisor:** Dr. Oleg Kachirski | **Program Director:** Dr. Rebecca Leis | **Course Instructor:** Gary Alvarez  
**Project Management (Jira):** [MAAN Agile Scrum Board](https://student-team-m9p1d1.atlassian.net/jira/software/projects/MAAN/boards/34)

---

## 1. Project Overview & Research Hypothesis

The **Audio Synchronizer Engine** is an automated Python video editing and rhythmic beat-cutting pipeline originally prototyped in Advanced Software Engineering (COS550). For this Artificial Intelligence Capstone, the system is expanded into a head-to-head empirical benchmark comparing two distinct beat-detection architectures:

1. **Baseline Heuristic Engine (`SignalProcessor`):** A rule-based Digital Signal Processing (DSP) module that computes a 100 ms sliding moving-average volume transient threshold across a 1D mono waveform (`n_fft=512`, `hop_length=256`, `threshold_coefficient=1.35`) and snaps detected peaks to a 30 FPS video frame grid [1], [4].
2. **Trained Machine Learning Audio Classifier (`AudioClassifier`):** A supervised learning pipeline that segments audio via Short-Time Fourier Transform (STFT) windowing to extract an **18-dimensional acoustic feature vector per frame**—5 Musical Surface descriptors (spectral flux, spectral centroid, spectral rolloff, zero-crossing rate, and RMS energy) plus 13 Mel-Frequency Cepstral Coefficients (MFCCs)—classified via Support Vector Machines (Polynomial, RBF, and Sigmoid kernels) and Nearest Neighbors in Scikit-Learn [1], [5], [6].

### Hypotheses
* **Alternative Hypothesis:** The trained STFT Musical Surface + MFCC machine learning classifier will achieve higher rhythmic beat-cut classification accuracy, Precision while maintaining local inference time and earning higher blind within-subjects human synchronization ratings (1–5 Likert scale) than the rule-based moving-average heuristic baseline [5], [6].
* **Null Hypothesis:** There is no statistically or practically significant difference in beat-cut accuracy or human-perceived video synchronization quality between the trained machine learning classifier and the rule-based moving-average heuristic engine.

---

## 2. System & Folder Architecture

```text
COS660-michaeldavid90069-AiEngineer/
├── main.py                                      # CLI Pipeline Controller & Telemetry Orchestrator
├── requirements.txt                             # Locked Python dependencies (Librosa, Scikit-Learn, MoviePy, pytest)
├── .gitignore                                   # Excludes venv/, __pycache__/, and heavy raw media binaries
├── COS660_Milestone1_AudioSynchronizer.ipynb    # Google Colab Interactive Build & Telemetry Notebook
├── Gonzales_HighLevelDesignDocument_Draft1.pdf  # High-Level Design Document (IEEE Format)
├── src/
│   ├── heuristic_engine.py                      # Milestone 1 (T2–T6): Asset scanner, header check, 100ms DSP engine
│   ├── ml_classifier.py                         # Milestone 2 (T7–T10): 18-feature STFT/MFCC extractor & SVM/kNN model
│   └── video_compositor.py                      # Milestone 3 (T11–T15): FFmpeg subclip slicer & MoviePy H.264 exporter
├── models/
│   └── audio_classifier.pkl                     # Serialized best-performing Scikit-Learn classifier (Milestone 2)
├── assets/
│   └── reference_120bpm.wav                     # 120 BPM synthetic verification track & input media directory
├── output/
│   ├── Sample_A.mp4                             # Anonymized Heuristic render for blind A/B evaluation
│   └── Sample_B.mp4                             # Anonymized AI Classifier render for blind A/B evaluation
└── tests/
    └── test_milestone1.py                       # Automated pytest Verification & Validation (V&V) unit test suite
