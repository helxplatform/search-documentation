# Dug: Digging up Dark Data

Dug is a semantic search engine that uses ontological knowledge graphs to
suggest relevant connections between validated public biomedical datasets. It
uses natural language processing to extract ontological identifiers from
standardized metadata, then builds a graph of study variables and related
concepts. Using Translator Query Language (TranQL), Dug enriches the graph with
additional connections, and indexes study variables and ontology terms to an
Elasticsearch endpoint, queried via the Dug API.

The API processes natural language queries, retrieving results from the index to
display relationships between ontological terms and datasets, organized by data
type or format. Results include only ontological concepts and public
metadata&mdash;no sensitive data (e.g., PII, PHI) are included [1].

Dug uses knowledge graphs to enhance discovery of relevant research data in a
way that aligns with the [FAIR
principles](https://www.go-fair.org/fair-principles/). For instance, valuable
public data repositories such as [dbGaP](https://www.ncbi.nlm.nih.gov/gap/) and
[TOPMed](https://www.nhlbiwgs.org/) can otherwise be challenging to
navigate. Dug improves on other search methods by prioritizing connecting user
search terms to curated, peer-reviewed knowledge structures generated from
clinical research, ensuring more precise and meaningful results.

### Features

- **Annotating Metadata with Biomedical Ontologies**: Dug's data ingest
  pipelines annotate study metadata terms to link them to biomedical ontologies,
  such as those available through the 
  [OBO  Foundry](https://bioportal.bioontology.org/).  This practice ensures
  consistent terminology, promoting data integration and interoperability across
  various datasets.

- **Contextualizing with the Biolink Model**: The annotated metadata are
  organized using the [Biolink Model](https://biolink.github.io/biolink-model/),
  an upper ontology that standardizes types and relationships for use in
  biological knowledge graphs. This model provides a unified framework, enabling
  diverse datasets to be linked and understood in a cohesive manner.

- **Federation with Larger Knowledge Graphs**: By aligning study data to the
  Biolink Model, the information can be integrated with larger knowledge graphs,
  such as those created by the NCATS Biomedical Data Translator
  program. Federation allows for comprehensive data linkage and discovery across
  multiple datasets.

- **Creating a Full-Text Search Index**: Using knowledge graphs, Dug constructs
  a full-text search index to enable efficient and precise searches across
  integrated data. Dug's semantic queries foster deeper insights and
  advancements in biomedical research.


# Roger

Dug provides two different methods for orchestrating the ingest of study
metadata: Core Dug and Roger. Core Dug works through manual or user-scripted
execution of ingest tasks, requires minimal configuration, and offers limited
customization. This makes it ideal for users managing smaller datasets with
fewer anticipated changes. On the other hand, Roger is a powerful, modular
processing pipeline designed for indexing large-scale datasets. It provides
advanced customization options, including the ability to integrate custom-built
parsers and annotators, greater flexibility for incorporating new datasets,
enhanced precision for isolating and troubleshooting pipeline components to
improve efficiency, and asset backup capabilities using the repository of your
choice.

---

1. *Waldrop, Alexander M., et al. (1 Jan. 2021). Dug: A Semantic Search Engine
   Leveraging Peer-Reviewed Literature to Span Biomedical Data
   Repositories. bioRxiv, Cold Spring Harbor Laboratory,
   www.biorxiv.org/content/10.1101/2021.07.07.451461v1.*
