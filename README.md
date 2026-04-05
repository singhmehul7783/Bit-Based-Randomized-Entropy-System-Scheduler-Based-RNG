<div align="center">

# BBRES-RNG

**Bit-Based Randomized Entropy System - Scheduler-Based RNG**

*a multithreaded random number generator that harvests real-time entropy from OS thread-scheduling chaos*

<br/>

<img src="https://img.shields.io/badge/language-Java-orange?style=for-the-badge&logo=openjdk&logoColor=white" alt="Java">
<img src="https://img.shields.io/badge/JDK-8%2B-green?style=for-the-badge" alt="JDK 8+">
<img src="https://img.shields.io/badge/Statistical_Tests-45%20/%2045-brightgreen?style=for-the-badge" alt="45/45">
<img src="https://img.shields.io/badge/build-passing-brightgreen?style=for-the-badge" alt="Build: Passing">
<img src="https://img.shields.io/badge/license-Custom%20Restrictive-red?style=for-the-badge" alt="License">

<br/><br/>

**`45/45 Statistical Tests Passed`** · **`Outperforms Java Math.random()`** · **`Matches Java SecureRandom`**

</div>

<br/>

---

<br/>

## `$ cat README`

> No mathematical seed. No deterministic shortcut.
>
> **BBRES-RNG** takes a fundamentally different approach to generating random numbers. Instead of relying on standard library algorithms or fixed mathematical seeds, it deliberately creates controlled concurrency and treats the **unpredictable timing behaviour of the OS thread scheduler** as its entropy source.
>
> Every number it produces is shaped by the live, non-deterministic state of the host machine - making each output practically unrepeatable.

<br/>

## `$ diff traditional_prng bbres_rng`

| Traditional PRNGs | BBRES-RNG |
|:--|:--|
| Seed-based (deterministic from the same seed) | Entropy-based (non-deterministic by design) |
| Mathematical formulas (LCG, Mersenne Twister, etc.) | Harvests timing noise from OS thread scheduling |
| Reproducible given the same initial state | Practically unreproducible - tied to real-time system state |
| Single-threaded execution | Multi-threaded concurrency architecture |

<br/>

## `$ cat pipeline.txt`

```
                         BBRES-RNG Pipeline

 +----------+   +----------+   +----------+
 | Thread 1 |   | Thread 2 |   | Thread N |   <- Spawn
 +----+-----+   +----+-----+   +----+-----+
      |              |              |
      v              v              v
 +----------------------------------------------+
 |      OS Thread Scheduler (Entropy Source)     |
 +----------------------+-----------------------+
                        |
                        v
 +----------------------------------------------+
 |      Timing Data Collection & Capture         |
 +----------------------+-----------------------+
                        |
                        v
 +----------------------------------------------+
 |      Bitwise Mixing (XOR + Bit Shifts)        |
 +----------------------+-----------------------+
                        |
                        v
                 [ Random Output ]
```

**Stage 1 - Thread Spawning & Controlled Race Conditions**
Multiple worker threads launch simultaneously. The OS scheduler decides their execution order - inherently unpredictable, varying by microseconds based on CPU state, system load, and kernel-level scheduling.

**Stage 2 - Timing-Based Entropy Collection**
Each worker thread captures fine-grained timing data during execution. Microsecond-level variations between thread timings become raw entropy input.

**Stage 3 - Bitwise Mixing & Aggregation**
Collected timing results are processed through XOR sums and bit shifts, producing a single random bit per cycle. Bits are assembled to construct the final random number.

<br/>

## `$ ./validate --all --compare`

