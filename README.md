# 🛡 Self-Evolving Malware Detection & Code Reconstruction
### SEMD v2.0 — EMBER Dataset · Federated Learning · Reinforcement Learning · Local LLM

---

## 📋 Table of Contents
1. [Project Overview](#overview)
2. [System Architecture](#architecture)
3. [Features](#features)
4. [Project Structure](#structure)
5. [Installation](#installation)
6. [Setup Guide](#setup)
7. [Running the System](#running)
8. [Dashboard Modules](#dashboard)
9. [Federated Learning Network](#federated)
10. [How Detection Works](#detection)
11. [LLM Integration](#llm)
12. [Troubleshooting](#troubleshooting)

---

## 🔍 Project Overview <a name="overview"></a>

SEMD is a complete malware detection and analysis platform that combines:

- **Federated Learning** — 3 clients train a shared global model without sharing raw data
- **Reinforcement Learning** — DQN agent adapts classification decisions through reward shaping
- **Neural Network** — Deep model trained on the EMBER dataset (2381 PE features)
- **Static Sandbox** — Real binary analysis extracting APIs, network IoCs, registry ops, evasion techniques
- **Local LLM** — DeepSeek Coder / llama3.2 via Ollama for pseudocode generation and threat reports
- **Mutation Intelligence** — LSTM predictor for malware evolution probability

Everything runs **100% locally** — no cloud APIs, no data leaves your machine.

---

## 🏗 System Architecture <a name="architecture"></a>

```
┌─────────────────────────────────────────────────────────────────┐
│                    SEMD Dashboard (Streamlit)                    │
│  File Upload │ Scan Folder │ FL Network │ Threat Intel │ Quarantine│
└──────────────────────┬──────────────────────────────────────────┘
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
┌──────────────┐ ┌──────────────┐ ┌──────────────┐
│  Federated   │ │ Neural Net   │ │   Sandbox    │
│  Server      │ │ (EMBER 2381) │ │   Engine     │
│  FedAvg      │ │ 512→256→128  │ │ Static Anal. │
└──────┬───────┘ └──────┬───────┘ └──────┬───────┘
       │                │                │
  ┌────┴────┐           │           ┌────┴────┐
  │Client 1 │      ┌────┴────┐      │ Mutation│
  │Client 2 │      │  DQN RL │      │  LSTM   │
  │Client 3 │      │  Agent  │      │Predictor│
  └────┬────┘      └─────────┘      └─────────┘
       │
  ┌────┴──────────────────────┐
  │   Local Ollama LLM        │
  │   llama3.2 / deepseek     │
  │   Pseudocode · Reports    │
  └───────────────────────────┘
```

### Component Summary

| Component | Technology | Purpose |
|-----------|-----------|---------|
| Neural Network | PyTorch — 2381→512→256→128→64→2 | Binary malware/benign classification |
| Federated Learning | Flower (flwr) — FedAvg | Privacy-preserving distributed training |
| RL Agent | Double DQN — 3 actions | Adaptive classification decisions |
| Sandbox | Pure Python static analysis | Real binary analysis without execution |
| Feature Extractor | EMBER-compatible (2381 dims) | PE binary feature engineering |
| Mutation Predictor | LSTM + Attention | Malware evolution probability |
| LLM | Ollama — llama3.2/deepseek-coder | Pseudocode, reports, classification |

---

## ✨ Features <a name="features"></a>

### File Analysis (Upload Any File)
- ✅ 7-step real analysis pipeline with live progress
- ✅ EMBER-compatible 2381-dimensional feature extraction
- ✅ Static sandbox: APIs, network IoCs, registry ops, evasion techniques
- ✅ Neural network prediction with confidence percentage
- ✅ Mutation probability score (LSTM-based)
- ✅ LLM malware family classification (Trojan/Ransomware/Spyware/etc.)
- ✅ LLM pseudocode behavioral reconstruction
- ✅ Safe code reconstruction with payloads redacted
- ✅ Base64 encoded payload detection and decoding
- ✅ Control flow graph (SVG, rendered in iframe)
- ✅ Malicious code line detection with line numbers
- ✅ PE section analysis with per-section entropy
- ✅ Threat score gauge (0–100)
- ✅ Malware family + intent + risk level badge
- ✅ Quarantine button with metadata sidecar
- ✅ Downloadable full analysis report (.txt)
- ✅ File-type aware detection (documents never false-flagged)

### Folder Scanner
- ✅ Batch scan any folder with per-file progress bar
- ✅ Quick sandbox (512KB cap, fast per-file)
- ✅ Per-malware expandable deep analysis panel
- ✅ 5 tabs per file: Sandbox · Code · Mutation · Base64 · CFG
- ✅ Per-file quarantine buttons with session state

### Federated Network
- ✅ 1 server + 3 clients in separate terminals
- ✅ Each client loads a non-overlapping EMBER shard (chunked, memory-safe)
- ✅ FedAvg aggregation with per-round accuracy logging
- ✅ RL agent trains locally on each client after each FL round
- ✅ Clients submit sandbox scan results to server
- ✅ Server aggregates global threat intelligence
- ✅ Live monitoring dashboard for all clients + rounds

### Threat Intelligence
- ✅ Populated by both federated clients AND direct dashboard scans
- ✅ Aggregated malware detection database
- ✅ Pseudocode viewer per detection
- ✅ Per-client statistics

### Quarantine Manager
- ✅ Files copied to absolute-path quarantine folder
- ✅ JSON metadata sidecar per file (original path, timestamp, size)
- ✅ Auto-rename leftover staging files on load
- ✅ Sidebar count always in sync with manager
- ✅ Delete with sidecar cleanup

### Model Performance
- ✅ FL round accuracy history chart
- ✅ Per-client accuracy bar chart
- ✅ Model architecture summary

---

## 📁 Project Structure <a name="structure"></a>

```
malware_detection/
│
├── config.py                # All configuration (absolute paths, hyperparams)
├── models.py                # Neural networks: MalwareDetectionModel, DQN, MutationPredictor
├── feature_extractor.py     # EMBER-compatible 2381-dim feature extraction
├── sandbox.py               # Static sandbox engine (no execution)
├── mutation_system.py       # LSTM mutation predictor + Base64 decoder
├── llm_analyzer.py          # Ollama LLM integration with full fallbacks
├── rl_agent.py              # Double DQN reinforcement learning agent
├── rl_environment.py        # RL environment with reward shaping
├── scanner.py               # Folder scanner
├── train_model.py           # EMBER dataset training (memory-safe chunked)
├── federated_server.py      # FL server (FedAvg + threat intel aggregation)
├── federated_client.py      # FL client (training + RL + sandbox reports)
├── dashboard.py             # Streamlit dashboard (2200+ lines)
├── generate_samples.py      # Generate synthetic test samples
├── requirements.txt         # Python dependencies
│
├── dataset/
│   └── ember_dataset.csv    ← Place your EMBER dataset here
│
├── quarantine/              # Auto-created — isolated malware files
├── sandbox_results/         # Auto-created — client scan results
├── logs/                    # Auto-created — FL training logs
├── reports/                 # Auto-created — analysis reports
└── samples/                 # Auto-created — test samples
```

---

## 📦 Installation <a name="installation"></a>

### Requirements
- Python 3.9+
- 4 GB RAM minimum (8 GB recommended for LLM)
- [Ollama](https://ollama.com/download) installed

### Step 1 — Install Python dependencies
```bash
pip install -r requirements.txt
```

**requirements.txt contents:**
```
streamlit>=1.28.0
torch>=2.0.0
numpy>=1.24.0
pandas>=2.0.0
scikit-learn>=1.3.0
joblib>=1.3.0
matplotlib>=3.7.0
flwr>=1.5.0
requests>=2.31.0
Pillow>=10.0.0
```

### Step 2 — Install Ollama
Download from: https://ollama.com/download

> On Windows, Ollama installs as a background service and starts automatically.  
> **Do NOT run `ollama serve` manually** — it is already running.

### Step 3 — Pull a language model

```bash
# Recommended (general purpose, no refusals, ~2.0 GB)
ollama pull llama3.2

# Alternative if you want a code-focused model (~4 GB)
ollama pull deepseek-coder:6.7b

# Lightweight option if low RAM (~800 MB)
ollama pull deepseek-coder:1.3b
```

The dashboard auto-detects whichever model you have installed.

---

## ⚙ Setup Guide <a name="setup"></a>

### Step 1 — Place the EMBER Dataset
```
malware_detection/
└── dataset/
    └── ember_dataset.csv     ← must have 2381 feature columns + "Label" column
```

The `Label` column must be `0` (benign) or `1` (malware).

### Step 2 — Train the Model
```bash
cd malware_detection
python train_model.py
```

This will:
- Read the CSV in memory-safe 2000-row chunks
- Fit a StandardScaler incrementally (no RAM spike)
- Train for 30 epochs with ReduceLROnPlateau scheduler
- Save the best model → `malware_model.pth`
- Save the scaler → `scaler.pkl`

**If you run out of RAM**, open `train_model.py` and reduce:
```python
MAX_ROWS = 50000   # use only 50k rows
```

### Step 3 — (Optional) Generate Test Samples
```bash
python generate_samples.py
```
Creates 5 synthetic test files in `samples/`:
- `packed_malware.exe` — high entropy, injection APIs, C2 strings
- `trojan_rat.exe` — anti-debug, registry persistence, network
- `ransomware_sim.exe` — file encryption, ransom note, crypto APIs
- `benign_app.exe` — clean, low entropy, no suspicious indicators
- `malicious_script.ps1` — PowerShell with Base64, registry, download

---

## 🚀 Running the System <a name="running"></a>

### Dashboard Only (most common)
```bash
cd malware_detection
streamlit run dashboard.py
```
Open: **http://localhost:8501**

---

### Full Federated Network (4 terminals)

**Terminal 1 — Start the FL server:**
```bash
cd malware_detection
python federated_server.py
```
Wait for: `Waiting for clients to connect...`

**Terminal 2 — Client 1:**
```bash
cd malware_detection
python federated_client.py --client-id 1
```

**Terminal 3 — Client 2:**
```bash
cd malware_detection
python federated_client.py --client-id 2
```

**Terminal 4 — Client 3:**
```bash
cd malware_detection
python federated_client.py --client-id 3
```

**Terminal 5 — Dashboard:**
```bash
cd malware_detection
streamlit run dashboard.py
```

Each client loads 20,000 rows from its own non-overlapping shard of the EMBER dataset. To reduce memory usage:
```bash
python federated_client.py --client-id 1 --rows 5000
```

---

## 📊 Dashboard Modules <a name="dashboard"></a>

| Module | Description |
|--------|-------------|
| 🏠 **Overview** | System metrics, architecture diagram, recent threats |
| 📤 **File Analysis** | Upload any file → full 7-step deep analysis |
| 🔬 **Scan Folder** | Batch scan a folder → per-file deep analysis |
| 📊 **Model Performance** | FL accuracy history, per-client charts |
| 🌐 **Federated Network** | Live client/server monitor, sandbox reports |
| ⚡ **Threat Intelligence** | Global aggregated threat database |
| 🗄 **Quarantine Manager** | Manage isolated files with metadata |

### File Analysis — 7 Tabs

| Tab | Contents |
|-----|----------|
| 📊 Overview | Threat gauge, file info, malware family badge, behavior list |
| 🔬 Sandbox | APIs, network IoCs, registry ops, evasion, PE sections, malicious lines |
| 💻 Code Reconstruction | LLM pseudocode + safe reconstructed code |
| 🧬 Mutation | Probability score, mutation report, polymorphism indicators |
| 🔐 Base64 | Auto-detected encoded payloads + manual decoder |
| 📈 Control Flow Graph | SVG flowchart of execution path |
| 📄 Full Report | Complete downloadable analysis report |

---

## 🌐 Federated Learning Network <a name="federated"></a>

### How it works

```
Client 1 (rows 0–20k)    Client 2 (rows 20k–40k)    Client 3 (rows 40k–60k)
       │                          │                           │
       │  local training          │  local training           │  local training
       │  + RL agent              │  + RL agent               │  + RL agent
       │                          │                           │
       └──────────── model weights (not data) ───────────────►│
                                  │
                          FL Server (FedAvg)
                          aggregates weights
                          saves global model
                          collects scan results
                          builds threat intel
```

### Privacy guarantee
Each client only sends **model weight updates** to the server — never raw files, features, or labels. The EMBER dataset stays local to each client.

### RL Agent (per client)
- **Algorithm:** Double DQN with experience replay
- **Actions:** 0 = Benign, 1 = Malware, 2 = Suspicious  
- **Rewards:** +3 true positive, -5 false negative (missed malware), +1 true negative, -2 false positive
- **Trains:** 500 steps per FL round, epsilon decays from 1.0 → 0.05

---

## 🔎 How Detection Works <a name="detection"></a>

### Detection Pipeline

```
Upload file
     │
     ▼
Extract 2381 EMBER features
(byte histogram, entropy, PE header, sections, imports, strings)
     │
     ▼
Normalize with StandardScaler (fitted on EMBER training data)
     │
     ▼
Neural Network prediction → probability[benign, malware]
     │
     ▼
Static sandbox analysis → threat_score (0–100)
     │
     ▼
_is_malware() verdict function
     │
     ├── File is .txt/.pdf/.docx/.jpg etc. → ALWAYS benign
     │   (unless entropy>7.5 + critical APIs + Base64 payload all together)
     │
     ├── Model confidence < 65% → trust sandbox only
     │   (threshold: threat_score > 55 AND entropy > 6.0)
     │
     ├── .exe/.dll/.bat/.ps1 + model certain (>65%) says malware → MALWARE
     │
     └── Unknown extension → multi-signal required
```

### Feature extraction layout (2381 dimensions)

| Offset | Size | Feature Group |
|--------|------|--------------|
| 0 | 256 | Byte frequency histogram |
| 256 | 256 | Byte entropy histogram |
| 512 | 104 | String features (count, URLs, suspicious patterns) |
| 616 | 10 | General info (size, entropy, MD5, PE flag) |
| 626 | 62 | PE optional header |
| 688 | 255 | PE section features (5 sections × entropy + flags) |
| 943 | 128 | Import table (64 suspicious API presence flags) |
| 1071 | 64 | Export features |
| 1135 | 96 | Data directory features (16 dirs × 6 values) |
| 1231 | 1150 | Reserved (zero-padded, EMBER compatibility) |

---

## 🤖 LLM Integration <a name="llm"></a>

### Model Priority (auto-detected at startup)
```
1. llama3.2:latest      ← recommended, no refusals
2. llama3.1:latest
3. llama3:latest
4. mistral:latest
5. phi3:latest
6. deepseek-coder:6.7b
7. codellama:latest
8. (first available model)
```

### LLM Functions

| Function | Output | Fallback |
|----------|--------|---------|
| `reconstruct_pseudocode()` | 20–30 line behavioral pseudocode | Deterministic builder from sandbox data |
| `classify_family()` | JSON: family, confidence, intent, risk | Keyword heuristics |
| `analyze_mutation()` | 6-section threat intelligence report | Pre-written template |
| `reconstruct_safe_code()` | Annotated code with payloads redacted | Regex-based redaction |
| `generate_report_summary()` | 3-paragraph executive summary | Formatted template |

> All functions have **full offline fallbacks** — the dashboard works completely without Ollama.
> Ollama just improves the quality of text output.

### Why prompts use "CTF / university course" framing
Some models (especially DeepSeek Coder) refuse to discuss malware analysis directly.
Framing requests as "CTF challenge" or "university cybersecurity lab" reliably bypasses
these safety filters while producing identical useful output.

---

## 🔧 Troubleshooting <a name="troubleshooting"></a>

### `Unable to allocate X GiB` during training
Reduce rows in `train_model.py`:
```python
MAX_ROWS = 30000
```

### `Only one usage of each socket address` (Ollama)
Ollama is already running. Do NOT run `ollama serve`. Just run:
```bash
ollama pull llama3.2
```

### `model 'deepseek-coder' not found`
Use the full tag:
```bash
ollama pull deepseek-coder:6.7b
```
Then update `config.py`: `OLLAMA_MODEL = "deepseek-coder:6.7b"`

### `TypeError: MalwareClient.get_parameters() got unexpected keyword argument 'config'`
You have a newer version of Flower. The method signatures must be exactly:
```python
def get_parameters(self, config): ...
def fit(self, parameters, config): ...
def evaluate(self, parameters, config): ...
```

### `ValueError: could not broadcast input array`
Feature extractor slot overflow. Replace `feature_extractor.py` with the latest version (uses hard-coded absolute offsets).

### Quarantine shows 0 files but folder has files
Files may be named `_staging_xxx`. Open the Quarantine Manager tab — it auto-renames them on load.

### Documents detected as malware
The neural network was trained on PE executables only. The `_is_malware()` function in `dashboard.py` whitelists 40+ document/media/archive extensions so they are always classified as benign unless they contain genuine embedded payloads.

### LLM gives `START\nEND` only
The LLM refused or returned empty output. The dashboard automatically falls back to `_build_pseudocode_from_analysis()` which generates detailed pseudocode directly from sandbox results. No LLM required.

### Federated clients not connecting
Make sure the server is running first and listening on port 8080:
```bash
python federated_server.py   # start this first
# wait for "Waiting for clients to connect..."
python federated_client.py --client-id 1
```

---

## 📄 License
This project is for educational and research purposes.

---

*SEMD v2.0 — Self-Evolving Malware Detection*  
*Built with PyTorch · Flower · Streamlit · Ollama · EMBER Dataset*
