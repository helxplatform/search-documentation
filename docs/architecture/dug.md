# Dug Architecture
![Dug Architecture](https://github.com/yskale/search-documentation/assets/14827177/b4037930-8a41-4e01-9fb9-6db1cd5eb203) 

Dug consists of two primary components: the [**Dug Data Development Kit**](#the-dug-data-development-kit-ddk) responsible for orchestrating metadata ingestion, annotation and indexing,  and the [**Dug Search API**](#dug-search-api), which presents search results to API consumers (eg. UI).


## The Dug Data Development Kit (DDK)
Dug provides a tool chain for the ingest, annotation, knowledge graph representation, query, crawling, indexing, and search of datasets with metadata. More detail can be found at (https://github.com/helxplatform/dug#the-dug-data-development-kit-ddk).
This Kit provides modular classes that can be swapped by developers to do custom data parsing (Ingest) and also specialized annotations. 

#### Data Ingestion
Dugs ingestion pipeline abstracts retrieval modes and data parsing formats to accommodate diverse metadata formats available across public data repositories. It parses various metadata formats into a common DugElement metadata model.
Users of the toolkit can also write their own parsers to convert their datasets into this common model. 

#### Data Annotation
Dug employs a modular approach to annotation as well. The default implementation provided in the toolkit does the following steps.

##### Named Entity Recognition (NER) and Entity Linking
Dug’s annotation module uses Named Entity Recognition (NER) to extract biomedical entities from free-text descriptions of parsed data. These entities are then linked to ontology identifiers by interfacing with external services, such as RENCI’s Sapbert API. Together, NER and entity linking enrich the metadata with semantic context, ensuring that search results are more accurate and meaningful.
##### Synonymization
Ontologies typically define a set of synonyms for each entity, allowing the same concept to be referenced in multiple ways. This increases coverage and improves entity recognition. To expand synonym mappings, we integrate with the [NCATS Data Translator Name Resolution service](https://name-resolution-sri.renci.org/docs#/lookup/lookup_names_post_synonyms_post)
, which provides standardized synonyms for entities. 

#### Concept Expansion
##### Knowledge Graph Representation
Concept expansion in Dug is facilitated through the use of knowledge graphs, where nodes represent entity types (e.g., diseases, genes) and edges describe relationships between entities. This structured representation enables Dug to establish connections between ontological identifiers and enrich metadata with contextual information.

##### Integration with Biolink Upper Ontology
Dug leverages the Biolink upper ontology to define connections across domain-specific ontologies. This integration allows for the establishment of meaningful relationships between entities, enhancing the semantic capabilities of the system.

#### Data Indexing
After annotation and concept expansion, the resulting data structure is indexed using Elasticsearch. Dug's back-end search architecture utilizes linked Elasticsearch indices for speed and flexibility. Indexed metadata records include search terms extracted from annotations and expanded concepts. Dug organizes search results by partitioning metadata records, core ontological concepts, and expanded knowledge-graph answers into separate Elasticsearch indices.

## Dug Search API

Dug search API exposes endpoints for querying each underlying Elasticsearch index:

`/search_var`: Search for study variables matching a user's query.

`/search_concepts`: Search for ontological concepts matching a user's query.

`/search_kg: Search` for knowledge-graph answers matching a user's query and an ontological concept ID.


