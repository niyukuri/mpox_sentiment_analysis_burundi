# Detecting Alarmist Mpox-Related Communication on Facebook in Burundi

**Machine learning and transformer-based approaches for digital epidemic intelligence**

This research project evaluates natural language processing (NLP) methods for distinguishing **informative** from **alarmist** Mpox-related communication in publicly available Facebook posts collected in Burundi. It compares traditional machine-learning classifiers with French-language and multilingual transformer models and explores sentiment and engagement patterns.

> **Important:** This is a research prototype, not an autonomous misinformation detector or a validated public-health alerting system. Model predictions should be interpreted in context and reviewed by a human analyst.

## Annotation

Each post was manually assigned to one of two categories.

### Informative

Posts presenting factual, evidence-based information intended to inform, educate, or provide public-health guidance without exaggerated or fear-amplifying framing.

### Alarmist

Posts containing exaggerated, sensationalized, fear-inducing, or anxiety-amplifying language or framing concerning Mpox.

The annotation was performed by two annotators using predefined labeling guidelines.

## Data Structure

| Variable | Description |
|---|---|
| `Date` | Publication date |
| `Texte` | Original Facebook post |
| `texte_clean` | Preprocessed text |
| `PageName` | Publishing Facebook page or organization |
| `Likes` | Number of likes/reactions |
| `Shares` | Number of shares |
| `Comments` | Number of comments |
| `Engagement` | Likes + shares + comments |
| `Category` | Numerical class: 0 = Informative, 1 = Alarmist |
| `Category_Name` | Textual class label |

Engagement variables were **not used as predictors** in the classification models. They were analyzed separately to characterize audience interaction.

## Analytical Workflow

```text
Facebook Posts
      |
      ▼
Data Collection
      │
      ▼
Data Cleaning & Preprocessing
      │
      ├───────────────┐
      │               │
      ▼               ▼
Sentiment         Classification
Analysis              │
                      ├── Traditional ML
                      │     ├── Logistic Regression
                      │     ├── Naïve Bayes
                      │     └── SVM
                      │
                      └── Transformers
                            ├── CamemBERT
                            └── XLM-RoBERTa
                      │
                      ▼
             Performance Evaluation
                      │
                      ▼
             Error / Context Analysis
```

## Text Preprocessing

### Traditional machine-learning models
## Overview

Social media can help public-health authorities understand how health information is communicated during outbreaks. However, factual reporting about cases, deaths, or disease risks can resemble alarmist language to automated classifiers. This project investigates that challenge in a French-dominant, low-resource setting.

The primary task is binary text classification:

- **Informative (0):** factual, evidence-based information intended to inform, educate, or provide public-health guidance without exaggerated or fear-amplifying framing.
- **Alarmist (1):** communication that uses exaggerated, sensationalized, fear-inducing, or anxiety-amplifying framing concerning Mpox.

Sentiment analysis is a **secondary descriptive analysis** and is not treated as equivalent to the informative/alarmist classification.

## Study dataset

The manuscript describes a manually collected dataset of **357 publicly available Facebook posts**, published from **14 April 2024 to 10 July 2026**.

| Characteristic | Value |
|---|---:|
| Total posts | 357 |
| Informative posts | 321 (89.9%) |
| Alarmist posts | 36 (10.1%) |
| Main language | French, with occasional English terminology |
| Unit of analysis | Individual Facebook post |

Posts were collected from public institutional, health, media, and other public Facebook pages using predefined Mpox-related search terms. Private or restricted content, duplicates, unrelated advertisements, and posts without sufficient text were excluded.

The manuscript identifies the following sources: Ministère de la Santé Publique du Burundi, Institut National de la Santé Publique du Burundi, UNICEF Burundi, Croix-Rouge Burundi, OMS Burundi, Global Development Community Burundi, US Embassy Bujumbura Burundi, WHO Regional Office for Africa, IOM Burundi, Andika Magazine, Jimbere Magazine, Akeza.net, and Yaga.

**Data access and privacy:** The raw dataset is not included in this repository and should not be redistributed without appropriate permission. The manuscript notes that public availability does not remove platform, privacy, or ethical constraints.

## Research objectives

1. Characterize sentiment and engagement patterns in Mpox-related Facebook posts.
2. Compare traditional machine-learning and transformer-based models for identifying alarmist communication.
3. Examine model errors and contextual challenges relevant to digital epidemic intelligence.

## Models evaluated

### Traditional machine learning

