# Dug: Digging up Dark Data

Dug is a semantic search engine that uses ontological knowledge graphs to to suggest relevant connections between biomedical datasets used in peer-reviewed research. It standardizes metadata from diverse public sources and employs natural language processing to extract ontology identifiers to build a graph of study variables and related concepts. Using Translator Query Language (TranQL), Dug enriches the graph with additional connections, and indexes study variables and ontology terms to an Elasticsearch endpoint, queried via the Dug API.

The API processes natural language queries, retrieving results from the index to display relationships between ontology terms and datasets, organized by data type or format. Results include only ontology concepts and public metadata&mdash;no sensitive data (e.g., PII, PHI) is included [1].

Dug leverages knowledge graphs to enhance the discovery of relevant research data in a way that aligns with the [FAIR principles](https://www.go-fair.org/fair-principles/). For instance, [dbGaP](https://www.ncbi.nlm.nih.gov/gap/) serves as a valuable repository of biomedical information, featuring datasets like [TOPMed](https://www.nhlbiwgs.org/) that can otherwise be challenging to navigate. Unlike other search methods, Dug prioritizes connecting user search terms to curated, peer-reviewed biomedical knowledge derived from clinical research, ensuring more precise and meaningful results.

### Features

- **Annotating Metadata with Biomedical Ontologies**: Study metadata is enriched using standardized terms from biomedical ontologies, such as those available through the [OBO Foundry](https://bioportal.bioontology.org/). This practice ensures consistent terminology, promoting data integration and interoperability across various datasets. 

- **Contextualizing with the Biolink Model**: The annotated metadata are organized using the [Biolink Model](https://biolink.github.io/biolink-model/), an upper ontology that standardizes types and relationships in biological knowledge graphs. This model provides a unified framework, enabling diverse datasets to be linked and understood in a cohesive manner.

- **Federation with Larger Knowledge Graphs**: By aligning study data to the Biolink Model, the information can be integrated with larger knowledge graphs, such as those created by the NCATS Biomedical Data Translator program. Federation allows for comprehensive data linkage and discovery across multiple datasets.

- **Creating a Full-Text Search Index**: Using knowledge graphs, Dug constructs a full-text search index to enable efficient and precise searches across integrated data. This supports semantic queries, fostering deeper insights and advancements in biomedical research.


# Roger

Dug is available in two versions to suit different needs: Core Dug and Roger. Core Dug is a standalone platform that requires minimal configuration and offers limited customization, making it ideal for users managing smaller datasets with fewer anticipated changes. On the other hand, Roger is a powerful, modular processing pipeline designed for indexing large-scale datasets. It provides advanced customization options, including the ability to integrate custom-built parsers and annotators, greater flexibility for incorporating new datasets, enhanced precision for isolating and troubleshooting pipeline components to improve efficiency, and asset backup capabilities using the repository of your choice.

---

1. *Waldrop, Alexander M., et al. (1 Jan. 2021). Dug: A Semantic Search Engine Leveraging Peer-Reviewed Literature to Span Biomedical Data Repositories. bioRxiv, Cold Spring Harbor Laboratory, www.biorxiv.org/content/10.1101/2021.07.07.451461v1.*