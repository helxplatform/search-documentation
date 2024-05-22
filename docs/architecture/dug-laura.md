<font color="blue">The page is currently organized like so:

- Diagram

- Components

- Functionality

I suggest organizing by components OR by functionality rather than both. Actually since the audience is developers, I recommend organizing by components/arch, and then with each component of the architecture, discuss its purpose, i.e., the functionality it supports. 

So:

- Keep your Diagram
- Keep the caption and combine with the components list and make this your introduction
- Put the diagram under this introductory paragraph
- Then list each component in the diagram (also briefly defined in your intro) and for each component, discuss the relevant functionality... 


For example....</font>

---------


Dug consists of FIVE OR MORE REALLY? primary components: 

- the API - definition here, 

- the UI - definition here,

- Elasticsearch Cluster - etc., 

- the store of variables/etc. - etc., 

- Data Dev Kit - etc.,

- I think you need to discuss the Annotation Model, and

- and the DugElement Metadata Model.

![Dug Architecture](https://github.com/yskale/search-documentation/assets/14827177/b4037930-8a41-4e01-9fb9-6db1cd5eb203)

## The Dug API Service
Functionality discussion (like what you have below)

## The Dug User Interface
etc.


and on down through the components.

<font color="blue">[Then I think it would be great if each component linked to a page that described them in more detail, along with whatever a developer needs to know about each component to install/admin/manage it. I think you and I discussed doing away with the Roger documentation. I think you said that using Roger in conjunction with Dug is optional, i.e., Roger is not a required component of Dug. I think you said you thought it made better sense to instead put the Roger documentation on a different site and then on this site to link to that and introduce Roger as an optional thing you, the reader, can use if you deem it helpful. Is that your recollection?]</font>




-------

Existing page content....




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
Dug provides a tool chain for the ingest, annotation, knowledge graph representation, query, crawling, indexing, and search of datasets with metadata. More detail can be found at (https://github.com/helxplatform/dug#the-dug-data-development-kit-ddk)

### Search Functionality
Dug's search API exposes endpoints for querying each underlying Elasticsearch index:

`/search_var`: Search for study variables matching a user's query.

`/search_concepts`: Search for ontological concepts matching a user's query.

`/search_kg: Search` for knowledge-graph answers matching a user's query and an ontological concept ID.


