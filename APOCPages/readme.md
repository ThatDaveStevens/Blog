# Don't forget to turn the page - Paged API calls with APOC

<div align="right">January 2022</div>
<div align="right">David Stevens</div>
<br>

In my last post I covered how to use the APOC procedure `apoc.load.jsonParams`](https://neo4j.com/labs/apoc/4.2/overview/apoc.load/apoc.load.jsonParams/) to load data from a Slack workspace into Neo4j to build a small social network and recommendations engine.   This small holiday project was a nice reminder of the ease of importing and working with data within Neo4j, but I forgot once important thing.   Data accessible via an API often has pages; you are not going to get a list and the details of 1000+ Slack channels in a single call.

In keeping with my approach for the baseline Slack application, can I keep page through the returned data and keep everything within Cypher?

So back to the Slack [conversations.list](https://api.slack.com/methods/conversations.list) API SDK, which states [paginating](https://api.slack.com/docs/pagination) is managed through the `response_metadata` section of the returned JSON and in particular the `next_cursor` value


~~~:JSON
    "response_metadata": {
        "next_cursor": ""
    }
~~~

Amending this value into your API request will take you to the next page of the data you wish to collect.   So how to manage this using only the Graph database and Cypher?

First I took the approach to create a working node call `Page_Cursor`, this would be a single node within the database where I would just update the cursor property at the end of each loop through.


_My original call to the conversations.list API was_

_`:param uri => "https://slack.com/api/conversations.list?limit=500"`_ <br>
_`:param token => "Bearer xoxp-12345567889-123455678" (not a real token ;) )`_


~~~
CALL apoc.load.jsonParams($uri,{Authorization:$token},null) yield value
UNWIND value.channels as item
MERGE (c:Channel {channelid:item.id,name:item.name,description:item.purpose.value,is_archived:item.is_archived})
MERGE (p:Person {SlackID:item.purpose.creator})
MERGE (p)-[:CREATED]->(c)
~~~

By amending the uri to include the `cursor` parameter I can retrieve the stored value from my node and use this to complete the required API call.

`:param uri1 => "https://slack.com/api/conversations.list?limit=500&cursor="`

~~~
//First retrieve the current next cursor value

MATCH (nc:NextCursor)
WITH nc.nc as ncx

//Use the next cursor value within the call to the Slack API
CALL apoc.load.jsonParams ($uri1 + ncx + $uri2,{Authorization:$token},null) yield value
UNWIND value.channels as item
MERGE (c:Channel {channelid:item.id,name:item.name,description:item.purpose.value,is_archived:item.is_archived})
MERGE (p:Person {SlackID:item.purpose.creator})
MERGE (p)-[:CREATED]->(c)
~~~

The trick now is to store the new `NextCursor` value within the `NextCursor` node.  As it's not possible to unwind against a JSON datastream at the top level, given the differences in structures, I make the same call again, but this time unwind from the `response_metadata` section and not the `channels` section as above.    This this call I can then simply overwrite the `next_cursor` property within the node, ensuring my next call will include the details of the next page.

~~~
//First retrieve the current next cursor value
MATCH (nc:NextCursor)
WITH nc

//Now overwrite it with the new value
CALL apoc.load.jsonParams ($uri1 + nc.nc + $uri2,{Authorization:$token},null) yield value
UNWIND value.response_metadata as item
SET nc.nc=item.next_cursor
~~~

These are the basic building blocks to store and step through pages within any API pagination call. 



