# Methodology Overview (Exploratory LLM Classification)

This repository contains the output of an exploratory language-model–based classification pipeline that uses simplified narrative categories to label sentence-level content in news articles.

The goal of this analysis was to test how large language models (LLMs) interpret basic thematic emphasis in individual sentences when prompted with general, clearly delimited category definitions. These categories were not designed to represent formal “media frames” in the traditional sense, but rather to serve as intuitive, easily distinguishable prompts for zero-shot inference.

This pipeline was **methodologically similar** to the author’s previous work on article-level framing using LLMs, but it:
- Used a simpler label set,
- Focused only on sentence-level associations,
- And is presented independently for demonstration and exploratory purposes.

> ⚠️ **Note:** These results are not peer-reviewed and should not be interpreted as a formal framing analysis. The narrative labels used here were intentionally simplified to examine LLM interpretability and consistency when given clearer categorical boundaries.


## Sentence-Level Thematic Labeling of Entities

This repository includes a structured dataset of model-inferred sentence-level associations between named entities and narrative categories. These associations were derived using a two-step process: zero-shot prediction using `facebook/bart-large-mnli`, followed by a semantic validation and filtering stage using `google/flan-t5-large`.

Let $ A = \{a_1, a_2, \dots, a_n\} $ denote the set of news articles. Each article $a_i \in A$ was split into sentences 
$ S_i = \{s_{i1}, s_{i2}, \dots, s_{im}\} $ using the `spaCy` dependency parser.

For each sentence $s_{ij}$, named entity recognition (NER) was applied to extract a set of named entities  
$E_{ij} = \{e_1, e_2, \dots, e_k\}$, where each $e_k$ was tagged as a `PERSON`, `ORG`, or `GPE`.

To identify possible narrative cues in each sentence, a set of simplified, general-purpose categories was used:
- Humanitarian  
- Military  
- Political  
- Security  
- Neutral  

Each sentence was passed to `facebook/bart-large-mnli` for zero-shot classification. For sentence $s_{ij}$, the model returned:

$
P_{ij} = \{p(f_1|s_{ij}), p(f_2|s_{ij}), \dots, p(f_5|s_{ij})\}
$

All categories with a model confidence of at least 0.7 were retained:

$
F_{ij}^* = \{f \in F \mid p(f|s_{ij}) \geq 0.7\}
$

For each entity  $e_k \in E_{ij}$ and each retained label $f \in F_{ij}^*$, the tuple  
$(a_i, e_k, s_{ij}, f, p(f|s_{ij}))$ was stored.

This generated a set of observations capturing how the model paired entities with inferred narrative categories:

$
\mathcal{R} = \{(a_i, e_k, s_{ij}, f, p(f|s_{ij}))\}
$

---

## Validation and Filtering

To reduce noise and improve interpretability, a multi-stage filter was applied using the `google/flan-t5-large` model. Let  
$ D = \{(s_i, e_i, f_i)\}_{i=1}^N $ be the set of candidate outputs.

### 1. Semantic Relevance Check

Prompted the model with:

> "Does the following sentence support a plausible interpretation of the label X in relation to entity Y?"

Only pairs with affirmative responses were retained:

$
D_1 = \{(s_i, e_i, f_i) \in D \mid \text{LLM}(P_{\text{verify}}(s_i, e_i, f_i)) = \text{Yes}\}
$

---

### 2. Contradiction Filtering

For label types prone to overuse (e.g., "Humanitarian"), a contradiction check was issued:

> "Does this sentence contradict the interpretation that entity Y is described in humanitarian terms?"

Negative answers were retained:

$
D_2 = D_1 \setminus \{(s_i, e_i, f_i) \mid f_i = \text{Humanitarian} \land \text{LLM}(P_{\text{contradict}}(s_i)) = \text{Yes}\}
$

---

### 3. Contextual Validation of Action-Based Labels

For categories like "Military" and "Political", prompts asked:

> "Is entity Y involved in an action that reflects the category X?"

$
D_3 = \{(s_i, e_i, f_i) \in D_2 \mid f_i \notin \{f\} \lor \text{LLM}(P_f(s_i, e_i)) = \text{Yes}\}
$

---

### 4. Noise Filtering

To discard metadata, headlines, or low-information content, a prompt asked:

> "Is this sentence mostly non-substantive (e.g., naming people, metadata)?"

$
D' = \{(s_i, e_i, f_i) \in D_3 \mid \text{LLM}(P_{\text{noise}}(s_i)) = \text{No}\}
$

This yielded the final set of validated associations.

---

**Note:** All labels were inferred from surface-level text using LLMs. They should not be interpreted as ground-truth annotations or editorial judgments.
