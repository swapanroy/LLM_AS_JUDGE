# 🩺 LLM-as-a-Judge Evaluation Pipeline for Clinical Text Summarization

An automated, enterprise-grade evaluation pipeline that implements **LLM-as-a-Judge** auditing for clinical text summarization. This project processes real-world medical transcriptions, generates structured clinical summaries using Anthropic Claude, and independently audits those outputs using a dual-model cross-family framework (**Claude** and **Google Gemini**).

## 🚀 Key Features

* **Real-World Clinical Data:** Utilizes the MTSamples Medical Transcriptions dataset (`tboyle10/medicaltranscriptions`), covering text notes across 40 distinct medical specialties.
* **Dual-Model Cross-Family Auditing:** Eliminates self-preference bias by having two independent model families evaluate the same summaries against a standardized multi-dimensional rubric.


* **Rigorous Scoring Rubric:** Scores summaries on a 1–5 scale across five critical dimensions: *Factuality, Completeness, Relevance, Safety,* and *Presentation*, complete with concise rationales.
* **Statistical Agreement Metrics:** Computes **Quadratic Weighted Kappa (QWK)** and **Spearman Rank Correlation ($r$)** to quantify inter-rater reliability between independent LLM judges.


* **Production-Grade Resilience:** Features robust multi-tier JSON parsing fallbacks and exponential backoff retry mechanisms to gracefully handle API rate limits and malformed outputs.

---

## 📐 Evaluation Dimensions

| **Dimension** | **Measured Metric** | **Clinical Significance** |
| --- | --- | --- |
| **Factuality** | Hallucination Detection | Verifies all summary claims are strictly grounded in source notes. |
| **Completeness** | Information Omission | Ensures core diagnoses, key findings, and care plans are fully captured. |
| **Relevance** | Noise Reduction | Confirms exclusion of extraneous conversational clutter. |
| **Safety** | Harm Mitigation | Flags unsafe medical guidance, harmful advice, or severe factual drift. |
| **Presentation** | Usability & Clarity | Validates structure for rapid physician review. |

---

## 🛠️ Tech Stack & Requirements

* **Language:** Python 3.8+
* **APIs:** Anthropic API (Claude) & Google GenAI API (Gemini)
* **Data Processing & Stats:** `pandas`, `numpy`, `scikit-learn` (`cohen_kappa_score`), `scipy` (`spearmanr`)

---

## ⚙️ Core Architecture & Code Highlights

1. **Robust JSON Parsing (`parse_scores`):** Handles variable LLM output formatting via a multi-tier fallback strategy (Direct JSON parsing $\rightarrow$ Regex block extraction $\rightarrow$ Per-key regex extraction).
2. **API Fault Tolerance:** Implements exponential backoff (`time.sleep(2 ** attempt)`) across multiple retry attempts to ensure uninterrupted pipeline execution.
3. **Statistical Integrity:** Automatically calculates ordinal agreement matrices, handling sparse data or constant arrays gracefully.

---

## 📦 Quick Start

1. **Clone the repository:**
```bash
git clone https://github.com/your-username/clinical-llm-judge.git
cd clinical-llm-judge

```


2. **Install dependencies:**
```bash
pip install pandas numpy scikit-learn scipy anthropic google-generativeai

```


3. **Set your API keys as environment variables:**
```bash
export ANTHROPIC_API_KEY="your-anthropic-key"
export GEMINI_API_KEY="your-gemini-key"

```


4. **Run the pipeline notebook or script.**

---
## 📊 Sample Execution & Agreement Metrics

The table below demonstrates pipeline output comparing how consistently Claude and Gemini scored a stratified batch of 6 notes across five standard evaluation dimensions:

* **`qwk` (Quadratic Weighted Kappa):** Measures ordinal agreement between the two model judges (ranging from -1.0 to 1.0).
* **`spearman_r` (Spearman Rank Correlation):** Measures monotonic rank-ordering correlation between the judges.

### Dimension Breakdown:
* **Relevance (`qwk` = 1.00):** Perfect agreement between Claude and Gemini[cite: 3]. Both models scored relevance identically across all 6 notes. Note that `spearman_r` is `NaN` (Not a Number) because variance was zero (e.g., both models assigned the exact same score or maximum score to every note in this small sample, making rank correlation mathematically undefined).
* **Safety (`qwk` = 0.77, `spearman_r` = 1.00):** Strong ordinal alignment and perfect rank-order correlation, demonstrating that both LLM judges align heavily on identifying clinical safety risks.
* **Factuality (`qwk` = 0.60, `spearman_r` = 0.78) & Presentation (`qwk` = 0.60, `spearman_r` = 0.78):** Both dimensions hit the $\ge 0.60$ threshold, representing good cross-model consistency.
* **Completeness (`qwk` = 0.27, `spearman_r` = 0.42):** Shows lower agreement. This indicates that evaluating missing medical information is more subjective for LLMs, with Claude and Gemini interpreting clinical omission thresholds differently on this specific batch.

---

## 📄 License

Distributed under the MIT License. See `LICENSE` for more information.

*Note: Readme was created using Gemini.*
