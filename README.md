# Detecting Alarmist Mpox-Related Communication on Facebook in Burundi

Machine learning and transformer-based NLP for detecting alarmist communication in Mpox-related Facebook posts in Burundi.

## Overview

Social media platforms have become important channels for disseminating public health information during infectious disease outbreaks. At the same time, misinformation, sensationalized narratives, and emotionally charged content can complicate public understanding of disease risks.

This project investigates the use of natural language processing (NLP) and machine learning to distinguish between **informative** and **alarmist** Mpox-related communication on Facebook in Burundi.

The study compares three traditional machine-learning algorithms:

- Logistic Regression
- Naïve Bayes
- Support Vector Machine (SVM)

with two transformer-based language models:

- CamemBERT
- XLM-RoBERTa

The project also includes secondary analyses of **sentiment** and **Facebook user engagement**.

## Research Objectives

The study aims to:

1. Characterize sentiment and engagement patterns in Mpox-related Facebook communication in Burundi.
2. Compare traditional machine-learning and transformer-based models for distinguishing informative and alarmist communication.
3. Examine model misclassification patterns and identify contextual challenges relevant to automated digital epidemic intelligence.

## Dataset

The dataset consists of publicly available Facebook posts related to Mpox and Mpox prevention in Burundi.

### Data collection

Posts were manually collected from public Facebook pages using predefined search terms in French, English, and Kirundi, including:

- `Mpox Burundi`
- `MonkeyPox Burundi`
- `Variole du singe Burundi`
- `Akaranda`
- `Ibihara vy’inkende`
- `Mpox Vaccin Burundi`

**Study period:** 14 April 2024 – 10 July 2026

The dataset was predominantly French-language, with occasional English terminology.

### Sources

The study included public posts from 13 Facebook pages, including:

- Ministère de la Santé Publique du Burundi
- Institut National de la Santé Publique du Burundi (INSP)
- UNICEF Burundi
- Croix-Rouge Burundi
- OMS Burundi
- Global Development Community Burundi
- US Embassy Bujumbura Burundi
- Bureau Régional de l'OMS en Afrique
- IOM Burundi
- Andika Magazine
- Jimbere Magazine
- Akeza.net
- Yaga

Private or restricted content, duplicate posts, unrelated advertisements, and posts without sufficient textual information were excluded.

## Dataset Summary

The final dataset contains **357 Facebook posts**:

| Category | Number | Percentage |
|---|---:|---:|
| Informative | 321 | 89.9% |
| Alarmist | 36 | 10.1% |
| **Total** | **357** | **100%** |

The substantial class imbalance is an important characteristic of the classification problem.

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
      │
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

The preprocessing included:

- Lowercase conversion
- URL removal
- Whitespace and encoding normalization
- French-language normalization
- Removal of punctuation and non-semantic elements
- Keyword boosting for predefined alarm-related terms

Alarm-related terms were repeated three times to increase their TF-IDF weight.

### Transformer models

Transformer preprocessing was designed to preserve contextual information.

The workflow included:

- Lowercase conversion
- URL removal
- Non-alphabetic character removal
- Space normalization
- Tokenization using the corresponding pretrained tokenizer
- Maximum sequence length of 256 tokens

Numerical information such as case counts and death tolls was retained where possible because it may carry epidemiological meaning.

## Handling Class Imbalance

To address the imbalance:

- Stratified data splitting was used.
- Minority-class oversampling with replacement was applied **only to the training data**.
- Validation and test sets retained their natural class distribution.
- Precision, recall, F1-score, and confusion matrices were emphasized rather than accuracy alone.

## Machine-Learning Models

### Traditional Models

The following models were implemented using scikit-learn:

- Logistic Regression
- Multinomial Naïve Bayes
- Support Vector Machine

Text was represented using TF-IDF features with:

```text
max_features = 5000
ngram_range = (1, 2)
```

Class weighting was used for Logistic Regression and LinearSVC.

### Transformer Models

Two pretrained transformer models were fine-tuned:

- **CamemBERT** – French-language transformer
- **XLM-RoBERTa** – multilingual transformer

Key parameters:

```text
Learning rate: 1e-5
Batch size: 16
Epochs: 10
Optimizer: AdamW
Weight decay: 0.01
Maximum sequence length: 256
Random seed: 42
```

Training used an NVIDIA Tesla T4 GPU with mixed-precision training.

