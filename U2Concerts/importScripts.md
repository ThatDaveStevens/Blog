LOAD csv with headers from "file:///U2Concerts.csv" as loadFile
MERGE (c:Country {name:loadFile.Country})
MERGE (city:City {name:loadFile.City})
MERGE (v:Venue {name:loadFile.Venue})
MERGE (t:Tour {name:loadFile.Tour})
with c,city,v,t,loadFile

MERGE (c)<-[:LOCATED]-(city)
MERGE (t)-[:VISTED]->(city)
MERGE (t)-[:PLAYED {date:loadFile.Date}]->(v)
RETURN c,city,v,t