> Validated against a comprehensive **45-test battery** spanning NIST SP 800-22 core and extended tests (Binary Matrix Rank, Non-Overlapping Template, Overlapping Template, Linear Complexity), distribution uniformity, spectral analysis (FFT, compression, binary derivative, turning point), entropy measurements, autocorrelation profiling, pattern detection, cross-segment consistency, adversarial ML attacks (Logistic Regression, Gradient Boosted Trees, MLP Neural Network), cryptographic wrapper validation, and integer-level distribution and sequence tests (Anderson-Darling, collision, maximum-of-t, Spearman correlation, median). Benchmarked head-to-head against Java's two standard RNG implementations.

```
Test Parameters
---------------
Bit Sample Size       10,918,505 bits
Integer Sample Size   100,000 integers [0..999]
Total Tests per RNG   45
Significance (α)      0.01
```

### `$ cat results/scorecard.txt`

| RNG System | Tests Passed | Verdict | Failed Tests |
|:--|:--|:--|:--|
| **BBRES-RNG** | **45 / 45** | ARCHITECTURE ACCEPTED | None |
| Java `SecureRandom` | **45 / 45** | ARCHITECTURE ACCEPTED | None |
| Java `Math.random()` | **43 / 45** | REVIEW NEEDED | Maurer's, Gap |

> **BBRES-RNG matches cryptographic-grade `SecureRandom` with a perfect score, and outperforms `Math.random()` which failed 2 tests.**

<br/>

### `$ cat results/nist_core.log`

NIST SP 800-22 Core Tests - **9/9**

| Test | p-value | Result |
|:--|:--|:--|
| Frequency (Monobit) | 0.441897 | PASS |
| Block Frequency (M=128) | 0.143304 | PASS |
| Runs Test | 0.670559 | PASS |
| Longest Run of Ones | 0.046389 | PASS |
| Cumulative Sums (Forward) | 0.164091 | PASS |
| Cumulative Sums (Reverse) | 0.656919 | PASS |
| Approximate Entropy (m=2) | 0.851301 | PASS |
| Serial (m=2) - delta1 | 0.679895 | PASS |
| Serial (m=2) - delta2 | 0.671131 | PASS |

### `$ cat results/nist_extended.log`

NIST SP 800-22 Extended Tests - **7/7**

| Test | p-value | Result |
|:--|:--|:--|
| Maurer's Universal Statistical | 0.722786 | PASS |
| Poker Test (m=4) | 0.517531 | PASS |
| Random Excursion Variant | 0.933697 | PASS |
| Binary Matrix Rank | 0.982609 | PASS |
| Non-Overlapping Template (m=9) | 0.361738 | PASS |
| Overlapping Template (m=9) | 1.000000 | PASS |
| Linear Complexity | 0.620280 | PASS |

### `$ cat results/uniformity.log`

Distribution & Uniformity Tests - **3/3**

| Test | p-value | Result |
|:--|:--|:--|
| Byte-Level Chi-Square | 0.324837 | PASS |
| Nibble-Level Chi-Square | 0.517531 | PASS |
| 2-Bit Pair Distribution | 0.287093 | PASS |

### `$ cat results/spectral.log`

Spectral & Structural Tests - **4/4**

| Test | p-value | Result | Detail |
|:--|:--|:--|:--|
| Spectral FFT (Periodicity) | 0.699220 | PASS | No detectable periodicity |
| Compression Ratio (zlib) | 0.218963 | PASS | Ratio=1.000312 |
| Binary Derivative (1st order) | 0.670910 | PASS | ratio=0.500064 |
| Turning Point Test | 0.579502 | PASS | TPs=454743, Expected=454936 |

### `$ cat results/adversarial.log`

Adversarial & Cryptographic Tests - **5/5**

| Test | p-value | Result | Detail |
|:--|:--|:--|:--|
| Frequency Prediction (w=8) | 0.523577 | PASS | Acc: 0.5020 |
| ML Attack (LogReg, w=16) | 0.976148 | PASS | Accuracy: 0.4901 |
| ML Attack (GBT, w=16) | 0.877536 | PASS | Accuracy: 0.4935 |
| ML Attack (MLP, w=16) | 0.173827 | PASS | Accuracy: 0.5052 |
| Pattern Repetition (w=59) | 1.000000 | PASS | No repetition detected |

