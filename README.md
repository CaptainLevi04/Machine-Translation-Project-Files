# Neural Machine Translation — Arabic ↔ English

A from-scratch Neural Machine Translation project built to understand the full NLP pipeline behind machine translation rather than simply optimize for the highest possible BLEU score.

The core project implements a **Seq2Seq LSTM Encoder–Decoder with Bahdanau Attention**, trained on Arabic–English parallel data and evaluated using **BLEU**. The project was then extended through dataset analysis, dataset replacement, dataset fusion, a Transformer implementation, bidirectional translation, live deployment, paragraph-level translation, and transfer learning.

## Project Philosophy

> **The goal was not to chase the highest possible BLEU score, but to truly understand the entire NLP pipeline, from tokenization to decoding, while building a Seq2Seq model and then a Transformer from scratch.**

**Build → Evaluate → Analyze → Improve → Retrain → Compare**

## Core Architecture

**Arabic Sentence → Tokenization + IDs → Embedding → LSTM Encoder → Bahdanau Attention → LSTM Decoder → English Output**

- **Tokenization:** converts text into tokens and numerical IDs.
- **Embedding:** maps token IDs into dense vector representations.
- **LSTM Encoder:** reads the source sequence and produces contextual hidden states.
- **Bahdanau Attention:** selects the most relevant encoder states at each decoding step.
- **LSTM Decoder:** generates the target sentence one token at a time.
- **Teacher Forcing:** used during training to provide the ground-truth previous target token.
- **BLEU:** evaluates generated translations against reference translations.
- **Greedy Search:** simple and fast decoding.
- **Beam Search:** searches multiple candidate sequences and generally gives better translations at higher computational cost.

## Project Requirements

![Project Requirements](../Project%20Requirments.png)

## Project Development Journey

### 1. Initial Baseline — OPUS / Helsinki NLP

The first experiment used the **OPUS / Helsinki NLP dataset** and produced approximately **12.92 BLEU**.

Error analysis then exposed serious data-quality problems, including corrupted translation pairs and extreme sentence-length outliers. One validation example contained roughly **171K words**, motivating a dataset change.

Notebook: `Notebooks/Developing Journey/Helsinki_Low_Quality_Data_Set.ipynb`

### 2. Tatoeba — Cleaner Sentence-Level Data

The project moved to the **Tatoeba dataset** with about **30K sentence pairs**.

**Result: 26.55 BLEU**

This passed the project target of BLEU > 20, but Tatoeba is dominated by short sentences, so the model became specialized in short-sentence translation.

Notebook: `Notebooks/Developing Journey/TATOEBA_Data_Set.ipynb`

### 3. Wikipedia — Broader Sentence-Length Coverage

A **Wikipedia dataset with about 86K sentence pairs** was used to test broader sentence-length handling, covering approximately **1–35 words**.

**Result: 25.63 BLEU**

Notebook: `Notebooks/Developing Journey/wikipedia_DataSet.ipynb`

### 4. Combining Tatoeba + Wikipedia

Instead of choosing between the two datasets, they were combined:

- Tatoeba → strong short-sentence coverage
- Wikipedia → broader sentence-length coverage

**Result: 27.48 BLEU**

## Transformer Extension

As an additional experiment beyond the core requirements, a **Transformer model was implemented from scratch** and tested on Tatoeba.

- **Greedy decoding:** 33.62 BLEU
- **Beam Search (width = 5):** 35.19 BLEU

Notebook: `Notebooks/Extra Work (advanced than project requirments)/Transformer_with_Tatoeba.ipynb`

## Bidirectional Arabic ↔ English Translation

The original project focused on **Arabic → English**.

As an additional extension, the system was expanded to:

**Arabic → English**  
**English → Arabic**

The English → Arabic direction achieved **21.16 BLEU**.

The project was also turned into a live translator with **automatic language detection**, routing each input to the matching translation model.

## Paragraph-Level Translation + Transfer Learning

The project was extended beyond isolated sentence pairs into a **paragraph-aligned English–Arabic translation setup**.

The **United Nations documents dataset** was processed and restructured into compact paragraph-level aligned samples. Transfer Learning was then applied to this setup.

**Result: 74.12 BLEU**

Relevant files:

