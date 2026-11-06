---
language:
- en
- pt
- es
- de
- fr
license: mit
task_categories:
- text-classification
task_ids:
- intent-classification
tags:
- multilingual
- intent-classification
- customer-service
dataset_info:
  features:
  - name: text
    dtype: string
  - name: label
    dtype: string
  - name: language
    dtype: string
  splits:
  - name: train
    num_examples: 170511
  - name: validation
    num_examples: 7726
  - name: validation_real
    num_examples: 5201
  - name: test
    num_examples: 7727
pretty_name: SID
configs:
- config_name: default
  data_files:
  - split: train
    path:
    - "train.csv"
  - split: test
    path: "test.csv"
  - split: validation
    path: "val.csv"
  - split: validation_real
    path: "val_real.csv"
---

# Dataset Card for SID (Synthetic Intent Dataset)

SID (Synthetic Intent Dataset) is a high-quality, augmented multilingual dataset designed for training robust intent classifiers for customer service chatbots. It covers **101 distinct intents** across **11 categories** and **5 languages**.

## Dataset Summary

The dataset was curated and augmented to address common challenges in production NLU systems:
- **Typo Robustness**: Includes simulated physical keyboard errors (QWERTY proximity).
- **Grammatical Diversity**: Includes simulated grammatical errors across all supported languages.
- **Entity Volatility**: Features resampled entities (dates, IDs, values) to prevent overfitting.
- **Domain Specificity**: Optimized for customer support, billing, and technical assistance.

### Supported Languages
- English (en)
- Portuguese (pt)
- Spanish (es)
- German (de)
- French (fr)

## Dataset Details

- **Curated by:** @Luigicfilho
- **Funded by:** @Luigicfilho
- **Shared by:** @Luigicfilho
- **Language(s) (NLP):** English (en), Portuguese (pt), Spanish (es), German (de), French (fr)
- **License:** MIT

### Dataset Sources
- **Repository:** https://huggingface.co/datasets/Luigicfilho/sid

## Uses

### Direct Use
This dataset is intended for training and benchmarking NLU (Natural Language Understanding) models, specifically **Intent Classifiers** for multilingual customer service applications. It is optimized for robustness against typos and grammatical errors.

### Out-of-Scope Use
- Use in life-critical systems where incorrect intent classification could lead to physical harm.
- Use with languages not included in the metadata.
- Fine-tuning for tasks other than text classification (e.g., generation).

## Dataset Structure

### Data Fields
- `text`: The input utterance (string).
- `label`: The target intent (101 classes, string).
- `language`: ISO code of the utterance's language (string).

### Data Splits

| Split | Rows | Description |
| :--- | :--- | :--- |
| **train** | 170,511 | Augmented training set with typos and grammar simulation. |
| **validation** | 7,725 | Synthetic validation set for model selection. |
| **validation_real**| 5,201 | **High-priority** real-world human-verified evaluation set. |
| **test** | 7,725 | Synthetic test set for final performance reporting. |

## Intent Categories

The dataset includes 101 intents organized into the following categories:
- `greetings_and_social`
- `account_and_profile`
- `orders_and_purchases`
- `billing_and_payments`
- `product_and_service_info`
- `technical_support`
- `appointments_and_scheduling`
- `complaints_and_feedback`
- `information_and_navigation`
- `legal_and_compliance`
- `miscellaneous`


## Usage Example

The easiest way to start using the dataset is with `pandas` and `scikit-learn`. Here is a simple baseline for training:

```python
import pandas as pd
from sklearn.preprocessing import LabelEncoder
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.linear_model import SGDClassifier

# 1. Load the remediated training data
df = pd.read_csv("data/train.csv")

# 2. Encode labels
le = LabelEncoder()
df["label_id"] = le.fit_transform(df["label"].astype(str))

# 3. Quick Vectorization (TF-IDF)
vectorizer = TfidfVectorizer(ngram_range=(1, 2), max_features=20000)
X = vectorizer.fit_transform(df["text"].astype(str))

# 4. Train a fast linear classifier
clf = SGDClassifier(loss="log_loss")
clf.fit(X, df["label_id"])

# Example Prediction
sample = ["I need to cancel my order please"]
vec_sample = vectorizer.transform(sample)
print(f"Predicted: {le.inverse_transform(clf.predict(vec_sample))[0]}")
```

## Dataset Creation

### Curation Rationale
Real-world chatbot users frequently make typing mistakes and grammatical errors. Most public datasets are "too clean," leading to model failure in production. SID was created to provide a specialized benchmark for **Typo-Robustness** and **Grammatical Variance**.

### Source Data
#### Data Collection and Processing
The dataset was initially generated using synthetic seed templates for each intent. It was then processed through an automated augmentation pipeline:
1. **Typo Simulation**: Using QWERTY-proximity character substitution.
2. **Grammar Simulation**: Injecting common grammatical errors (verb conjugation, gender agreement) for all 5 languages.
3. **Entity Resampling**: Replacing dates, prices, and IDs with random values to prevent overfitting.
4. **UTF-8 Normalization**: Ensuring consistent handling of accents and special characters.

#### Who are the source data producers?
Generated and augmented by @Luigicfilho using specialized NLU augmentation engines.

### Annotations
#### Annotation process
Automated generation based on predefined intent mapping in `intents.json`.

#### Who are the annotators?
Automated script with manual spot-checking for the `validation_real` set.

## Personal and Sensitive Information
None. All Names, Order IDs, and Personal Data are synthetically generated or resampled.

## Bias, Risks, and Limitations
- The dataset is synthetic; while it simulates errors, it may not capture 100% of the nuance found in natural human-to-human conversations.
- Some niche cultural idioms might be underrepresented in the 5 languages.

## Recommendations
Users should be made aware that this dataset is heavily augmented. It is recommended to evaluate models on the `validation_real` split for the most accurate measure of production-readiness.

## Citation

### BibTeX:
```bibtex
@dataset{sid_synthetic_intent_2026,
  author = {Luigicfilho},
  title = {SID: Synthetic Intent Dataset},
  year = {2026},
  url = {https://huggingface.co/datasets/Luigicfilho/sid}
}
```

### APA:
Luigicfilho. (2026). SID: Synthetic Intent Dataset (1.0) [Data set]. Hugging Face. https://huggingface.co/datasets/Luigicfilho/sid

## Dataset Card Contact
@Luigicfilho