- Logistic Regression
- Multinomial Naïve Bayes
- Support Vector Machine (SVM; LinearSVC)

Text is represented using TF–IDF features with up to 5,000 features and unigram/bigram terms (`ngram_range=(1, 2)`). Class weighting and minority-class oversampling are used to address class imbalance, as described in the study.

### Transformer models

- **CamemBERT** — a French-language transformer model
- **XLM-RoBERTa** — a multilingual transformer model

The manuscript describes fine-tuning with a maximum sequence length of 256 tokens, a learning rate of `1e-5`, batch size of 16, 10 epochs, AdamW optimization, and a weight decay of `0.01`.

## Analysis workflow

1. **Collect and clean data:** Retrieve eligible public posts and remove duplicates and ineligible records.
2. **Annotate posts:** Assign informative or alarmist labels using predefined guidelines and human judgement.
3. **Preprocess text:** Apply TF–IDF-oriented cleaning for traditional models; use tokenization appropriate to each transformer.
4. **Address class imbalance:** Apply oversampling only within training data/folds so validation and test data retain their natural class distribution.
5. **Train and evaluate models:** Use a stratified 80/20 train/test split with `random_state=42`; use repeated stratified cross-validation on the training set.
6. **Conduct secondary analyses:** Examine sentiment, likes, shares, comments, and total engagement.
7. **Review errors:** Inspect false positives and false negatives to understand contextual ambiguity.

Performance measures include accuracy, precision, recall, F1-score, and confusion matrices. Because alarmist posts are the minority class, alarmist-class recall and F1-score are particularly important.

## Principal findings

On the held-out test set, the manuscript reports the following results:

| Model | Informative F1 | Alarmist precision | Alarmist recall | Alarmist F1 | Accuracy |
|---|---:|---:|---:|---:|---:|
| XLM-RoBERTa | 0.95 | 0.56 | 0.71 | **0.63** | 0.92 |
| CamemBERT | 0.95 | 0.57 | 0.57 | 0.57 | 0.92 |
| Logistic Regression | 0.95 | 0.50 | 0.29 | 0.36 | 0.90 |
| SVM | 0.95 | 0.50 | 0.29 | 0.36 | 0.90 |
| Naïve Bayes | 0.91 | 0.33 | 0.57 | 0.42 | 0.85 |

The test set contained only **seven alarmist posts**, so the reported minority-class metrics are uncertain and should be interpreted cautiously. Cross-validation results also showed variability, and the study does not establish that one transformer will generalize best to new data.

The secondary sentiment analysis classified 313 posts as positive, 35 as negative, and 9 as neutral. Alarmist posts had higher average observed engagement in this dataset. These are descriptive findings: engagement does not establish faster diffusion or causal effects on public beliefs or behaviour.

### Lessons from error analysis

- Factual reports containing case counts, deaths, suspected cases, or words such as “risk” and “vigilance” were sometimes classified as alarmist.
- Some alarmist posts expressed themselves in calm or formal language and were missed by models.
- Negative sentiment is not the same as alarmist communication.
- Contextual human review is essential when interpreting predictions.

## Repository contents

The repository can be organized as follows. Adapt the names to match the files actually committed:

```text
.
├── README.md
├── requirements.txt
├── notebooks/
│   ├── 01_data_exploration.ipynb
│   ├── 02_sentiment_analysis.ipynb
│   ├── 03_traditional_models.ipynb
│   └── 04_transformer_models.ipynb
├── src/
│   ├── preprocessing.py
│   ├── train_classical.py
│   ├── train_transformer.py
│   └── evaluate.py
├── results/
│   ├── figures/
│   └── metrics/
└── docs/
    └── study_manuscript.pdf
```

This is a **suggested structure**, not a claim that these files are already present. Only add entries for files that are actually included in the repository.

| Model | Class | Precision | Recall | F1-score | Accuracy |
|---|---|---:|---:|---:|---:|
| XLM-RoBERTa | Informative | 0.97 | 0.94 | 0.95 | 0.92 |
| XLM-RoBERTa | Alarmist | 0.56 | 0.71 | **0.63** | 0.92 |
| CamemBERT | Informative | 0.95 | 0.95 | 0.95 | 0.92 |
| CamemBERT | Alarmist | 0.57 | 0.57 | 0.57 | 0.92 |
| Logistic Regression | Informative | 0.93 | 0.97 | 0.95 | 0.90 |
| Logistic Regression | Alarmist | 0.50 | 0.29 | 0.36 | 0.90 |
| SVM | Informative | 0.93 | 0.97 | 0.95 | 0.90 |
| SVM | Alarmist | 0.50 | 0.29 | 0.36 | 0.90 |
| Naïve Bayes | Informative | 0.95 | 0.88 | 0.91 | 0.85 |
| Naïve Bayes | Alarmist | 0.33 | 0.57 | 0.42 | 0.85 |
## Installation

