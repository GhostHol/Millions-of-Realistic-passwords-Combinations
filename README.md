# Millions-of-Realistic-passwords-Combinations

```markdown
# 🛡️ Realistic Human Password Generator (RHPG)

## 📖 Table of Contents
- [📍 Background](#-background)
- [🧠 Why "Realistic"?](#-why-realistic)
- [⚙️ Architecture & Key Features](#-architecture--key-features)
- [🔬 15 Human Behavior Patterns](#-15-human-behavior-patterns)
- [🚀 Installation & Usage](#-installation--usage)
- [📊 Performance & Benchmark](#-performance--benchmark)
- [📚 Academic Foundations](#-academic-foundations)
- [⚠️ Disclaimer](#-disclaimer)

---

## 📍 Background

Testing the strength of password cracking algorithms (like Hashcat or John the Ripper) requires a dataset that represents how humans actually construct passwords. 

Standard random generators (`random_string()`) produce high-entropy strings like `xK9$mPq2`, which are **never** used by average users. RHPG bridges this gap by modeling human **cognitive bias**, generating billions of synthetic passwords with a statistical distribution closely resembling real-world breach databases (such as *RockYou*).

---

## 🧠 Why "Realistic"?

Human-created passwords are not random. They follow specific mental heuristics to make them memorable. RHPG simulates the 3 main pillars of human habits:

1. **Dictionary Dependency**
   Humans choose familiar words: `dragon`, `love`, `sunshine`.
2. **Predictable Mangling**
   To meet security requirements, humans make minimal modifications:
   - *Capitalization*: `Dragon`
   - *Leet Speak*: `dr4g0n`
   - *Appending*: `dragon123`, `dragon!`
3. **Contextual Anchoring**
   Using easily remembered information: Names (`john`), Years (`1998`), or keyboard patterns (`qwerty`).

> **Note:** This generator is designed to produce purely synthetic datasets. No real leaked passwords are stolen or hardcoded within it.

---

## ⚙️ Architecture & Key Features

RHPG is built on a *streaming pipeline* architecture capable of handling billions of records without triggering *Out of Memory* (OOM) errors.

```text
┌──────────────┐     ┌─────────────────┐     ┌───────────────────┐     ┌──────────────┐
│  RNG Engine  │────▶│ Pattern Selector│────▶│  Bloom Filter     │────▶│  Disk I/O    │
│ (Weighted)   │     │ (15 Human Rules)│     │  (Deduplication)  │     │  (Buffered)  │
└──────────────┘     └─────────────────┘     └───────────────────┘     └──────────────┘
                                                      │
                                              ┌───────▼────────┐
                                              │ State Manager  │
                                              │ (Checkpointing)│
                                              └────────────────┘
```

### System Features:
- **🎯 Probabilistic Pattern Modeling:** Each pattern has a weight percentage based on security research (e.g., the `word+number` pattern has a 25% probability of appearing).
- **🌸 Bloom Filter Deduplication:** Uses a highly efficient space-probability algorithm. Capable of checking billions of duplicate passwords using only ~1.8GB of RAM/Disk space, avoiding the *birthday problem paralysis*.
- **💾 Stateful Processing (Resume):** Can be stopped (Ctrl+C) and resumed exactly from the last position without creating duplicate files or duplicate passwords in the `.txt` file.
- **🧮 Memory-Mapped Files (mmap):** Extremely fast disk access for the Bloom Filter without loading the entire file into RAM.

---

## 🔬 15 Human Behavior Patterns

This generator doesn't just shuffle strings; it calls specific functions that simulate 15 common user habit patterns:

| No | Behavior Pattern | Example Output | Proportion |
|:--:|------------------|----------------|:----------:|
| 1 | Base Word + Number | `dragon123`, `shadow99` | 25.0% |
| 2 | Base Word + Year | `password1998`, `love2005` | 15.0% |
| 3 | Name + Number | `john123`, `sari44` | 12.0% |
| 4 | *Keyboard Walking* | `qwerty123`, `asdfgh` | 8.0% |
| 5 | Numeric Only | `123456`, `11111111` | 7.0% |
| 6 | *Capitalization* | `Dragon123`, `Shadow` | 6.0% |
| 7 | Word + Symbol | `password!`, `@dragon` | 5.0% |
| 8 | *Leet Speak* | `p@ssw0rd`, `h4ck3r` | 4.0% |
| 9 | Two Words Combined | `sunmoon`, `darkforest` | 3.5% |
| 10| Name + Year | `budi1995`, `emma2000` | 3.0% |
| 11| Common Phrases | `iloveyou`, `letmein` | 2.5% |
| 12| Repeated Words | `catcat`, `lololo` | 2.0% |
| 13| Predictable Complex | `Dragon123!`, `Word99@me` | 2.5% |
| 14| Indonesian Words | `cinta123`, `jakarta99` | 2.5% |
| 15| Name + Word | `johnlove`, `darkrudi` | 2.0% |

*(Proportions are adjusted based on global data breach statistical distributions)*

---

## 🚀 Installation & Usage

### Prerequisites
- Python 3.8 or higher
- Free disk space: Minimum ~15 GB (for a 1 Billion password target + system files)

### Running the Generator

**1. Interactive Mode (Recommended)**
```bash
python password_generator.py
```
*The system will automatically detect if there is a previously stopped process and ask whether to Resume or start Fresh.*

**2. Command Line Interface (CLI)**
```bash
# Start from scratch (erases old progress)
python password_generator.py --fresh

