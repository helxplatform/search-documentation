# Dug Architecture diagram

![Dug Architecture](https://github.com/helxplatform/search-documentation/assets/14827177/c002667a-a025-4954-8e80-b054830ab6cb)
Dug consists of two primary components: the Dug API service responsible for orchestrating metadata ingestion, indexing, and search, and the Dug search web portal, which presents search results to end users.

## Components
1. Dug API Service: This component is responsible for managing the entire process of metadata ingestion, indexing, and search orchestration.

2. Dug Search Web Portal: The web portal serves as the interface for end users to interact with the search functionality and view results.

3. Elasticsearch: The underlying search engine used for indexing and retrieving metadata records.

### Data Ingestion
Dug's ingestion pipeline abstracts retrieval modes and data parsing formats to accommodate diverse metadata formats available across public data repositories. It parses various metadata formats into a common DugElement metadata model, akin to the Data Tags Suite (DATS) metadata schema. Developers can extend Dug's plug-in interface to parse input data into DugElement objects, making it adaptable to nearly any metadata format.

### Data Annotation
Dug's annotation module extracts biomedical ontology identifiers from ingested metadata elements using tools for Named Entity Recognition (NER). The module leverages the Monarch Initiative's Biolink API to retrieve ontological identifiers and additional information. Developers can extend Dug's annotation interface by creating child classes to specify new API endpoints.

### Concept Expansion
Concept expansion further annotates ontological identifiers by identifying relevant connections within ontological knowledge graphs. This process enhances search results by establishing connections between entities based on predicates defined in the Biolink upper ontology.

### Data Indexing
After annotation and concept expansion, the resulting data structure is indexed using Elasticsearch. Dug's back-end search architecture utilizes linked Elasticsearch indices for speed and flexibility. Indexed metadata records include search terms extracted from annotations and expanded concepts. Dug organizes search results by partitioning metadata records, core ontological concepts, and expanded knowledge-graph answers into separate Elasticsearch indices.

### Search Engine
#### Search Functionality
Dug's search API exposes endpoints for querying each underlying Elasticsearch index:

`/search_var`: Search for study variables matching a user's query.
`/search_concepts`: Search for ontological concepts matching a user's query.
`/search_kg: Search` for knowledge-graph answers matching a user's query and an ontological concept ID.


