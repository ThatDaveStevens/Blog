# Patterns from Assets

Organisations create many different types of assets to help articulate the value, implementation and usage of their products or services; Can we extract enough information from the simplest of representations to identify any common pattern or opportunity across this seemingly disconnected products?

![image](images/asset.png)<br>

By running the descriptive text and metadata for any given asset we have the potential to reveal some key insights

- Enabling technologies
- influencing business trends
- key industry terms
- target industry sectors


![image](images/assetsInsights.png)


## Find the patterns

By transferring or storing this information into a structured Graph database we can start to identify potential patterns.

:bulb: The following examples use [Neo4j (desktop)](https://neo4j.com/) and the [Neo4j Data Science Library](https://neo4j.com/developer/graph-data-science/graph-algorithms/) 

### Creating some sample data

- [Sample Data Script](scripts.cql)

![image](images/SampleData.png)<br>


### Simarility of Assets - Single points of reference

A simple similarity query is to identify which of the assets have share relationships to one of the common data points; maybe we wish to compare the assets for common enabling technologies.

![image](images/Pattern1.png)<br>

Within the Data Science Library we can create a sub-Graph of just this area.

~~~
CALL gds.graph.create('myGraph',['Asset','Technology'],'REALIZED')
~~~

~~~
CALL gds.nodeSimilarity.stream('myGraph')
YIELD node1,node2,similarity
RETURN gds.util.asNode(node1).name AS Asset1,gds.util.asNode(node2).name AS Asset2,similarity
ORDER BY similarity DESCENDING,Asset1,Asset2
~~~

Asset1|Asset2|similarity|
|---|---|---|
|"Asset B"|"Asset D"|0.66
|"Asset C"|"Asset D"|0.5
|"Asset A"|"Asset B"|0.25
|"Asset B"|"Asset C"|0.25
|"Asset B"|"Asset E"|0.25
|"Asset A"|"Asset C"|0.2
|"Asset A"|"Asset D"|0.2
|"Asset D"|"Asset E"|0.2

The result show us Assets B & D are similar with the highest set of common technologies.

![image](images/Example1.png)<br>

### Removing the graph from memory

~~~
CALL gds.graph.drop('myGraph')
~~~


## Similarity across 2 or more reference points

Whilst analysing a collection of assets against a single common reference point can highlight common patterns, we may wish to extend this to include other reference points; "Is there any similarity between both the enabling technologies and the high level business needs being addressed?"

![image](images/Pattern2.png)<br>


**TO-DO**



---

Reference work

[Patterns](../Patterns/readme.md)

---


---

[BACK](../README.md)

---