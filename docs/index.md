# Dug: Digging up Dark Data

Dug applies semantic web and knowledge graph methods to improve the 
[FAIR](https://www.go-fair.org/fair-principles/)-ness of research data.

As an example, [dbGaP](https://www.ncbi.nlm.nih.gov/gap/) is a rich source of metadata about biomedical knowledge 
derived from clinical research like the underutilized [TOPMed](https://www.nhlbiwgs.org/) data sets. A key obstacle to 
leveraging this knowledge is the lack of researcher tools to navigate from a set of concepts of interest towards 
relevant study variables.

While other approaches to searching this data exist, our focus is semantic search: For us, "relevant" is defined as 
having a basis in curated, peer reviewed ontologically represented biomedical knowledge. Given a search term, 
Dug returns results that are related based on connections in ontological biomedical knowledge graphs.

### Features

- **Annotating Metadata with Biomedical Ontologies**: Study metadata are enriched using standardized terms from biomedical ontologies, 
such as those available through the [OBO Foundry](https://bioportal.bioontology.org/). This practice ensures consistent terminology, 
facilitating data integration and interoperability across various datasets. 

- **Contextualizing with the Biolink Model**: The annotated metadata are structured within the [Biolink Model](https://biolink.github.io/biolink-model/), an upper ontology 
that standardizes types and relationships in biological knowledge graphs. This model provides a unified framework, enabling diverse datasets to be integrated and understood in a cohesive manner.

- **Federation with Larger Knowledge Graphs**: By aligning study data with the Biolink Model, it becomes possible to federate this information with larger knowledge graphs, such as those developed by the NCATS Biomedical Data Translator program . 
This federation allows for comprehensive data linkage and discovery across multiple datasets.
- **Creating a Full-Text Search Index**: Leveraging the interconnected knowledge graphs, a full-text search index is constructed. This index enhances the ability to 
perform efficient and effective searches across the integrated data, supporting semantic queries and facilitating new insights in biomedical research.


# Roger


Roger is a comprehensive pipeline toolkit designed to index datasets on a large scale. It leverages the internals of 
Dug, breaking them down into discrete tasks to facilitate the management of the indexing workflow with robust 
efficiency. The internal procedures of Dug are deconstructed into logical steps, enhancing the management of 
failures, restarts, and parallelization.