- `Notebooks/Developing Journey/Constructing_Paragraphs_from_UN_Data_Set.ipynb`
- `Notebooks/Extra Work (advanced than project requirments)/Paragraphs_Transfer_Learning_(United_Nations).ipynb`
- `Notebooks/Extra Work (advanced than project requirments)/UnitedNations_Pragraphs_DataSet.json`

## Attention Visualization

Bahdanau attention weights were visualized as heatmaps to inspect which Arabic source tokens receive higher attention while generating each English token.

## Live Deployment

The Streamlit application supports:

- Arabic → English
- English → Arabic
- Automatic language detection
- Live translation

Documentation: `Streamlit/application.md`

## Repository Structure

```text
Machine-Translation-Project-Files/
│
├── Notebooks/
│   ├── Project Notebook/
│   │   └── en-ar-ar-en.ipynb
│   │
│   ├── Developing Journey/
│   │   ├── Constructing_Paragraphs_from_UN_Data_Set.ipynb
│   │   ├── Helsinki_Low_Quality_Data_Set.ipynb
│   │   ├── TATOEBA_Data_Set.ipynb
│   │   └── wikipedia_DataSet.ipynb
│   │
│   └── Extra Work (advanced than project requirments)/
│       ├── Paragraphs_Transfer_Learning_(United_Nations).ipynb
│       ├── Transformer_with_Tatoeba.ipynb
│       └── UnitedNations_Pragraphs_DataSet.json
│
├── Presentation/
│   └── AR-EN Presentation.pptx
│
├── Results/
│   ├── AR to EN.jpg
│   ├── Arabic to English.jpg
│   ├── EN to AR.jpg
│   ├── English to Arabic.jpg
│   └── amr.md
│
├── Streamlit/
│   └── application.md
│
├── Project Requirments.png
└── README.md
```

## Notebook Guide

| Notebook | Purpose |
|---|---|
| `en-ar-ar-en.ipynb` | Main project notebook for the core Arabic–English / English–Arabic translation work. |
| `Helsinki_Low_Quality_Data_Set.ipynb` | Initial baseline experiment and dataset-quality/error analysis. |
| `TATOEBA_Data_Set.ipynb` | Tatoeba experiment and short-sentence translation results. |
| `wikipedia_DataSet.ipynb` | Wikipedia experiment for broader sentence-length coverage. |
| `Constructing_Paragraphs_from_UN_Data_Set.ipynb` | Preparing and restructuring UN data for paragraph-level alignment. |
| `Transformer_with_Tatoeba.ipynb` | From-scratch Transformer experiment on Tatoeba. |
| `Paragraphs_Transfer_Learning_(United_Nations).ipynb` | Transfer Learning experiment for paragraph-level UN translation. |

## Key Results

| Experiment | Dataset / Setup | Result |
|---|---|---:|
| Initial baseline | OPUS / Helsinki NLP | **12.92 BLEU** |
| Seq2Seq LSTM + Attention | Tatoeba (~30K pairs) | **26.55 BLEU** |
| Seq2Seq LSTM + Attention | Wikipedia (~86K pairs) | **25.63 BLEU** |
| Combined sentence-level model | Tatoeba + Wikipedia | **27.48 BLEU** |
| Transformer + Greedy | Tatoeba (~30K pairs) | **33.62 BLEU** |
| Transformer + Beam Search | Tatoeba (~30K pairs) | **35.19 BLEU** |
| English → Arabic | Bidirectional extension | **21.16 BLEU** |
| Paragraph-level Transfer Learning | UN dataset | **74.12 BLEU** |

> BLEU scores from different experiments should not automatically be treated as directly comparable because the experiments use different datasets, directions, data granularities, and model setups.

## Technologies

- Python
- PyTorch
- NLTK
- sacrebleu
- Streamlit
- Jupyter Notebook

## Results & Presentation

The repository includes the project presentation, translation examples, attention visualizations, and deployment documentation.

## Notes

This repository reflects the **development journey** of the project, including failed experiments, dataset investigations, architecture changes, and additional work beyond the original requirements.

The key outcome was understanding how **data quality, sentence length, decoding strategy, architecture, and transfer learning** affect an NMT system.

## Author

**Machine Translation Project — Arabic ↔ English**

Built as a Deep Learning / NLP project with **ITIDA – EME Innovation Labs – Giza**.
