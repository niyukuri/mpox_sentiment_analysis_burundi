# Detecting Alarmist Mpox-Related Communication on Facebook in Burundi

Machine learning and transformer-based NLP for detecting alarmist communication
in Mpox-related Facebook posts in Burundi.

## Overview

Social media platforms have become important channels for disseminating public
health information during infectious disease outbreaks. At the same time,
misinformation, sensationalized narratives, and emotionally charged content can
complicate public understanding of disease risks.

This project investigates the use of natural language processing (NLP) and
machine learning to distinguish between **informative** and **alarmist**
Mpox-related communication on Facebook in Burundi.

The study compares three traditional machine-learning algorithms:

- Logistic Regression
- Naïve Bayes
- Support Vector Machine (SVM)

with two transformer-based language models:

- CamemBERT
- XLM-RoBERTa

The project also includes secondary analyses of **sentiment** and **Facebook
user engagement**.

## Research Objectives

The study aims to:

1. Characterize sentiment and engagement patterns in Mpox-related Facebook
   communication in Burundi.
2. Compare traditional machine-learning and transformer-based models for
   distinguishing informative and alarmist communication.
3. Examine model misclassification patterns and identify contextual challenges
   relevant to automated digital epidemic intelligence.

## Dataset

The dataset consists of publicly available Facebook posts related to Mpox and
Mpox prevention in Burundi.

### Data collection

Posts were manually collected from public Facebook pages using predefined
search terms in French, English, and Kirundi, including:

- `Mpox Burundi`
- `MonkeyPox Burundi`
- `Variole du singe Burundi`
- `Akaranda`
- `Ibihara vy’inkende`
- `Mpox Vaccin Burundi`

The study period was:

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
