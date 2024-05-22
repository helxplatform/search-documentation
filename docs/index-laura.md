# Dug: Digging Up Data in the Dark 
<font color = "blue">(capitalize)</font>

<font color = "blue">Start with a brief introduction that defines Dug ... it's a search engine based on graphs, etc. etc. Include the information you had in this statement below: </font> To achieve this, <del>we</del> <font color = "blue">our data stewards</font> annotate study metadata with terms from [biomedical ontologies](http://www.obofoundry.org/), 
contextualize them within a unifying [upper ontology](https://biolink.github.io/biolink-model/) allowing study data 
to be federated with [larger knowledge graphs](https://researchsoftwareinstitute.github.io/data-translator/), 
and create a full text search index based on those knowledge graphs.<font color = "blue>up here</font>

<font color = "blue"> Note that my bullet headers in bold below are terrible. We should come up with better ones.</font>


## Key Features
- **FAIR**: Dug applies <delsemantic web and knowledge graph methods</del> to improve the [FAIR](https://www.go-fair.org/fair-principles/)-ness of research data. As an example, [dbGaP](https://www.ncbi.nlm.nih.gov/gap/) is a rich source of metadata about biomedical knowledge 
derived from clinical research like <del>the underutilized</del> [TOPMed](https://www.nhlbiwgs.org/) data sets. A key obstacle to 
leveraging this knowledge is the lack of researcher tools to navigate from a set of concepts of interest towards 
relevant study variables. <del>In a word, **search**.</del> <font color="blue">(This last sentence implies that searching is inherently semantic-webby but it's not. Searching predates semantic web. Additionally I don't think this last sentence is doing much for you.)</font>

	<font color = "blue">Comments/Suggestions for this bullet point:
	
	- Using both "semantic web" and "knowledge graph methods" is a bit redundant-sounding.
	- I also think that this sentence isn't doing a lot for you. It's really saying "Dug + FAIR" which is not meaningful, kind of like "Apples have seeds," leaving the reader with a "so what?" 
	- The example that follows attempts to add some meaning but I don't think it is particularly illustrative/effective. 
	- Additionally there nothing to explain or prove or verify how Dug does all this FAIR goodness. 
	- I suggest all the features you list here follow this formula:
		- Dug has **feature X** which is accomplished by **Y method**. An example is **Z example**. 
		- But use a better, action verb than my "has" above. For example: Dug makes promotes the findability, accessibility, interoperability, and reusability (FAIR) of research data by .... then be specific and provide evidence for each letter in FAIR... how does Dug make it findable? accessible? interoperable? reusable? Then provide a specific example that best captures all of the 4 FAIR letters. This may be hard, so you might only get 3 for example. That's okay but then you need to provide an example for the remaining letter. Otherwise you are asking the audience to take your word for it, and they need you to prove it to them instead.</font>



While other approaches to searching this data exist, our focus is semantic search: <del>For us, "relevant" is defined as</del><font color = "blue"> It's good that you are saying "for us" here and demarking the term as having OUR specialized meaning. However, I think Dug's search results should be relevant in the traditional sense. Additionally I don't think you need to redefine the term. I think what follows is important enough to stand on its own, and I actually think it is another key feature of Dug :).</font> 

- **Human Curated:** Dug is built on X number of biomedical ontologies, such as A, B, and C, manually curated by experts in the biomedical semantic community <font color="blue">OR SOMETHING LIKE THAT, WHATEVER JIM MIGHT SAY ABOUT HIS COMMUNITY.(The term peer-review I don't think applies and I would suggest steering clear of it unless you could guarantee to me that every single ontology Dug uses employs a true peer-review process. But I'm pretty sure they don't. Plus, I don't think it is needed anyway. The fact that expert ontologists come up with these ontologies is assurance enough that Dug is based on authoritative information.)</font> <del>Given a search term,</del> Dug returns results that are related based on connections in <del>ontological</del> <font color = "blue">(Treat ontology and KG as synonyms because in the context of Dug they are and to do otherwise makes your text wordy and redundant.)</font> biomedical knowledge graphs. <font color = "blue">This sentence is your punch. This answers the question of so what? Why should I care that it is human curated? Possibly you might want to revise the earlier sentence to say something like...</font> Dug returns results that expose connections between concepts in biomedical ontologies which <font color = "blue">IS IMPORTANT OR HELPFUL HOW? Now give me your Z example, and this example should make a good case for why human curated is best... so pick one that a machine wouldn't perform well with on its own.</font>

<del>To achieve this, we annotate study metadata with terms from [biomedical ontologies](http://www.obofoundry.org/), 
contextualize them within a unifying [upper ontology](https://biolink.github.io/biolink-model/) allowing study data 
to be federated with [larger knowledge graphs](https://researchsoftwareinstitute.github.io/data-translator/), 
and create a full text search index based on those knowledge graphs.</del>

- OTHERS? I suspect there are more features you could discuss here...


<font color = "blue">CUT Roger?
# Roger


Roger is a comprehensive pipeline toolkit designed to index datasets on a large scale. It leverages the internals of 
Dug, breaking them down into discrete tasks to facilitate the management of the indexing workflow with robust 
efficiency. The internal procedures of Dug are deconstructed into logical steps, enhancing the management of 
failures, restarts, and parallelization.</font>

