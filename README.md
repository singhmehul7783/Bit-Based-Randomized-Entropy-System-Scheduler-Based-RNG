<p align="center">
  <img src="https://img.shields.io/badge/language-Java-orange?style=for-the-badge&logo=openjdk&logoColor=white" alt="Java">
  <img src="https://img.shields.io/badge/license-Custom%20Restrictive-red?style=for-the-badge" alt="License: Custom Restrictive">
  <img src="https://img.shields.io/badge/JDK-8%2B-green?style=for-the-badge" alt="JDK 8+">
  <img src="https://img.shields.io/badge/NIST%20SP%20800--22-30%2F30%20PASSED-brightgreen?style=for-the-badge" alt="NIST: 30/30 Passed">
  <img src="https://img.shields.io/badge/build-passing-brightgreen?style=for-the-badge" alt="Build: Passing">
</p>

# 🎲 BBRES-RNG — Bit-Based Randomized Entropy System Scheduler-Based RNG

> A multithreaded pseudo-random number generator that harvests **real-time entropy from OS thread-scheduling chaos** — no mathematical seed, no deterministic shortcut.

<p align="center">
  <b>✅ 30/30 Statistical Tests Passed</b> &nbsp;|&nbsp; <b>🏆 Outperforms Java Math.random()</b> &nbsp;|&nbsp; <b>🔒 Matches Java SecureRandom</b>
</p>

---

## 📌 What Is This?

**BBRES-RNG** is a custom-built Java PRNG that takes a fundamentally different approach to generating random numbers. Instead of relying on standard library algorithms or fixed mathematical seeds, it deliberately creates controlled concurrency and treats the **unpredictable timing behavior of the operating system's thread scheduler** as its entropy source.

Every number it produces is shaped by the live, non-deterministic state of the host machine — making each output practically unrepeatable.

---

## 💡 Why It's Different

| Traditional PRNGs | BBRES-RNG |
|---|---|
| Seed-based (deterministic from the same seed) | Entropy-based (non-deterministic by design) |
| Use mathematical formulas (LCG, Mersenne Twister, etc.) | Harvests timing noise from OS thread scheduling |
| Reproducible given the same initial state | Practically unreproducible — tied to real-time system state |
| Single-threaded execution | Multi-threaded concurrency architecture |

---

## 🏆 Validation Results — Comparative Benchmark

BBRES-RNG has been independently validated against a comprehensive 30-test battery spanning NIST SP 800-22, extended statistical tests, spectral analysis, adversarial ML attacks, cryptographic wrapper validation, and integer-level distribution tests. It was benchmarked head-to-head against Java's two standard RNG implementations.

### Overall Scorecard

| RNG System | Tests Passed | Verdict | Failed Tests |
|---|---|---|---|
| **BBRES-RNG** | **30 / 30** | ✅ **ARCHITECTURE ACCEPTED** | None |
| Java `SecureRandom` | **30 / 30** | ✅ ARCHITECTURE ACCEPTED | None |
| Java `Math.random()` | **28 / 30** | ⚠️ REVIEW NEEDED | Maurer's Universal Statistical, Gap Test (KS) |

> **BBRES-RNG matches the cryptographic-grade `SecureRandom` with a perfect score, and outperforms `Math.random()` which failed 2 tests.**

### Test Parameters

| Parameter | Value |
|---|---|
| Bit Sample Size | 2,000,000 bits |
| Integer Sample Size | 100,000 integers [0..999] |
| Total Tests per RNG | 30 |
| Significance Level (α) | 0.01 |

---

### Detailed Test Results — BBRES-RNG (30/30 ✅)

#### NIST SP 800-22 Core Tests (9/9 ✅)

| Test | p-value | Result |
|---|---|---|
| Frequency (Monobit) | 0.094324 | ✅ PASS |
| Block Frequency (M=128) | 0.923799 | ✅ PASS |
| Runs Test | 0.323306 | ✅ PASS |
| Longest Run of Ones | 0.469160 | ✅ PASS |
| Cumulative Sums (Forward) | 0.178039 | ✅ PASS |
| Cumulative Sums (Reverse) | 0.163651 | ✅ PASS |
| Approximate Entropy (m=2) | 0.053664 | ✅ PASS |
| Serial (m=2) — δ1 | 0.151995 | ✅ PASS |
| Serial (m=2) — δ2 | 0.324972 | ✅ PASS |

#### Extended NIST & Uniformity Tests (6/6 ✅)

| Test | p-value | Result |
|---|---|---|
| Maurer's Universal Statistical | 0.981707 | ✅ PASS |
| Poker Test (m=4) | 0.479188 | ✅ PASS |
| Random Excursion Variant | 0.500000 | ✅ PASS |
| Byte-Level Chi-Square | 0.468109 | ✅ PASS |
| Nibble-Level Chi-Square | 0.479188 | ✅ PASS |
| 2-Bit Pair Distribution | 0.386897 | ✅ PASS |

#### Spectral, Adversarial & Cryptographic Tests (5/5 ✅)