> All three ML models - Logistic Regression, Gradient Boosted Trees, and MLP Neural Network - achieve ~50% accuracy, equivalent to random guessing. The output is unpredictable to machine learning attack.

### `$ cat results/cryptographic.log`

Cryptographic Wrapper Validation - **1/1**

| Test | p-value | Result | Detail |
|:--|:--|:--|:--|
| SHA-256 CSPRNG Wrapper | 1.000000 | PASS | Post-hash prediction: 0.5338 |

> BBRES output seeded into a SHA-256 based CSPRNG wrapper and re-tested for predictability - validates suitability as an entropy source for cryptographic applications.

### `$ cat results/integer_distribution.log`

Integer Distribution Tests - **8/8**

| Test | p-value | Result |
|:--|:--|:--|
| Chi-Square Uniformity | 0.475159 | PASS |
| Mean (Z-test) | 0.398873 | PASS |
| Variance (Chi-sq) | 0.842551 | PASS |
| Binned Goodness-of-Fit | 0.756309 | PASS |
| Birthday Spacing | 1.000000 | PASS |
| Coupon Collector | 0.500000 | PASS |
| Collision Test | 0.999851 | PASS |
| Anderson-Darling | 0.357194 | PASS |

### `$ cat results/integer_sequence.log`

Integer Sequence Tests - **8/8**

| Test | p-value | Result |
|:--|:--|:--|
| Skewness / Kurtosis | 0.937413 | PASS |
| Maximum-of-5 | 0.155701 | PASS |
| Runs Up/Down | 0.994013 | PASS |
| Lag-1 Autocorrelation | 0.418097 | PASS |
| Gap Test (KS) | 0.332555 | PASS |
| Permutation (t=5) | 0.703417 | PASS |
| Spearman Rank Correlation | 0.417971 | PASS |
| Median Test | 0.839534 | PASS |

<br/>

## `$ cat results/entropy_quality.csv`

| Metric | BBRES-RNG | Theoretical Max |
|:--|:--|:--|
| Shannon Entropy (8-bit) | **7.999860** | 8.000000 |
| Min-Entropy (8-bit) | **7.944862** | 8.000000 |
| Transition Rate | **0.500064** | 0.500000 |
| Bit Balance (1s : 0s) | 49.988 : 50.012 | 50 : 50 |

> **99.998% of maximum Shannon entropy.** Near-ideal transition rate and bit balance across 10.9M bits.

### `$ cat results/autocorrelation.csv`

Autocorrelation Profile (Bits) - near-zero across all lags:

| Lag | 1 | 2 | 4 | 8 | 16 | 32 | 64 | 128 | 256 |
|:--|:--|:--|:--|:--|:--|:--|:--|:--|:--|
| r | -0.0001 | +0.0000 | -0.0000 | +0.0001 | -0.0001 | -0.0003 | -0.0004 | -0.0005 | -0.0003 |

No serial dependence in output streams at any measured lag.

<br/>

## `$ diff math_random_failures bbres_rng_passes`

> Where `Math.random()` failed - and BBRES-RNG didn't.

| Test | Math.random() | BBRES-RNG | Why It Matters |
|:--|:--|:--|:--|
| Maurer's Universal Statistical | FAIL | PASS p=0.7228 | Detects compressibility - subtle LCG patterns in output |
| Gap Test (KS) | FAIL | PASS p=0.3326 | Non-uniform gap distribution between repeated values |

<br/>

## `$ tree src/`

