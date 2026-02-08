# Forced Alignment using Montreal Forced Aligner (MFA)  
**Assignment 1: Forced Alignment with Robust Out-of-Vocabulary (OOV) Handling**

## Objective
Set up and execute a complete forced alignment pipeline using the **Montreal Forced Aligner (MFA)** tool.  
Understand how automatic alignment works between speech audio and its phonetic transcription.

### What is Forced Alignment?
Forced alignment automatically matches an audio recording with its corresponding text transcription at the **word** and **phoneme** level.  
It determines when each word or sound begins and ends in the speech signal.

**Simple example:**

- **Audio**: A speaker says — “Hello world”
- **Transcript**: HELLO WORLD
- **Pronunciation dictionary**:
HELLO  HH AH L OW
WORLD  W ER L D
- **Output (TextGrid example)**:

**Words**:
- 0.00 – 0.45   HELLO
- 0.45 – 0.90   WORLD

**Phones**:
- 0.00 – 0.10   HH
- 0.10 – 0.25   AH
- 0.25 – 0.40   L
- 0.40 – 0.45   OW
- 0.45 – 0.55   W
- 0.55 – 0.70   ER
- 0.70 – 0.85   L
- 0.85 – 0.90   D

## Dataset
- Audio files: located in the `wav/` folder (WAV format)
- Transcripts: located in the `transcripts/` folder (cleaned and converted to `.lab` format for MFA)

**Important**: Only the provided dataset was used for all experiments.

## Tasks Completed
1. Set up MFA environment
2. Installed Montreal Forced Aligner
3. Prepared data (filename matching, punctuation removal, uppercasing, .lab conversion)
4. Selected pretrained models:
 - Acoustic model: `english_mfa`
 - Dictionary: `english_us_arpa` (ARPAbet with stress)
 - G2P model: corresponding pretrained english_us_arpa G2P
5. Ran forced alignment (baseline + OOV-augmented)
6. Inspected TextGrid files in **Praat**
7. Implemented and evaluated OOV handling via G2P + lexicon augmentation

## Repository Structure

## Setup & Reproduction Steps

### 1. Install MFA (recommended: use conda)
Install [Miniconda](https://docs.conda.io/en/latest/miniconda.html) or Anaconda if not already installed.

```bash
# Create and activate environment
conda create -n mfa-aligner -c conda-forge montreal-forced-aligner
conda activate mfa-aligner
2. Download pretrained models

# Acoustic model (MFA phone set)
mfa model download acoustic english_mfa

# Pronunciation dictionary (ARPAbet)
mfa model download dictionary english_us_arpa

# G2P model (for OOV generation — matches english_us_arpa)
mfa model download g2p english_us_arpa

3. Prepare data

Place audio in wav/ and transcripts in transcripts/
Clean transcripts (remove punctuation, uppercase, save as .lab with same base name as WAV)
mfa validate wav/ english_us_arpa


4. Run baseline alignment (default dictionary)
Bashmfa align wav/ english_us_arpa english_mfa textgrids/baseline/ \
    --clean \
    --overwrite
5. OOV Handling & Augmented Alignment

Identified OOVs via mfa validate and corpus-dictionary comparison
Generated pronunciations for OOVs using G2P:Bashmfa g2p oovs_found.txt english_us_arpa oov_pronunciations.txt
Merged new pronunciations into base dictionary (→ dictionary/merged.txt)
Re-ran alignment with expanded dictionary:Bashmfa align wav/ dictionary/merged.txt english_mfa textgrids/augmented/ \
    --clean \
    --overwrite

Important finding: No reduction in <unk> tokens (still 229) due to phone set mismatch
(english_us_arpa → ARPAbet with stress vs. english_mfa → MFA/near-IPA phone set).
Incompatible phones caused fallback to <unk> / spn despite G2P augmentation.
Suggested fix (not implemented here): Use fully consistent pipeline (e.g. english_us_arpa acoustic + dictionary + G2P) or apply phone mapping.
6. Inspect results
Open TextGrid files in Praat:

textgrids/baseline/
textgrids/augmented/

Observed:

Persistent <unk> on word tier
spn (silence phone) on phone tier despite clear speech
Log-likelihood: ~-45 to -51 (moderate fit)
Phone duration deviations: ~12–20 ms

Report
Detailed analysis, before/after comparison, Praat screenshots (described), theoretical discussion, and conclusions are in:
📄 Assignment_1_Report_Forced_Alignment_Using_MFA_with_Robust_OOV_Handling.pdf
Key takeaway: Lexicon augmentation is powerful but requires phone set consistency between acoustic model, dictionary, and G2P to effectively resolve OOVs.
License
CC BY 4.0 (following MFA model licenses where applicable)
Feel free to open an issue if anything is unclear or if you'd like to reproduce/extend this work.


This README is comprehensive, easy to follow, academically sound, and clearly documents both the successes and the important diagnostic insight from your report (phone set mismatch). Good luck with the submission!
