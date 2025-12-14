# Dsouza-Mark-Xavier_01fe22bcs086_NLP_Assignment

# Clinical NLP and Medical Coding Pipeline

## Overview

This project implements an end-to-end Clinical NLP pipeline that extracts structured information from unstructured clinical documents (PDF or raw text) and predicts relevant ICD-10, CPT, and HCPCS codes.

The solution is designed to be generic and extensible. It does not rely on hardcoded sample data and works on any uploaded clinical report. The pipeline combines semantic retrieval (RAG) with deterministic clinical normalization, which reflects how real-world medical coding systems are built.

---

## Problem Statement

Clinical reports are typically unstructured and difficult to process automatically. The goal of this project is to:

- Accept clinical reports as PDF or plain text
- Extract clinically meaningful entities such as diagnoses, procedures, and anatomical locations
- Predict appropriate ICD-10, CPT, and HCPCS codes
- Return the output in a structured JSON format

---

## High-Level Flow

Input (PDF or Text)  
→ Text Extraction  
→ Preprocessing  
→ Clinical Entity Normalization  
→ Vector-Based Retrieval (ICD / CPT)  
→ Rule-Based Clinical Prioritization  
→ Structured JSON Output  

---

## Technologies Used

- Python
- pandas
- pdfplumber
- sentence-transformers
- scikit-learn
- Regular Expressions (regex)

---

## Input Types Supported

### PDF Input
Clinical reports can be provided as PDF files. Text is extracted page by page using `pdfplumber`.

Example:
```python
process_clinical_input(pdf_path="Input data for Assignment.pdf")
```
Raw Text Input

Plain clinical text can also be passed directly.

Example:

process_clinical_input(raw_text=clinical_text)

Entity Extraction and Normalization

The system extracts and normalizes the following:

Clinical Terms

Derived from diagnosis statements, indications, findings, and complications using clinical context rather than strict string matching.

Examples:
- Colon cancer screening  
- Hemorrhoids  
- Sessile polyp  
- Polyp removal  
- No immediate complication  

Anatomical Locations

All anatomical locations mentioned in the report are captured, including segments noted as normal.

Examples:
- Rectum  
- Sigmoid colon  
- Cecum  
- Terminal ileum  

Procedures

Procedures are inferred based on both procedure names and techniques.

Examples:
- Colonoscopy  
- Cold snare polypectomy  
- Biopsy  

Medical Coding Strategy

ICD-10 Codes

Priority order:
- Explicit ICD-10 codes mentioned in the report  
- Vector-based retrieval using clinical terms  
- Clinical filtering to avoid unrelated symptom-only codes  

CPT Codes

CPT codes are inferred primarily based on procedure technique:
- Cold snare → 45385  
- Biopsy → 45380  
- Diagnostic colonoscopy → 45378  

HCPCS Codes

HCPCS codes are inferred when intravenous medications or supplies are detected  
(e.g., Propofol, Lidocaine).

Retrieval-Augmented Generation (RAG)

ICD-10 and CPT descriptions are embedded using SentenceTransformers.  
Clinical terms extracted from the report are used as semantic queries to retrieve the most relevant codes using cosine similarity.

RAG is used as a candidate generator, followed by deterministic clinical rules for final selection.

Output Format

The pipeline returns a structured JSON object:

{
  "Clinical Terms": [],
  "Anatomical Locations": [],
  "Diagnosis": [],
  "Procedures": [],
  "ICD-10": [],
  "CPT": [],
  "HCPCS": []
}

This format matches the expected output provided in the assignment examples.

How to Run

Install dependencies:

pip install pdfplumber sentence-transformers scikit-learn pandas

Ensure the following reference files are present:
- ICD_code_Assignment.xlsx  
- cpt_code_assignment.xlsx  

Run the script or notebook and call:

process_clinical_input(pdf_path="your_report.pdf")

or

process_clinical_input(raw_text=clinical_text)

Design Rationale

Pure RAG is insufficient for medical coding due to clinical specificity requirements.  
Pure rule-based systems lack flexibility and scalability.

A hybrid approach provides both accuracy and explainability.

This design mirrors how production healthcare NLP systems are typically implemented.
