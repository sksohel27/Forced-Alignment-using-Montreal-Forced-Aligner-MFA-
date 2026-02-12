# Forced Alignment using Montreal Forced Aligner (MFA)

**Assignment 1: Forced Alignment with Robust Out-of-Vocabulary (OOV) Handling**

---

## Objective

Set up and execute a complete forced alignment pipeline using the **Montreal Forced Aligner (MFA)** tool and understand how automatic alignment works between speech audio and its phonetic transcription.

---

## What is Forced Alignment?

Forced alignment automatically matches an audio recording with its corresponding text transcription at the **word** and **phoneme** levels. It determines when each word or sound begins and ends in the speech signal.

### Simple Example

**Audio:** A speaker says — *“Hello world”*
**Transcript:** `HELLO WORLD`

**Pronunciation Dictionary:**

```
HELLO  HH AH L OW
WORLD  W ER L D
```

**Output (TextGrid example)**

**Words**

```
0.00 – 0.45   HELLO
0.45 – 0.90   WORLD
```

**Phones**

```
0.00 – 0.10   HH
0.10 – 0.25   AH
0.25 – 0.40   L
0.40 – 0.45   OW
0.45 – 0.55   W
0.55 – 0.70   ER
0.70 – 0.85   L
0.85 – 0.90   D
```

---

## Dataset

* **Audio files:** Located in the `wav/` folder (WAV format)
* **Transcripts:** Located in the `transcripts/` folder (cleaned and converted to `.lab` format for MFA)

⚠️ **Important:** Only the provided dataset was used for all experiments.

---

## Tasks Completed

1. Set up the MFA environment
2. Installed Montreal Forced Aligner
3. Prepared data (filename matching, punctuation removal, uppercasing, `.lab` conversion)
4. Selected pretrained models:

   * Acoustic model: `english_mfa`
   * Dictionary: `english_us_arpa` (ARPAbet with stress)
   * G2P model: corresponding pretrained `english_us_arpa`
5. Ran forced alignment (baseline + OOV-augmented)
6. Inspected TextGrid files in **Praat**
7. Implemented and evaluated OOV handling via G2P + lexicon augmentation

---

## Repository Structure

```
├── wav/
├── transcripts/
├── dictionary/
├── textgrids/
│   ├── baseline/
│   └── augmented/
├── Assignment_1_Report_Forced_Alignment_Using_MFA_with_Robust_OOV_Handling.pdf
└── README.md
```

---

## Setup & Reproduction Steps

### 1. Install MFA (Recommended: Conda)

Install Miniconda or Anaconda if not already installed.

```bash
# Create and activate environment
conda create -n mfa-aligner -c conda-forge montreal-forced-aligner
conda activate mfa-aligner
```

---

### 2. Download Pretrained Models

```bash
# Acoustic model
mfa model download acoustic english_mfa

# Pronunciation dictionary
mfa model download dictionary english_us_arpa

# G2P model (for OOV generation)
mfa model download g2p english_us_arpa
```

---

### 3. Prepare Data

* Place audio files in `wav/`
* Place transcripts in `transcripts/`
* Clean transcripts:

  * Remove punctuation
  * Convert to uppercase
  * Save as `.lab` with the same base filename as the WAV file

Validate the dataset:

```bash
mfa validate wav/ english_us_arpa
```

---

### 4. Run Baseline Alignment

```bash
mfa align wav/ english_us_arpa english_mfa textgrids/baseline/ \
    --clean \
    --overwrite
```

---

### 5. OOV Handling & Augmented Alignment

**Process:**

* Identified OOV words via `mfa validate` and corpus–dictionary comparison
* Generated pronunciations using G2P:

```bash
mfa g2p oovs_found.txt english_us_arpa oov_pronunciations.txt
```

* Merged new pronunciations into the base dictionary → `dictionary/merged.txt`

Re-ran alignment with the expanded dictionary:

```bash
mfa align wav/ dictionary/merged.txt english_mfa textgrids/augmented/ \
    --clean \
    --overwrite
```

---

## Key Finding ⚠️

**No reduction in `<unk>` tokens (still 229)** due to a **phone set mismatch**:

* `english_us_arpa` → ARPAbet with stress
* `english_mfa` → MFA / near-IPA phone set

Incompatible phones caused fallback to `<unk>` / `spn` despite G2P augmentation.

### Suggested Fix (Not Implemented)

Use a fully consistent pipeline:

✅ Same acoustic model
✅ Same dictionary
✅ Same G2P model

OR apply phone mapping between the sets.

---

## Inspecting Results

Open TextGrid files in **Praat**:

```
textgrids/baseline/
textgrids/augmented/
```

### Observations

* Persistent `<unk>` on the word tier
* `spn` (silence phone) on the phone tier despite clear speech
* Log-likelihood: ~ -45 to -51 (moderate fit)
* Phone duration deviations: ~12–20 ms

---

## Report

Lexicon augmentation is powerful but requires **phone set consistency** between the acoustic model, dictionary, and G2P to effectively resolve OOVs.

For detailed analysis of the whole project you can contact me one below email ID: sksohelofficial27@gmail.com

---

## License

CC BY 4.0 (following MFA model licenses where applicable)

Feel free to open an issue if anything is unclear or if you'd like to reproduce or extend this work.
