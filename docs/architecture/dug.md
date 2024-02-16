# Dug Architecture diagram
![Dug Architecture](https://github.com/yskale/search-documentation/assets/14827177/b4037930-8a41-4e01-9fb9-6db1cd5eb203) Dug consists of two primary components: the Dug API service responsible for orchestrating metadata ingestion, indexing, and search, and the Dug search web portal, which presents search results to end users.

## Components
1. Dug API Service: This component is responsible for managing the entire process of metadata ingestion, indexing, and search orchestration.

2. Dug Search Web Portal: The web portal serves as the interface for end users to interact with the search functionality and view results.

3. Elasticsearch: The underlying search engine used for indexing and retrieving metadata records.

### Data Ingestion
Dug's ingestion pipeline abstracts retrieval modes and data parsing formats to accommodate diverse metadata formats available across public data repositories. It parses various metadata formats into a common DugElement metadata model, akin to the Data Tags Suite (DATS) metadata schema. 

### Data Annotation
#### Named Entity Recognition (NER)
Dug's annotation module employs Named Entity Recognition (NER) techniques to extract biomedical ontology identifiers from free-text descriptions of study variables. This process enhances metadata quality and enables more accurate search results.

#### Ontological Identifier Extraction
The annotation module interfaces with external services, such as the Monarch Initiative's Biolink API, to extract ontological identifiers from metadata elements. These identifiers provide additional context and semantic meaning to the metadata.

### Concept Expansion
#### Knowledge Graph Representation
Concept expansion in Dug is facilitated through the use of knowledge graphs, where nodes represent entity types (e.g., diseases, genes) and edges describe relationships between entities. This structured representation enables Dug to establish connections between ontological identifiers and enrich metadata with contextual information.

#### Integration with Biolink Upper Ontology
Dug leverages the Biolink upper ontology to define connections across domain-specific ontologies. This integration allows for the establishment of meaningful relationships between entities, enhancing the semantic capabilities of the system.

### Data Indexing
After annotation and concept expansion, the resulting data structure is indexed using Elasticsearch. Dug's back-end search architecture utilizes linked Elasticsearch indices for speed and flexibility. Indexed metadata records include search terms extracted from annotations and expanded concepts. Dug organizes search results by partitioning metadata records, core ontological concepts, and expanded knowledge-graph answers into separate Elasticsearch indices.

### The Dug Data Development Kit (DDK)
Dug provides a tool chain for the ingest, annotation, knowledge graph representation, query, crawling, indexing, and search of datasets with metadata. More detail can be found at https://github.com/yskale/dug#the-dug-data-development-kit-ddk  

### Search Functionality
Dug's search API exposes endpoints for querying each underlying Elasticsearch index:

`/search_var`: Search for study variables matching a user's query.

`/search_concepts`: Search for ontological concepts matching a user's query.

`/search_kg: Search` for knowledge-graph answers matching a user's query and an ontological concept ID.