| Test | p-value | Result |
|---|---|---|
| Discrete Fourier Transform (Periodicity) | 0.227460 | ✅ PASS |
| Frequency Prediction (w=8) | 0.884165 | ✅ PASS |
| ML Attack (LogReg, w=16) | 0.579260 | ✅ PASS |
| Pattern Repetition (w=53) | 1.000000 | ✅ PASS |
| SHA-256 CSPRNG Wrapper | 1.000000 | ✅ PASS |

#### Integer Distribution & Sequence Tests (10/10 ✅)

| Test | p-value | Result |
|---|---|---|
| Chi-Square Uniformity | 0.475159 | ✅ PASS |
| Mean (Z-test) | 0.398873 | ✅ PASS |
| Variance (Chi-sq) | 0.842551 | ✅ PASS |
| Binned Goodness-of-Fit | 0.756309 | ✅ PASS |
| Birthday Spacing | 1.000000 | ✅ PASS |
| Coupon Collector | 0.500000 | ✅ PASS |
| Runs Up/Down | 0.994013 | ✅ PASS |
| Lag-1 Autocorrelation | 0.418097 | ✅ PASS |
| Gap Test (KS) | 0.332555 | ✅ PASS |
| Permutation (t=5) | 0.703417 | ✅ PASS |

---

### Entropy Quality Comparison

| Metric | BBRES-RNG | SecureRandom | Math.random() | Theoretical Max |
|---|---|---|---|---|
| Shannon Entropy (8-bit) | **7.999260** | 7.999311 | 7.999233 | 8.000000 |
| Min-Entropy (8-bit) | **7.883082** | 7.828281 | 7.879001 | 8.000000 |
| Transition Rate | **0.500348** | 0.499482 | 0.500284 | 0.500000 |
| Bit Balance (1s : 0s) | 50.06% : 49.94% | 49.97% : 50.03% | 50.02% : 49.98% | 50% : 50% |

> BBRES-RNG achieves **99.991% of maximum Shannon entropy** and demonstrates the **highest min-entropy** (7.883) among all three RNGs tested, indicating superior worst-case unpredictability.

### Autocorrelation Profile (Bits) — Near-Zero Across All Lags

| Lag | BBRES-RNG | SecureRandom | Math.random() |
|---|---|---|---|
| 1 | -0.0007 | +0.0010 | -0.0006 |
| 2 | -0.0009 | -0.0005 | -0.0002 |
| 8 | -0.0000 | -0.0011 | -0.0002 |
| 32 | -0.0010 | +0.0002 | +0.0001 |
| 256 | -0.0003 | -0.0002 | +0.0007 |

All three RNGs show near-zero autocorrelation at all measured lags, confirming the absence of serial dependence in their output streams.

### Where Math.random() Failed (and BBRES-RNG Didn't)

| Test | Math.random() | BBRES-RNG | Why It Matters |
|---|---|---|---|
| **Maurer's Universal Statistical** | ❌ p=0.0083 | ✅ p=0.9817 | Detects compressibility — failure suggests subtle internal patterns in the LCG-based output |
| **Gap Test (KS)** | ❌ p=0.0005 | ✅ p=0.3326 | Measures spacing between repeated values — failure indicates non-uniform gap distribution |

---

## ⚙️ How It Works

The system generates random numbers through a three-stage pipeline:

### 1. Thread Spawning & Controlled Race Conditions
Multiple worker threads are launched simultaneously. The OS scheduler decides their execution order — a process that is inherently unpredictable and varies by microseconds based on system load, CPU state, and kernel-level scheduling decisions.

### 2. Timing-Based Entropy Collection
Each worker thread captures fine-grained timing data during its execution. The microsecond-level variations between thread timings become raw entropy input.

### 3. Bitwise Mixing & Aggregation
The collected timing results are aggregated and processed through a complex sequence of **XOR sums and bit shifts**, producing a single random bit per cycle. These bits are then assembled to construct the final random number within the requested range.

```
┌──────────────────────────────────────────────────────┐
│                    BBRES-RNG Pipeline                 │
│                                                      │
│  ┌─────────┐   ┌─────────┐   ┌─────────┐            │
│  │Thread 1 │   │Thread 2 │   │Thread N │  ← Spawn   │
│  └────┬────┘   └────┬────┘   └────┬────┘            │
│       │             │             │                   │
│       ▼             ▼             ▼                   │
│  ┌──────────────────────────────────────┐            │
│  │    OS Thread Scheduler (Entropy)     │            │
│  └──────────────────┬───────────────────┘            │
│                     │                                │
│                     ▼                                │
│  ┌──────────────────────────────────────┐            │
│  │  Timing Data Collection & Capture    │            │
│  └──────────────────┬───────────────────┘            │
│                     │                                │
│                     ▼                                │
│  ┌──────────────────────────────────────┐            │
│  │  Bitwise Mixing (XOR + Bit Shifts)   │            │
│  └──────────────────┬───────────────────┘            │
│                     │                                │
│                     ▼                                │
│              [ Random Output ]                       │
└──────────────────────────────────────────────────────┘
```

---

## 🏗️ Project Structure