Use Python 3.10 or a compatible version supported by the selected package releases.

```bash
git clone <YOUR-REPOSITORY-URL>
cd <YOUR-REPOSITORY-DIRECTORY>

python -m venv .venv
```

Activate the environment:

```bash
# macOS / Linux
source .venv/bin/activate

# Windows PowerShell
.venv\Scripts\Activate.ps1
```

Install dependencies after creating a `requirements.txt` containing the versions tested by the project:

```bash
pip install -r requirements.txt
```

The manuscript identifies these main libraries:

- `pandas`
- `scikit-learn`
- `torch`
- `transformers`
- `sentencepiece`
- `matplotlib`
- `openpyxl`

GPU acceleration was used for transformer training in the reported experiments (NVIDIA Tesla T4, with mixed precision). A compatible GPU is helpful for fine-tuning transformers; traditional TF–IDF models can generally run on a CPU.

## Data and reproducibility

The manuscript describes a stratified 80/20 train/test split with a fixed random seed of 42 and repeated stratified cross-validation on the training data. Oversampling should occur **only within each training fold** to prevent information leakage.

The raw Facebook dataset is not distributed with this project. To reproduce the analysis, users need lawful access to an appropriately governed dataset and the corresponding preprocessing, annotation, training, and evaluation code.

Before claiming full reproducibility, document the exact Python and package versions, pretrained model identifiers and revisions, annotation guidelines, data-cleaning steps, random seeds, split indices where sharing is appropriate, and model-selection procedure. Avoid publishing private information or post-level content that cannot be redistributed.

## Ethical considerations and responsible use

- Use only data collected and processed in accordance with applicable platform terms, ethical requirements, and relevant laws.
- Do not publish private or restricted content, personal identifiers, or a raw dataset whose redistribution is not authorized.
- Treat predictions as signals for **prioritizing human review**, not as definitive judgements about misinformation, intent, credibility, or public-health risk.
- Review false positives and false negatives, especially for factual epidemiological updates and public-health advice.
- Evaluate performance across languages, sources, and time periods before considering any operational use.
- Do not use model outputs to target, penalize, or stigmatize individuals or communities.

## Limitations

The study has several limitations:

1. The dataset is relatively small and contains only 36 alarmist posts.
2. Only seven alarmist posts occur in the held-out test set.
3. Data come from Facebook and may not represent other platforms or the wider population.
4. The corpus is predominantly French and does not fully represent Burundi's multilingual communication environment.
5. Human annotation involves judgement, and informative and alarmist framing can be context-dependent.
6. The binary labels do not cover all communication types, such as misinformation, disinformation, rumours, opinions, preventive advice, or official announcements.

## Future work

Potential extensions include larger and more diverse datasets, additional languages and platforms, stronger annotation and inter-annotator agreement procedures, confidence intervals, explainability methods, temporal evaluation, and prospective evaluation of human-in-the-loop digital epidemic intelligence workflows.

## Citation

If you use or discuss this work, cite the manuscript using the author and title information below. Add the final publication details or DOI when available.

> Sakubu, D., Gatore Sinigirira, K. J., Ntacoyanyimye, S., Kamatari, O., Havyarimana, P., Ndikuryayo, S., Irumva, K. C., Renguka, L. C., Bishola, T., & Niyukuri, D. *Detecting alarmist Mpox-related communication on Facebook in Burundi using machine learning and transformer-based models.* Manuscript.

## Funding and acknowledgements

The manuscript reports support from the UK International Development and the International Development Research Centre (IDRC), Ottawa, Canada, through the AI for Development: Responsible AI, Empowering People (AI4D) Program, and from the UK Medical Research Council (MRC) through UK Research and Innovation (UKRI), in collaboration with Imperial College London (Project PB4838). The manuscript states that its views do not necessarily represent those of IDRC, its Board of Governors, or the official policies of the UK Government.

## License

MIT License

Copyright (c) 2026 David Niyukuri and project contributors

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all  copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.


## Contact

For research enquiries, contact the corresponding project team through the contact details provided in the associated publication or the repository's GitHub profile.