## Model Evaluation

Models were evaluated using:

- Accuracy
- Precision
- Recall
- F1-score
- Confusion matrices

Because alarmist posts represented the minority class, **alarmist-class recall and F1-score** were considered particularly important.

## Train/Test Strategy

The dataset was divided using a stratified:

- **80% training set**
- **20% held-out test set**

The same test partition was used for model comparison.

Repeated stratified cross-validation was additionally performed on the training set using:

```text
7 folds × 3 repeats
```

The test set remained isolated for final evaluation.

## Results

### Test-set performance

| Model | Class | Precision | Recall | F1-score | Accuracy |
|---|---|---:|---:|---:|---:|
| XLM-RoBERTa | Informative | 0.97 | 0.94 | 0.95 | 0.92 |
| XLM-RoBERTa | Alarmist | 0.56 | 0.71 | **0.63** | 0.92 |
| CamemBERT | Informative | 0.95 | 0.95 | 0.95 | 0.92 |
| CamemBERT | Alarmist | 0.57 | 0.57 | 0.57 | 0.92 |
| Logistic Regression | Informative | 0.91 | 0.95 | 0.93 | 0.88 |
| Logistic Regression | Alarmist | 0.25 | 0.14 | 0.18 | 0.88 |
| SVM | Informative | 0.91 | 0.98 | 0.95 | 0.90 |
| SVM | Alarmist | 0.50 | 0.14 | 0.22 | 0.90 |
| Naïve Bayes | Informative | 0.90 | 1.00 | 0.95 | 0.90 |
| Naïve Bayes | Alarmist | 0.00 | 0.00 | 0.00 | 0.90 |

On the held-out test set, XLM-RoBERTa correctly identified **5 of 7 alarmist posts**, while CamemBERT identified **4 of 7**. Both transformer models achieved an overall accuracy of 92%.

Because only seven alarmist posts were present in the test set, minority-class performance estimates should be interpreted cautiously.

## Sentiment Analysis

Sentiment analysis was conducted as a **secondary exploratory analysis** using a pretrained Hugging Face transformer pipeline.

Among the 357 posts:

- **313 (≈88%)** were classified as positive
- **35 (≈10%)** were classified as negative
- **9 (≈3%)** were classified as neutral

Sentiment and alarmist communication are treated as different concepts. A negative post is not necessarily alarmist, and an informative post may contain negative or emotionally charged language.

## Engagement Analysis

The project examines:

- Likes
- Shares
- Comments
- Total engagement

Observed average engagement was higher for alarmist posts in the collected dataset:

| Category | Likes | Shares | Comments |
|---|---:|---:|---:|
| Informative | ~61 | ~11 | ~3 |
| Alarmist | ~142 | ~36 | ~5 |

These descriptive findings should **not** be interpreted as evidence that alarmist posts spread faster or cause changes in public beliefs or behavior.

## Misclassification Analysis

Several informative posts were incorrectly classified as alarmist when they contained:

- Case counts
- Death tolls
- Suspected cases
- Terms such as `risk`
- `propagation`
- `vigilance`
- `dangerous`

Conversely, some alarmist posts were classified as informative because they used calm, formal, or solution-oriented language.

These findings demonstrate that isolated keywords or sentiment are insufficient for reliably identifying alarmist communication. Context remains essential.

## Digital Epidemic Intelligence

The proposed application is a **decision-support workflow**:

```text
Social Media Monitoring
        ↓
Automated NLP Screening
        ↓
Potentially Alarmist Content
        ↓
Human Contextual Review
        ↓
Risk Communication / Public Health Response
```

The models are intended to help **prioritize content for human review**, rather than autonomously determine whether information is misinformation or represents a public-health risk.

## Reproducibility

The analysis uses:

```text
RANDOM_STATE = 42
```

Key software packages include:

- Python
- PyTorch
- Transformers
- scikit-learn
- pandas
- matplotlib
- openpyxl
- sentencepiece

Transformer experiments were conducted using an NVIDIA Tesla T4 GPU.

## Ethical Considerations

Only publicly accessible Facebook content was included.

- Private and restricted content was excluded.
- Personal identifiers were not analyzed or reported.
- The project does not seek to identify individual users.

## Data Availability

The raw Facebook dataset is **not publicly redistributed** because it consists of social-media content subject to platform terms and privacy considerations.

