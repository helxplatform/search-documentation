# Dug Architecture Overview

Dug is a semantic search framework designed to improve the findability of biomedical datasets (such as dbGaP and TOPMed). It bridges the gap between raw study metadata and biomedical knowledge graphs, allowing researchers to search for concepts (e.g., "heart attack") and find relevant study variables even if the specific term does not appear in the text.

## High-Level Architecture

Dug operates as a pipeline that ingests raw metadata, annotates it with ontology terms, expands those terms using knowledge graphs, and indexes the results for full-text search.


### 1. Core Data Pipeline
The pipeline consists of five distinct phases:

1.  **Ingest (Link):**
    * **Parsers:** Converts heterogeneous input formats (dbGaP XML, CSV, JSON) into standardized internal objects (`DugElement`).
    * **Supported Formats:** Includes dbGaP, TOPMed, HEAL, and NIDA data dictionaries.
2.  **Annotate:**
    * **NLP Tagging:** Extracts biological entities from variable descriptions using annotators like **Monarch** (SciGraph) or **Sapbert** (token classification).
    * **Normalization:** Standardizes identifiers (CURIEs) using the Translator SRI normalization service to ensure consistency across datasets.
3.  **Expand (TranQL):**
    * Uses **TranQL** to query knowledge graphs (e.g., ROBOKOP).
    * Expands annotated concepts to find related terms (e.g., linking "Phenotype" → "Disease" → "Anatomical Entity") to enrich the search index.
4.  **Index:**
    * Consolidates annotated variables and expanded concepts into documents.
    * Pushes structured data into **Elasticsearch** indices (`concepts_index`, `variables_index`, `kg_index`).
5.  **Search:**
    * Exposes data via a **FastAPI** service.
    * Supports queries for Concepts, Variables, and Knowledge Graph segments.

### 2. Key Data Models
Dug organizes data into three primary classes:

* **DugConcept:** Represents an ontological term (e.g., *Lung Cancer*). It acts as a hub linking multiple variables together.
* **DugVariable:** Represents a specific data element from a study (e.g., *Question 4: Do you have a cough?*).
* **DugStudy:** Represents the parent collection or clinical trial containing the variables.

### 3. Infrastructure Services
The system relies on the following containerized services:

* **API Service:** Python/FastAPI application handling search requests and crawler execution.
* **Elasticsearch:** Primary storage for indexed knowledge graphs and full-text search capability.
* **Redis:** Caching layer for high-volume service requests and annotator caching.
