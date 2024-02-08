# Roger Components Architecture diagram
![image](../img/Roger%20Architecture.png)


# Pipeline architecture description

The primary function of the Roger package is to allow the data pipeline steps in
Dug to be broken down into sizes that can be managed by orchestration services
(whether that's a command line, a Makefile, Apache Airflow, or other).

## Knowledge graph building pipeline

A key component of the Dug system takes concepts that have been found and
annotated in source data sets and expands them to a broader range of associated
concepts. By doing this, searches on a particular concept will also find results
which are closely related in the semantics of the knowledge graph.

In order for this system to work, the knowledge graph must be fully built and
loaded into the knowledge graph database which is then accessed through a
knowledge graph API. This pipeline produces those graphs using a bulk loader
process which translates an on-disk knowledge graph to one stored in a
graph-aware database.

First, a collection of graphs are merged by matching nodes that are the same
between graphs. The resulting new, large graph is then prepared for loading
through the extraction of graph database schemata for the nodes and edges. Roger
then prepares CSV files of the full graph data, then loads them using the
RedisGraph bulk loader.

By the end of this process, the knowledge graph API server is ready to use for
concept expansion.

## Annotate and Index Pipeline

For Dug Search to work properly, it must have a base of documents that have been
indexed in ElasticSearch. This pipeline calls objects and procedures inside the
Dug code to first call a parser (configured specifically for each data
set). This extracts a list of elements which then gets passed to a crawler
object, which annotates the original files with both variables and concepts.

The variables documents get immediately loaded to ElasticSearch for indexing,
after which those indexes are validated.

The concept documents are further expanded using the concept knowledge
graph. The concepts and the expansions will then also be indexed to
ElasticSearch.

Finally, the process will produce a Transformer-compliant knowledge graph from
the annotations.

By the end of this process, for each input data set that has been processed, the
ElasticSearch server will have indexed fully annotated documents that allow the
search engine to perform semantic search queries.
