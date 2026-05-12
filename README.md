# Data Mining 2026 — Mini Project 1

**Course:** Data Mining  
**Term:** Spring Semester 2026  
**Author:** Engin Samet Dede

## Overview

This repository contains the first mini-project for the Data Mining course: an end-to-end sentiment-analysis study of Reddit comments. The main task analyses the *r/TwoXChromosomes* subreddit, and an additional bonus task replicates the same pipeline on *r/internationallaw* and extends it with sentence-level embeddings and additional classifiers. The work covers text cleaning, lexicon-based sentiment scoring with VADER, word and sentence embeddings (FastText and SBERT), classifier benchmarking, and the analysis of parent–child sentiment transitions in threaded discussions.

## Main Project — r/TwoXChromosomes

The main task investigates how sentiment is distributed across discussion threads of *r/TwoXChromosomes* and how it propagates from parent comments to their replies.

**Data preparation.** Comments are loaded into a pandas DataFrame, filtered for deleted or removed posts, and normalised through a custom `clean_text` function that lowercases the text, strips URLs and markdown artefacts, and removes non-alphabetic noise while preserving sentence boundaries.

**Sentiment labelling.** VADER (Valence Aware Dictionary and sEntiment Reasoner) is applied to every cleaned comment to obtain four scalar polarity scores (`neg`, `neu`, `pos`, `compound`). The compound score is then discretised using the official VADER thresholds: `compound >= 0.05` becomes POSITIVE, `compound <= -0.05` becomes NEGATIVE, and the remainder is labelled NEUTRAL.

**Exploratory analysis.** The compound-score distribution is visualised as a histogram with the empirical mean overlaid, and per-class counts are reported.

**Embeddings and classification.** The cleaned text is vectorised with FastText (sub-word, mean-pooled) and the resulting features are used to train and benchmark several supervised classifiers. A class-balancing step based on oversampling of the minority classes is applied before training, and a stratified 80/20 train–test split is used throughout. For every classifier a full classification report and a confusion-matrix heatmap are produced.

**Parent–child sentiment transitions.** Reddit's threaded structure is exploited by isolating comments whose `parent_id` starts with `t1_`, i.e. replies to other comments rather than to the submission itself. After a self-join with the parent-sentiment lookup, the resulting `(parent_sentiment, child_sentiment)` pairs are summarised in two heatmaps: a raw transition-count matrix and a row-normalised transition-probability matrix giving `P(child | parent)`.

## Additional Part

The additional part extends the project in two directions: a fresh corpus collected from a different subreddit, and a comparison between modern sentence embeddings and the FastText baseline.

### I. Custom subreddit — r/internationallaw

The original instructions ask for a custom subreddit scraped through the Reddit API with `praw`. After registering on Reddit and submitting a Developer Platform application with a full academic justification, the request was rejected under Reddit's *Responsible Builder Policy*, so the live scraping step was not available.

As a substitute, a pre-collected JSONL dump of *r/internationallaw* obtained from ArcticShift — a community-maintained successor of Pushshift — is used. ArcticShift exposes Reddit objects in their native field structure, which allows the downstream pipeline to run unmodified once a thin schema-mapping layer is added (renaming `id` to `comment_id`, `link_id` to `post_id`, and stripping the Reddit-internal `t3_` prefix). Post titles are merged into the comments frame on `post_id`, the cleaning rules of the main task are reapplied, and the resulting `InternationalLaw.csv` is fed into the same VADER-based sentiment and parent–child transition pipeline. *r/internationallaw* was chosen because its analytical, legal register is stylistically very different from the personal-narrative tone of *r/TwoXChromosomes*, and therefore provides a useful out-of-domain test of the same methodology.

### II. Advanced embeddings and classifier benchmark

The cleaned comments are re-embedded using Sentence-BERT (`all-MiniLM-L6-v2`), which produces 384-dimensional context-aware sentence vectors. SBERT encodes the full comment as a single semantic unit, which is generally a stronger representation for short and medium-length discussion posts than averaged sub-word vectors. After random oversampling brings every sentiment class up to the size of the majority class and a stratified 80/20 split is applied, three classifiers are trained and evaluated on identical features for direct comparison:

- **K-Nearest Neighbours** (`n_neighbors=5`) — non-parametric, distance-based baseline.
- **Logistic Regression** (`max_iter=1000`) — linear discriminative model.
- **Gradient Boosting** (`n_estimators=100`) — non-linear tree ensemble.

For every model a full classification report (precision, recall, F1 per class, accuracy), a confusion-matrix heatmap, and the overall test accuracy are reported, and the three test accuracies are compared in a single bar chart at the end of the section.

## Methods and Libraries

The project relies on the following Python libraries:

- `pandas`, `numpy` for data manipulation.
- `nltk`, `vaderSentiment` for text preprocessing and lexicon-based sentiment scoring.
- `gensim` for FastText embeddings.
- `sentence-transformers` for SBERT embeddings.
- `scikit-learn` for classifiers, metrics, and train–test splits.
- `matplotlib`, `seaborn` for visualisations.

## Repository Structure
.
├── Mini_Project_1_SOLUTION.ipynb        # main notebook (all cells)
├── data/
│   ├── r_r_internationallaw_posts.jsonl
│   └── r_r_internationallaw_comments.jsonl
├── outputs/
│   ├── InternationalLaw.csv             # cleaned r/internationallaw corpus
│   └── InternationalLaw_with_sentiment.csv
└── README.md

## How to Run

The notebook is intended to be executed in Google Colab. The two JSONL dumps must be available at the paths referenced in the data-loading cell of the additional part. Running every cell in order reproduces all figures, metrics, and CSV exports.

Engin Samet Dede
