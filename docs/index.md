# Dug: digging up dark data

Dug applies semantic web and knowledge graph methods to improve the 
[FAIR](https://www.go-fair.org/fair-principles/)-ness of research data.

As an example, [dbGaP](https://www.ncbi.nlm.nih.gov/gap/) is a rich source of metadata about biomedical knowledge 
derived from clinical research like the underutilized [TOPMed](https://www.nhlbiwgs.org/) data sets. A key obstacle to 
leveraging this knowledge is the lack of researcher tools to navigate from a set of concepts of interest towards 
relevant study variables. In a word, **search**. 

While other approaches to searching this data exist, our focus is semantic search: For us, "relevant" is defined as 
having a basis in curated, peer reviewed ontologically represented biomedical knowledge. Given a search term, 
Dug returns results that are related based on connections in ontological biomedical knowledge graphs.

To achieve this, we annotate study metadata with terms from [biomedical ontologies](http://www.obofoundry.org/), 
contextualize them within a unifying [upper ontology](https://biolink.github.io/biolink-model/) allowing study data 
to be federated with [larger knowledge graphs](https://researchsoftwareinstitute.github.io/data-translator/), 
and create a full text search index based on those knowledge graphs.



# Roger

Roger is a pipeline toolkit built around dug to index datasets at scale. 
Roger makes use of Dug's internals by breaking them down into individual 
tasks making it easier to manage the indexing workflow in a robust manner. 
Dug's internal procedures are broken down into logical steps for better manging
failures , restarts and parallelization.