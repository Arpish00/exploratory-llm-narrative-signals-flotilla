
# Sentence-Level Entity–Label Associations from News Texts (LLM Exploration)

> ⚠️ **Important**: This is not part of any formal study. It is an informal model exploration intended to demonstrate how general-purpose LLMs behave when provided with clearly bounded label definitions. No normative conclusions, ground-truth claims, or domain assertions are made.

---

## Contents

```text
├── sentence_entity_label_pairs.csv         # LLM-inferred (entity, label) associations at sentence level
├── entity_graph.png               # Graph of filtered entity-label connections
├── methods_description.md                  # Overview of the method and filtering steps
├── LICENSE (CC BY-NC 4.0)                  # Non-commercial attribution license
└── README.md                               # You are here
````

---

## Dataset Description

### `sentence_entity_label_pairs.csv`

This file contains sentence-level mappings between named entities (people, organizations, geopolitical actors) and model-predicted interpretive labels.

**Fields:**

| Column Name  | Description                                                  |
| ------------ | ------------------------------------------------------------ |
| `article_id` | ID referencing the original article from which sentence came |
| `entity`     | Named entity in the sentence      |
| `sentence`   | Full sentence extracted from article                          |
| `label`      | Narrative label predicted by the model                       |
| `confidence` | Score from zero-shot classification (BART-MNLI)        |

---

## Label Set Used

This simplified label set was chosen for clarity and ease of inference:

* **Humanitarian** – Indicates suffering, aid, or civilian relief themes
* **Military** – Indicates armed action, combat, strikes, or troop movements
* **Political** – Indicates governance, statements, diplomatic positioning
* **Security** – Indicates threat, surveillance, or protection justifications
* **Neutral** – Indicates factual or procedural mention without emphasis

---

## Method Summary

1. **Sentence Extraction**: News articles were split into sentences and named entities extracted using `spaCy`.
2. **Zero-Shot Prediction**: Each sentence was evaluated for label alignment using `facebook/bart-large-mnli`.
3. **Confidence Thresholding**: Labels with ≥ 0.7 model confidence were retained.
4. **Filtering**: A secondary validation step using `google/flan-t5-large` was applied to:

   * Check semantic relevance of label to entity
   * Remove internal contradictions
   * Confirm action alignment for some categories
   * Filter out noisy or trivial content

See `methods_description.md` for mathematical formulation and prompts used.

---

## Visualizations

* **`entity_graph.png`**: Graph showing entity-label relations after filtering


This is intended to offer visual insight into how the model clustered narrative emphases around named actors.

---

## License & Use

This repository is shared under the **Creative Commons Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)** license.

* You are free to share, remix, and build upon the materials
* You must give appropriate credit
* You may not use it for commercial purposes

All article content referenced remains under the copyright of the original news publishers.

---

## Disclaimer

* No human annotation was performed.
* This is not a benchmark or manually verified dataset.
* Labels reflect model-generated interpretations, not truth claims.
* This repository is **not part of any formal academic publication** and is intended purely for demonstration and transparency.

---


## Related Work

This repository complements the methods described in the following preprint:

> Solanki, A. R. (2025, June 18). LLM-Inferred Narrative Frames in Geopolitical Conflict Reporting: An Exploratory Zero-Shot Approach. https://doi.org/10.31235/osf.io/u4sdf_v1

While the preprint focuses on different framing categories and LLM filters, this repository explores simplified label associations using the same underlying zero-shot methodology. These results are not included in the original paper and are shared independently for exploratory purposes.

