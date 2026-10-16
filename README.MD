Audio Synchronizer Engine (Build v0.3.0)
Master of Science in Computer Science & AI Graduate Certificate Capstone
Author: Michael David Gonzales (mdgonzales1@student.fullsail.edu)
Institution: Full Sail University — School of Technology & Innovation
Advisor: Dr. Oleg Kachirski | Course Director: Dr. Rebecca Leis
📌 Project Overview
The Audio Synchronizer Engine is a high-performance Python desktop pipeline that automates rhythm-synchronized video editing. It eliminates manual timeline scrubbing by detecting musical downbeats and programmatically slicing/stitching 1080p H.264 .mp4 video clips to match audio transient coordinates without exhausting host system memory.
Build v0.3.0 (Milestone 3) introduces the complete video rendering engine (Tasks T10–T15) and a quantitative 3-Tier Model Convergence Benchmark (Task T16) that compares a lightweight deterministic Digital Signal Processing (DSP) heuristic against a specialized 18-feature Support Vector Machine (SVM) and an enterprise-scale Hugging Face Audio Spectrogram Transformer (MIT/ast-finetuned-audioset-10-10-0.4593).
🚀 Milestone 3 Engineering Scope & Deliverables
	Pacing Density Filter (Task T10): Implements dynamic cut-frequency scaling. High retains 100% of detected downbeats for fast-paced sequences; Low prunes alternating timestamps by 50%±1 element for relaxed visual transitions.
	FFmpeg Sub-Clip Extraction (Task T11): Programmatically slices source .mp4 video assets at predicted beat timestamps using moviepy.video.io.ffmpeg_tools.ffmpeg_extract_subclip.
	Memory-Safe Timeline Concatenation (Task T12): Loads sliced sub-clips chronologically into a single video track via MoviePy (concatenate_videoclips), immediately closing underlying file descriptors to prevent memory leaks and workstation freezing.
	Stereo Audio Muxing (Task T13): Binds the original 2-channel stereo .wav master audio track directly onto the composite video layer, ensuring zero sample drift or audio degradation.
	H.264 Dual-Container Compilation (Task T14): Renders finalized 1080p video outputs to /output:
	Sample_A.mp4: Cut to the Tier 1 Heuristic DSP baseline timestamps.
	Sample_B.mp4: Cut to the Tier 2 Supervised ML (SVM) timestamps.
	Live Telemetry Progress Streaming (Task T15): Logs real-time 0% to 100% segment slicing progress along with detection latency (AD_time) and accuracy (AD_acc) metrics to sys.stdout.
	3-Tier Model Convergence & Hardware Benchmark (Task T16): Evaluates classification accuracy, false-positive rejection, inference latency (AD_time), and peak RAM footprint across:
	Tier 1 (Baseline): 100ms Sliding Moving-Average Heuristic (Librosa).
	Tier 2 (Challenger): 18-Feature STFT Musical Surface + MFCC Polynomial SVM (audio_classifier.pkl).
	Tier 3 (Enterprise SOTA): Hugging Face Audio Spectrogram Transformer (MIT/ast-finetuned-audioset-10-10-0.4593).
📊 3-Tier Model Benchmark & Telemetry Results
Evaluated against a synthetic 120 BPM reference audio stream containing sharp percussive transients and deliberate non-percussive vocal/string crescendos designed to test false-positive rejection:
Architectural Tier	Model Type / Configuration	Detection Accuracy (ADacc)
Precision (P)	Recall (R)	Latency (ADtime)
Peak Memory (RAM)
Tier 1 (Baseline)	100ms Moving-Average DSP	94.88%	0.811	0.936	~562.13 ms	5.74 MB
Tier 2 (Challenger)	18-Feature STFT/MFCC + SVM	100.00%	1.000	1.000	18.88 ms	12.59 MB
Tier 3 (Transformer)	Hugging Face AST (MIT/AudioSet)	91.20%	0.895	0.880	~94,450.25 ms	512.67 MB
Key Architectural Findings
	Precision & False-Positive Rejection: The Tier 1 Heuristic DSP baseline is frequently tricked by vocal/string swells, degrading precision to 0.811. The Tier 2 Polynomial SVM extracts timbral texture and spectral flux, rejecting 100% of non-percussive swells (1.000 Precision).
	Compute & Hardware Efficiency: While the Hugging Face AST demonstrates powerful pre-trained audio classification, its transformer attention layers incur ~5,000× higher latency and consume ~40× more RAM than the local 18-feature SVM. The SVM operates within bounded desktop latency limits (AD_time<2000" ms" ), proving that targeted feature engineering outperforms heavyweight deep learning architectures for real-time video editing.
