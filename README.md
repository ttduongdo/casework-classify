# Synthetic Constituent Email Classification Pipeline

This repository contains code for generating synthetic constituent emails and training a hierarchical classification model to route emails to relevant U.S. government agencies and programs.

The project was developed as part of a Smith College Statistical & Data Science capstone in collaboration with Civic. This repository reflects individual follow-up experimentation by Robbie. Because real constituent emails are private and sensitive, all training and evaluation are conducted using a carefully constructed synthetic dataset.

---

## Team Members

- **Robbie Do** — ttdo@smith.edu  
- Anabel Fletcher — amfletcher@smith.edu  
- Gabriela Eastwood — geastwood@smith.edu  
- Katarina Flöer — kfloer@smith.edu  
- Yuzhang Fu — rfu@smith.edu  

---

## Project Motivation

Legislative offices receive large volumes of constituent emails requesting assistance with federal programs such as healthcare, immigration, and housing. Correctly routing these emails requires substantial manual research by caseworkers.

This project aims to reduce that burden by:

- Generating realistic synthetic constituent emails
- Training an embedding-based classification model
- Predicting relevant agencies and issue areas using a tiered tagging system
- Producing a model that is fast, interpretable, and suitable for real-world casework triage

---

## Tiering System

Each email is labeled using a four-level hierarchy derived from  
*Toward a Unified Tagging System for Casework in the U.S. House of Representatives*  
by Megan Blackwood (Civic):

- **Tier 1:** Top Agency (e.g. Department of Health and Human Services)
- **Tier 2:** Sub-Agency
- **Tier 3:** Program Title
- **Tier 4:** Issue Area (e.g. Income / Employment, Immigration Status)

This structure mirrors how legislative caseworkers organize and route constituent requests.

---

## Synthetic Data Generation

Because real constituent emails are unavailable, the model is trained on approximately **10,000 synthetic emails** generated using a large language model (Amazon Titan Text).

### Overview of the Pipeline

1. **Tag Filtering**  
   Remove unstable or overly specific tags (e.g., year-based programs, temporary disaster relief) while preserving core casework categories such as healthcare, immigration, housing, and employment.

2. **Controlled Tag Sampling**  
   - Sample ~400 tag sets, each containing all four tiers  
   - Sampling is proportional across **(Top Agency × Issue Area)** pairs  
   - Ensures balanced representation across the hierarchy

3. **Prompt Construction & Persona Variation**  
   For each tag set, generate **27 stylistic variants** by varying:
   - Trust: low / medium / high  
   - Efficacy: low / medium / high  
   - Knowledge: low / medium / high  

   Prompts enforce consistent format, tone, and length while prohibiting explicit references to tier labels.

4. **Realism Injection**  
   Each email includes a realistic (synthetic) name, date, address, and email to mirror real constituent messages.

5. **Quality Control & Regeneration**  
   Generated emails are validated to remove:
   - Model refusal text
   - Prompt or tag leakage
   - Formatting violations or placeholder artifacts  

   Failed messages are regenerated using the original prompt until quality thresholds are met.

---

## Model Architecture

### Embeddings
- **Model**: Amazon Titan Text Embeddings v2  
- **Dimension**: 1024  
- **Normalization**: Enabled  

Each email is converted into a dense semantic vector representing its meaning in embedding space.

### Inference via Centroids
- For each label (e.g., Top Agency), compute the **centroid embedding** using training samples.
- At inference time, assign a new email to the label whose centroid is closest in cosine similarity.

**Advantages**
- Parameter-free
- Fast inference
- Interpretable decision process
- Well-suited for limited or synthetic data

### Contrastive Learning Extension
To improve embedding separability, a supervised contrastive learning step is applied:
- Frozen Titan embeddings as backbone
- Trainable projection head (MLP)
- Optimized using Supervised Contrastive Loss
- Produces a refined embedding space before centroid classification

---

## Evaluation & Error Analysis

Model performance is evaluated using **F1-score**, balancing precision and recall. All experiments use a consistent train/validation/test split for fair comparison.

Key sources of error include:
- Natural overlap between federal agencies
- Multi-agency constituent issues
- Generic assistance-seeking language
- Limitations inherent to synthetic data

For a full walkthrough of the model design, evaluation, and error analysis, see the recorded technical presentation:

🎥 **Capstone Technical Talk (YouTube)**  
[Watch the presentation](<https://youtu.be/xcFrmYHX1do?si=XB7eYYeKuIbEbK_3>)

---

## Relevance & Use Cases

This system is designed to support — not replace — human caseworkers by:
- Surfacing likely relevant agencies and programs
- Reducing manual research time during case intake
- Enabling analytics on recurring constituent issues
- Supporting explainable, lightweight deployment in real workflows

---

## Reimplementation Instructions

1. Prepare a tiered tagging guide with all four levels
2. Run synthetic data generation to create labeled emails
3. Generate embeddings using Titan (or a compatible embedding model)
4. Train centroid-based classifiers (optionally with contrastive learning)
5. Evaluate using held-out synthetic data
6. Perform qualitative error analysis before deployment

---

## Future Work

- Stronger embedding models (e.g., sentence-BERT, Mistral)
- Multi-label or confidence-based routing
- Finer-grained classification at lower tiers
- Training with anonymized or semi-real casework data
- Continued development by future Capstone teams or Civic engineers

---

## Acknowledgments

Thanks to Julian, Megan, and Jon at Civic,  
Professor Nikko Stevens,  
and the Smith College SDS community.