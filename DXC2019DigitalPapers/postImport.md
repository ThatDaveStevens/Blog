
### Matching to trends

~~~
match (t:transcript)
optional match (trend) 
where (trend:BusinessTrend or trend:TechnologyTrend)
AND lower(t.text) contains lower(trend.name)
merge (t)-[:MENTIONS]->(trend)
return t,trend
~~~

