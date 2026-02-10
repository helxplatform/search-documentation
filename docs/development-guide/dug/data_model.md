# Data Parser Data Models

All data parsers in Dug output objects that inherit from the base `DugElement` class. These models are defined using **Pydantic** to ensure type safety and consistent schema validation.

## 1. Base Model: `DugElement`
This is the parent class for all searchable items in the system. It contains the core metadata fields shared across studies, variables, and concepts.

* **id** (`str`): Unique identifier for the element (e.g., `phv0012345`).
* **name** (`str`): Human-readable label (e.g., "Age at diagnosis").
* **description** (`str`): Textual description or question text.
* **type** (`str`): Discriminator field (e.g., "variable", "study").
* **programs** (`List[str]`): The program(s) the element belongs to (e.g., "HEAL", "TOPMed").
* **action** (`str`): A URL to view the element in its source system.
* **parents** (`List[str]`): IDs of parent elements (e.g., a variable's parent study ID).
* **concepts** (`Dict[str, DugConcept]`): A dictionary of ontological concepts mapped to this element.
* **metadata** (`Dict[str, Any]`): Flexible dictionary for extra attributes (e.g., permissible values, units).

---

## 2. Specific Output Models
Parsers return an `Indexable` type, which is a Union of the following specific classes:

### A. `DugStudy`
Represents a high-level collection, such as a clinical trial or a dataset.
* **Inherits from:** `DugElement`
* **Fixed Type:** `"study"`
* **Specific Fields:**
    * `abstract` (`str`): A summary or abstract of the study.
    * `publications` (`List[str]`): Associated publication IDs or links.
    * `variable_list` (`List[str]`): A list of variable IDs contained within the study.

### B. `DugVariable`
Represents a single data point or question within a study.
* **Inherits from:** `DugElement`
* **Fixed Type:** `"variable"`
* **Specific Fields:**
    * `data_type` (`str`): The type of data (e.g., "text", "integer", "encoded value"). Defaults to "text".
    * `is_cde` (`bool`): Flag indicating if this variable is a Common Data Element (CDE). Defaults to `False`.

### C. `DugSection`
Represents a grouping of variables, such as a Case Report Form (CRF) or a specific section of a questionnaire.
* **Inherits from:** `DugElement`
* **Fixed Type:** `"section"`
* **Specific Fields:**
    * `is_crf` (`bool`): Flag indicating if this section represents a standardized Case Report Form.
    * `variable_list` (`List[str]`): A list of variable IDs contained in this section.

### D. `DugConcept`
Represents a harmonized ontological term (e.g., "Lung Cancer"). While usually generated during the **Annotation** phase, some parsers (like `TOPMedTagParser`) may output these directly.
* **Inherits from:** `DugElement`
* **Fixed Type:** `"concept"`
* **Specific Fields:**
    * `concept_type` (`str`): The semantic type (e.g., "Disease", "Anatomical Entity").
    * `identifiers` (`Dict`): A dictionary of equivalent knowledge graph identifiers.
    * `kg_answers` (`Dict`): TranQL knowledge graph answers associated with the concept.
