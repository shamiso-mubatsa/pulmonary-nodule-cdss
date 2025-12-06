# 🫁 Pulmonary Nodule CDSS 
NLP-Driven Clinical Decision Support System for Pulmonary Nodule Follow-up in Radiology Reports  
**Author:** Shamiso Mubatsa  
**Course:** SAT 5141 – Clinical Decision Support & AI Modeling  
**Date:** Fall 2025  

---

## Project Overview

This project develops a **Natural Language Processing (NLP)-based Clinical Decision Support System (CDSS)** that detects mentions of pulmonary nodules in chest radiology reports and generates automated follow-up recommendations based on extracted findings.

The CDSS performs three main tasks:

1. **Nodule Detection** – Classifies report text as containing a pulmonary nodule or not.  
2. **Attribute Extraction** – Identifies nodule size (mm) when present.  
3. **Recommendation Logic** – Applies a clinical rule engine based on the 2017 Fleischner Society Guidelines.

This project demonstrates how traditional NLP techniques (TF–IDF + Logistic Regression) can support radiologists and clinicians by improving workflow efficiency and reducing missed follow-ups.

---

## 📁 Repository Structure
pulmonary-nodule-cdss/
│
├── notebooks/
│ └── Pulmonary_Nodule_CDSS.ipynb # Main modeling & CDSS notebook
│
├── data/
│ ├── reports.csv # Cleaned radiology reports from OpenI
│ └── cdss_outputs.csv # CDSS recommendations for full dataset
│
├── README.md # Project documentation
└── presentation/ # Final presentation PDF

---

## Dataset Description

### **Source: OpenI (NIH / Indiana University Chest X-ray Collection)**  
Dataset link: 

Only the **free-text radiology reports** were used for this project.  
The original dataset includes >3,900 XML reports, which were:

- Parsed  
- Cleaned  
- Standardized  
- Converted into a structured CSV (`reports.csv`)

Final fields:

| Column | Description |
|--------|-------------|
| `report_id` | Report identifier from OpenI |
| `report_text` | Final cleaned radiology report |
| `label_nodule` | Weak label: 1 = nodule mentioned, 0 = no nodule |

---

## Weak Labeling Approach

Because expert-annotated nodule labels are not available in OpenI, **weak labels** were generated using keyword matching:

- “nodule”
- “nodular”
- “pulmonary nodule”
- “spiculated”
- “mass” (context filtered)
- “lesion” (context filtered)

### Why weak labels are justified:
- Common practice in radiology NLP (CheXpert, MIMIC-CXR, NIH14 all use text mining).
- Reports already contain the radiologist’s diagnostic assessment.
- Allows model training without manual annotation.
- Appropriate for a course project with limited time.

> **Limitation:** Weak supervision may miss subtle phrasing or clinical nuance and may underdetect nodules described indirectly.

This limitation is acknowledged later in the Discussion section.

---

## NLP Modeling Approach

### **Pipeline Summary**

1. **Text Cleaning & Normalization**  
   - Lowercasing  
   - Removing punctuation  
   - Removing non-informative phrases  
   - Converting to a clean narrative string  

2. **Vectorization**  
   - TF–IDF (bigrams + unigrams)  
   - Maximum vocabulary features selected  

3. **Classifier**  
   - Logistic Regression  
   - `max_iter=2000`  
   - Train/test split: 80/20  

4. **Evaluation Metrics**  
   - Precision  
   - Recall  
   - F1-score  
   - Support  

### **Model Performance (from this study):**

| Class | Precision | Recall | F1-score | Support |
|-------|----------|--------|----------|---------|
| No Nodule (0) | 0.92 | 1.00 | 0.96 | 634 |
| Nodule (1) | 1.00 | 0.66 | 0.79 | 152 |

**Overall Accuracy:** 0.93  
**Macro F1-score:** 0.88  

### Interpretation

- **High precision** for both classes → very few false positives.  
- **Lower recall** for nodule class → expected for weak labels and term-based modeling.  
- Still strong performance for a classic NLP approach on free-text radiology data.

---

## CDSS System Logic

After classification, reports flagged as “nodule detected” undergo:

### **1. Nodule Size Extraction**
The system uses regular expressions to detect size patterns:
"6 mm", "8-mm", "1.2 cm", etc

All centimeters are converted to millimeters.

### **2. Guideline-Based Recommendation Engine**

Rules based on Fleischner Society (simplified for educational use):

- **Nodule < 6 mm:**  
  → “No routine follow-up needed; consider patient risk.”
- **6–8 mm:**  
  → “Recommend CT follow-up at 6–12 months.”
- **> 8 mm:**  
  → “Recommend CT at ~3 months or PET/CT evaluation.”

All outputs are stored in `cdss_outputs.csv`.

---

## How to Run This Project

### **Option 1 — Run the Notebook in Google Colab**
1. Open the `.ipynb` file in Colab  
2. Upload the `/data/reports.csv` file when prompted  
3. Run all cells  
4. Outputs will generate:  
   - Predictions  
   - Nodule size extraction  
   - CDSS recommendations  
   - `cdss_outputs.csv`

### **Option 2 — Local Execution**
```bash
pip install -r requirements.txt   # if requirements file added later
python Pulmonary_Nodule_CDSS.ipynb

## 🔍 Discussion & Limitations

Weak Label Limitations

Keyword detection may miss subtle nodule descriptions

Some false negatives expected

No radiologist-confirmed ground truth available

Does not distinguish benign vs. malignant nodules

Model Limitations

TF–IDF loses semantic context

Logistic regression cannot understand clinical nuance

No contextual understanding like transformer models (e.g., BioBERT, ClinicalBERT)



Future Improvements

Upgrade to BERT embeddings

Fine-tune transformer models for radiology domain

Add additional clinical features (age, risk, smoking status)

Include image-based modeling (CT or CXR)

## 🎯 Conclusion

This project demonstrates the feasibility of using NLP to extract clinically relevant information from radiology reports and support decision-making for pulmonary nodule management.

The CDSS prototype shows how structured recommendations can be generated from unstructured text — saving clinician time and reducing the risk of missed follow-ups.

This work reflects real-world applications of radiology informatics, clinical AI, and evidence-based guideline automation.


If you use or extend this project, please credit:
Shamiso Mubatsa, SAT 5141 (2025)