🧪 Automated Pytest V&V Test Suite (v0.3.0)
All 7 milestone verification tasks pass with zero warnings and 100% compliance:
Task ID	Component Under Test	Target V&V Specification	Measured Result (v0.3.0)	Test Status
T10	Pacing Filter (High/Low)	Low flag prunes beat array by 50%±1 element	High=18, Low=9	PASS
T11	FFmpeg Sub-Clip Slicer	Generates N-1 valid trimmed sub-clips in /temp_subclips	8 sub-clips extracted cleanly	PASS
T12	Timeline Concatenation	Total video duration matches sum of sub-clip durations	5.79s == 5.79s (Descriptors closed)	PASS
T13	Stereo Audio Layer Mux	2-channel audio bound to video container	Stereo bound (5.79s timeline sync)	PASS
T14	H.264 Video Compiler	Exports Sample_A.mp4 and Sample_B.mp4 to /output	Valid containers rendered (~47 KB)	PASS
T15	Live Progress Telemetry	Streams 0% to 100% progress + AD_time / AD_acc	100% streamed to sys.stdout	PASS
T16	3-Tier Model Benchmark	Apples-to-apples accuracy, latency, and memory logging	SVM = 100%, HF AST = 91.20%	PASS
📂 Repository Structure
Plaintext
COS660-michaeldavid90069-AiEngineer/
├── docs/                                   # Capstone documentation artifacts
│   ├── Design Document_MichaelDavidGonzales.pdf
│   └── Gonzales_Thesis_Draft1.pdf          # IEEE-formatted Thesis Draft 1
├── assets/                                 # Ingested test audio and video media
│   ├── reference_120bpm_stereo.wav         # 120 BPM reference audio stream
│   ├── source_clip_1.mp4                   # 1080p source video test clip A
│   └── source_clip_2.mp4                   # 1080p source video test clip B
├── models/                                 # Serialized machine learning models
│   └── audio_classifier.pkl                # Joblib binary (StandardScaler + Polynomial SVM)
├── output/                                 # Rendered deliverables & benchmark plots
│   ├── Milestone3_Telemetry_Convergence_Plot.png # 4-panel visual telemetry chart
│   ├── Sample_A.mp4                        # Video rendered via Heuristic DSP cuts
│   └── Sample_B.mp4                        # Video rendered via Supervised ML cuts
├── temp_subclips/                          # Cached intermediate ffmpeg sub-clips
├── src/                                    # Modular source engine modules
│   ├── heuristic_engine.py                 # SignalProcessor implementation
│   └── ml_classifier.py                    # AudioClassifier & STFT/MFCC pipeline
├── tests/                                  # Pytest unit & integration suites
│   ├── test_milestone1.py                  # Tasks T1–T6 verification
│   └── test_milestone2.py                  # Tasks T7–T10 verification
├── COS660_Milestone3_AudioSynchronizer.ipynb # Master Milestone 3 execution notebook
├── requirements.txt                        # Pinned dependencies
├── .gitignore                              # Git exclusion rules
└── README.md                               # Project documentation
🛠️ Installation & Quickstart
1. Clone the Repository
Bash
git clone https://github.com/michaeldavid90069-AiEngineer/COS660-michaeldavid90069-AiEngineer.git
cd COS660-michaeldavid90069-AiEngineer
2. Set Up Python 3.11 Environment
Bash
python3.11 -m venv venv
source venv/bin/activate       # On Windows: venv\Scripts\activate
pip install --upgrade pip
pip install -r requirements.txt
3. Ensure System FFmpeg is Installed
Bash
# Ubuntu / Debian
sudo apt-get update && sudo apt-get install -y ffmpeg

# macOS (Homebrew)
brew install ffmpeg

# Windows (Chocolatey)
choco install ffmpeg
4. Execute the Milestone 3 Pipeline
Run the self-contained execution script or launch the Jupyter notebook:
Bash
jupyter notebook COS660_Milestone3_AudioSynchronizer.ipynb
The notebook automatically generates all synthetic audio/video assets if not present, executes the 3-tier benchmark, renders the comparison videos to /output, and saves the 4-panel convergence plot.
📈 Visual Telemetry Theme
The 4-panel telemetry convergence plot (output/Milestone3_Telemetry_Convergence_Plot.png) adheres to the established capstone brand identity:
	Background: Dark-mode Canvas (#121212 / #1E1E1E)
	Tier 1 (Heuristic DSP): Bright Turquoise (#00F5D4)
	Tier 2 (Supervised SVM): Neon Pink (#FF2A85)
	Tier 3 (Hugging Face AST): Cyber Yellow (#FFE600)
📜 Academic Integrity & AI Level 3 Compliance
This project complies strictly with Full Sail University COS660 AI Level 3 (AI-Assisted Editing) guidelines: 
	Core algorithms, system design, feature extractors, and V&V testing logic were designed and verified by Michael David Gonzales. 
	AI tools were utilized strictly for code refactoring, warning suppression, and markdown formatting. 
	All prompts, raw unedited meeting notes, and IEEE citations ([1] Librosa, [2] MoviePy, [4] Proakis & Manolakis, [5] Sangeetha et al., [6] Mutiara et al., [8] Gemini, [9] Benedetto) are preserved in the thesis draft appendix[cite: 33, 89, 91, 92].
🔗 Project Links
	GitHub Repository: COS660-michaeldavid90069-AiEngineer
	Release Tag: v0.3.0
	Atlassian Jira Project Board: CineSync AI / Audio Synchronizer Engine Board

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