```
Bit-Based-Randomized-Entropy-System-Scheduler-Based-RNG/
├── src/
│   ├── Main.java                              # Entry point & usage examples
│   └── bbresRNG/
│       ├── RNG.java                           # Primary API — generates numbers in a [min, max] range
│       ├── randomBitGeneratorModifiedRoot.java # Core bit generator — orchestrates thread spawning
│       ├── modRandomBitGenRNG.java            # Modified worker thread implementation
│       └── RandomBitGenRNG.java               # Base worker thread implementation
├── docs/                                      # Documentation & validation reports
├── LICENSE                                    # Custom Restrictive License
└── README.md
```

### Module Breakdown

| Class | Responsibility |
|---|---|
| `RNG.java` | Public-facing API. Accepts `(min, max)` range, concurrency parameter `n`, and randomization technique selector `randTech`. |
| `randomBitGeneratorModifiedRoot.java` | Orchestrates the generation of a single random bit (0 or 1). Contains two distinct generation methods: `generateRandBitG1` and `generateRandBitG2`. |
| `modRandomBitGenRNG.java` | Worker thread variant — collects timing data in parallel execution. |
| `RandomBitGenRNG.java` | Base worker thread — launched simultaneously for entropy harvesting via non-deterministic execution order. |

---

## 🚀 Getting Started

### Prerequisites

- **Java Development Kit (JDK)** 8 or newer
- A terminal, or any Java IDE (IntelliJ IDEA, Eclipse, VS Code with Java extensions)

### Build & Run

```bash
# Clone the repository
git clone https://github.com/singhmehul7783/Bit-Based-Randomized-Entropy-System-Scheduler-Based-RNG.git
cd Bit-Based-Randomized-Entropy-System-Scheduler-Based-RNG

# Compile
javac src/Main.java

# Run
java -cp src Main
```

---

## 📖 Usage

```java
import bbresRNG.RNG;

public class Main {
    public static void main(String[] args) {

        RNG rng = new RNG();

        // Basic: random number between 0 and 10 (default settings)
        System.out.println(rng.bbresRNG());

        // Custom range: random number between 5 and 15
        System.out.println(rng.bbresRNG(5, 15));

        // Custom concurrency: range [1, 100] with n=50 worker threads
        System.out.println(rng.bbresRNG(1, 100, 50));

        // Alternate generation method: randTech=2, n=35, range [0, 10000]
        System.out.println(rng.bbresRNG(0, 10000, 35, 2));
    }
}
```

### API Reference

| Method Signature | Description |
|---|---|
| `bbresRNG()` | Random number in `[0, 10]` with default concurrency and technique. |
| `bbresRNG(min, max)` | Random number in `[min, max]`. |
| `bbresRNG(min, max, n)` | Random number in `[min, max]` with `n` concurrent worker threads. |
| `bbresRNG(min, max, n, randTech)` | Full control — specify range, concurrency level, and generation technique (`1` or `2`). |

**Parameters:**
- **`n`** (int) — Number of worker threads to spawn. Higher values increase entropy but also increase computation time.
- **`randTech`** (int) — Selects between two bit-generation algorithms: `1` for `generateRandBitG1`, `2` for `generateRandBitG2`.

---

## 🔬 Design Philosophy

BBRES-RNG is built on a simple but powerful insight: **concurrency is inherently non-deterministic**. The OS thread scheduler makes decisions based on CPU load, interrupt timing, memory pressure, and hundreds of other factors that are impossible to predict or reproduce. By deliberately creating race conditions and measuring their outcomes with microsecond precision, BBRES-RNG converts this system-level chaos into usable randomness.

This makes BBRES-RNG conceptually similar to hardware random number generators (HRNGs) that harvest physical noise — except here, the "physical noise" is the operating system itself.

---

## 📊 Full Validation Reports

The complete validation reports with charts, visual analysis, and detailed methodology are available in the [`docs/`](docs/) directory:

- [`bbres-rng_combined_report.pdf`](docs/bbres-rng_combined_report.pdf) — BBRES-RNG (30/30 ✅)
- [`Java_SecureRandom_combined_report.pdf`](docs/Java_SecureRandom_combined_report.pdf) — Java SecureRandom (30/30 ✅)
- [`Java_Math_random___combined_report.pdf`](docs/Java_Math_random___combined_report.pdf) — Java Math.random() (28/30 ⚠️)

---

## 📄 License

This project is licensed under a **Custom Restrictive License** — see the [LICENSE](LICENSE) file for full terms.

**In short:**
- ✅ **Permitted:** Academic research, education, and personal study — **with proper attribution**
- ❌ **Prohibited without written permission:** Commercial use, modification, distribution, sublicensing, and derivative works
- 📧 **Contact for permissions:** [Mehul Singh](https://github.com/singhmehul7783)

---

## 🤝 Contributing

This project is source-available for educational purposes. If you'd like to contribute, please open an issue first to discuss the proposed changes. All contributions are subject to the project's custom license terms.

---

<p align="center">
  <sub>Built with ☕ Java and a healthy respect for chaos.</sub><br>
  <sub>Developed by <b>Mehul Singh</b> — Validated against 30 statistical tests with 2M+ bit samples.</sub>
</p>