Any derived or anonymized dataset should only be shared where permitted by applicable ethical and platform requirements.

This repository therefore focuses on the analytical workflow, code, and reproducible methodology rather than redistribution of the raw Facebook posts.

## Limitations

1. The dataset contains only 357 posts and 36 alarmist posts.
2. Only seven alarmist posts were present in the held-out test set.
3. The study focuses on Facebook and does not represent other social-media platforms.
4. The dataset is predominantly French and does not fully represent Burundi's multilingual communication environment, including Kirundi, English, and Swahili.
5. Manual annotation involves contextual judgment.
6. The binary framework does not capture all forms of health communication, including misinformation, disinformation, rumors, opinions, preventive advice, and official announcements.

## Future Work

Future development will focus on:

- Larger and more diverse datasets
- Multiple social-media platforms
- Multilingual analysis
- Improved annotation protocols
- Repeated cross-validation
- Confidence intervals
- Explainable AI
- Context-aware classification
- Prospective digital epidemic intelligence systems
- Integration of automated screening with human review

## Citation

If you use this repository or its methodology, please cite the associated research article:

> Sakubu D, Gatore Sinigirira KJ, Ntacoyanyimye S, Kamatari O, Havyarimana P, Ndikuryayo S, Irumva KC, Renguka LC, Bishola T, Niyukuri D.  
> **Detecting alarmist Mpox-related communication on Facebook in Burundi using machine learning and transformer-based models.**

## Funding

This work was supported by financial contributions from UK International Development and the International Development Research Centre (IDRC), Ottawa, Canada, under the **AI for Development: Responsible AI, Empowering People (AI4D) Program**.

The work also received support from the **UK Medical Research Council (MRC) through UK Research and Innovation (UKRI)**, in collaboration with Imperial College London (Grant No. 2029; Project PB4838).

## Authors

- Daxelle Sakubu
- Kelly Joelle Gatore Sinigirira
- Sadock Ntacoyanyimye
- Olivier Kamatari
- Pierre Havyarimana
- Symphorien Ndikuryayo
- Kelly Chandelle Irumva
- Luxe Claudia Renguka
- Tania Bishola
- David Niyukuri

### Affiliations

**University of Burundi**  
Doctoral School  
Department of Mathematics, Faculty of Science  
Institute of Applied Statistics

**Stellenbosch University**  
South African Centre for Epidemiological Modelling and Analysis  
Centre for Epidemic Response and Innovation

**University of Kinshasa**  
Department of Life Science

## License

MIT License

Copyright (c) 2026 David Niyukuri and co-authors

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.

---

> **Note:** The raw Facebook dataset is not included in this repository. Researchers interested in collaboration or access to permitted derived data should contact the study authors.

**14 April 2024 – 10 July 2026**

The dataset was predominantly French-language, with occasional English
terminology.

### Sources

The study included public posts from 13 Facebook pages, including:

- Ministère de la Santé Publique du Burundi
- Institut National de la Santé Publique du Burundi (INSP)
- UNICEF Burundi
- Croix-Rouge Burundi
- OMS Burundi
- Global Development Community Burundi
- US Embassy Bujumbura Burundi
- Bureau Régional de l'OMS en Afrique
- IOM Burundi
- Andika Magazine
- Jimbere Magazine
- Akeza.net
- Yaga

Private or restricted content, duplicate posts, unrelated advertisements, and
posts without sufficient textual information were excluded.

## Dataset Summary

The final dataset contains **357 Facebook posts**:

| Category | Number | Percentage |
|---|---:|---:|
| Informative | 321 | 89.9% |
| Alarmist | 36 | 10.1% |
| **Total** | **357** | **100%** |

The substantial class imbalance is an important characteristic of the
classification problem.

## Annotation

Each post was manually assigned to one of two categories.

### Informative

Posts presenting factual, evidence-based information intended to inform,
educate, or provide public-health guidance without exaggerated or
fear-amplifying framing.

### Alarmist

Posts containing exaggerated, sensationalized, fear-inducing, or
anxiety-amplifying language or framing concerning Mpox.

The annotation was performed by two annotators using predefined labeling
guidelines.

## Data Structure

The dataset contains variables describing textual content, publication
information, classification, and user engagement.

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

Engagement variables were **not used as predictors** in the classification
models. They were analyzed separately to characterize audience interaction.

## Analytical Workflow

```text
Facebook Posts
      │
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