# Resume a stopped process
python password_generator.py --resume

# View current progress statistics
python password_generator.py --status

# Clean up all output and temporary files
python password_generator.py --clean
```

### Configuration
You can adjust the target and generator behavior inside the `Config` class in the `.py` file:
```python
class Config:
    TOTAL_PASSWORDS = 100_000_000  # Target number of unique passwords
    OUTPUT_FILE = "dataset_password_realistis.txt"
    RANDOM_SEED = 42               # For research reproducibility
    BLOOM_FPR = 0.001              # Bloom Filter False Positive Rate (0.1%)
```

---

## 📊 Performance & Benchmark

Testing conducted on a standard system (Consumer-grade Laptop). 
*(Note: Speed heavily depends on the *False Positive Rate* of duplicates generated by the breadth of the *wordlist* used).*

| Target Scale | Estimated Time | `.txt` File Size | Total Disk Needed |
|:------------:|:--------------:|:-----------------:|:-----------------:|
| 1 Million    | ~30 seconds    | ~10 MB            | ~200 MB           |
| 10 Million   | ~5 minutes     | ~100 MB           | ~500 MB           |
| 100 Million  | ~45 minutes    | ~1 GB             | ~3 GB             |
| 1 Billion    | ~7 hours       | ~10 GB            | ~13 GB            |

**Generated Files:**
- `dataset_password_realistis.txt` : The main file containing unique passwords (1 password per line).
- `.bloom_filter.bin` : *(Temporary)* Deduplication support file (Auto-deleted upon completion).
- `.generator_state.bin` : *(Temporary)* Checkpoint for resuming (Auto-deleted upon completion).

---

## 📚 Academic Foundations

The algorithms and patterns in this research are built upon prominent cybersecurity literature:

1. **NIST SP 800-63B** - *Digital Identity Guidelines* (Regarding password entropy and human memory).
2. **Weir et al. (2010)** - *"Using Secret-Sharing for Detecting Compromised Passwords"*. (Regarding probabilistic context-free grammar structures for passwords).
3. **Florêncio & Herley (2007)** - *"A Large-Scale Study of Web Password Habits"*. (Regarding password length distribution and repetition).
4. **Bloom, B. H. (1970)** - *Space/Time Trade-offs in Hash Coding with Allowable Errors*. (The theoretical foundation of the Bloom Filter used in deduplication).

---

## ⚠️ Disclaimer

This project and repository are developed **purely for academic, educational, and defensive security research purposes** (Final Thesis). 

This generator **DOES NOT** store, distribute, or use real leaked credentials. All generated data is 100% synthetic. It is strictly forbidden to use this tool or its generated datasets for unauthorized activities, brute-forcing accounts without permission, or any other malicious endeavors. Use it ethically and responsibly.

---

<div align="center">
  <sub>Built with 🐍 Python for Information Security Research Purposes</sub>
</div>
```