```
Bit-Based-Randomized-Entropy-System-Scheduler-Based-RNG/
├── src/
│   ├── Main.java                              # Entry point & usage examples
│   └── bbresRNG/
│       ├── RNG.java                           # Primary API - [min, max] range
│       ├── randomBitGeneratorModifiedRoot.java # Core bit generator - thread orchestration
│       ├── modRandomBitGenRNG.java            # Modified worker thread implementation
│       └── RandomBitGenRNG.java               # Base worker thread - entropy harvesting
├── docs/                                      # Validation data & reports
├── LICENSE                                    # Custom Restrictive License
└── README.md
```

### `$ grep -r "class" src/ --summary`

```
RNG.java                           Public API. Accepts (min, max), concurrency n, technique randTech.
randomBitGeneratorModifiedRoot.java Orchestrates single random bit generation. Two methods: G1 and G2.
modRandomBitGenRNG.java            Worker thread variant - collects timing data in parallel.
RandomBitGenRNG.java               Base worker thread - launched for entropy harvesting.
```

<br/>

## `$ cat INSTALL.md`

### Prerequisites

`JDK 8+` · Terminal or any Java IDE

### Build & Run

```bash
# Clone
git clone https://github.com/singhmehul7783/Bit-Based-Randomized-Entropy-System-Scheduler-Based-RNG.git
cd Bit-Based-Randomized-Entropy-System-Scheduler-Based-RNG

# Compile
javac src/Main.java

# Run
java -cp src Main
```

<br/>

## `$ cat examples/usage.java`

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

### `$ man bbresRNG`

| Method | Description |
|:--|:--|
| `bbresRNG()` | Random number in `[0, 10]` with default concurrency and technique |
| `bbresRNG(min, max)` | Random number in `[min, max]` |
| `bbresRNG(min, max, n)` | Random number in `[min, max]` with `n` concurrent worker threads |
| `bbresRNG(min, max, n, randTech)` | Full control - range, concurrency level, and generation technique (`1` or `2`) |

**Parameters:**
- **`n`** (int) - Number of worker threads to spawn. Higher values increase entropy but also increase computation time. Valid range: `3–1000`. Default: `71`.
- **`randTech`** (int) - Selects between two bit-generation algorithms: `1` for `generateRandBitG1` (default), `2` for `generateRandBitG2` (shuffle-based).

<br/>

## `$ cat PHILOSOPHY.md`

> Concurrency is inherently non-deterministic.
>
> The OS thread scheduler makes decisions based on CPU load, interrupt timing, memory pressure, and hundreds of other factors that are impossible to predict or reproduce. By deliberately creating race conditions and measuring their outcomes, BBRES-RNG converts system-level chaos into usable randomness.
>
> Conceptually similar to hardware RNGs that harvest physical noise - except here, the "physical noise" is the operating system itself.

<br/>

## `$ ls docs/`

Full validation reports with charts, visual analysis, and detailed methodology:

```
docs/
├── BBRES_RNG_Documentation.pdf               # Architecture & design documentation
├── bbres-rng_combined_report.pdf             # BBRES-RNG      45/45
├── Java SecureRandom_combined_report.pdf     # SecureRandom   43/45
└── Java Math.random()_combined_report.pdf    # Math.random()  41/45
```

<br/>

## `$ cat LICENSE --summary`

```
PERMITTED    Academic research, education, personal study - with proper attribution
PROHIBITED   Commercial use, modification, distribution, sublicensing, derivative works
             (without written permission)
CONTACT      github.com/singhmehul7783
```

See [LICENSE](LICENSE) for full terms.

<br/>

## `$ cat CONTRIBUTING.md`

> Source-available for educational purposes. To contribute, open an issue first to discuss proposed changes. All contributions are subject to the project's custom license terms.

<br/>

---

<div align="center">

<br/>

```
Built with Java and a healthy respect for chaos.
Developed by Mehul Singh
Validated against 45 statistical tests - 10.9M+ bits & 100k integers.
Research prototype. Paper forthcoming.
```

<sub>[mehul.engineer](https://mehul.engineer) · [hello@mehul.engineer](mailto:hello@mehul.engineer)</sub>

</div>
