## Slack channels and members into Neo4j

<div align="right">December 2021</div>
<div align="right">David Stevens</div>

Small social network graph to identify common groups between people, in part this is inspired by a post by Michael Hunger on our internal "All nodes" slack channel; where he shared how to import and work with a subset of our HR dataset (person, department & location); could I extend this information with the Slack channels we belong too and make some recommendations for teams, new hires and generally mess about with Neo4j and some data.

![image](images/HRModel.png)<br>
_The starting datamodel from Michael's post_ 

![image](images/SlackModel.png)<br>
_The simple slack model_

![image](images/SlackModel2.png)<br>
_My combined working model_

_models drawn using [Arrows](https://arrows.app/)_



## Settting up a Slack application/AOIU

Before getting into Neo4j, we need to [set-up an API within Slack](https://api.slack.com/apps) to provide the information we need to complete the dataset.
Here I created a very simple API application with the following **User Token Scopes**

![image](images/SlackAPI.png)<br>

Once set-up, you just need to deploy the application into selected Slack workspace.



## Neo4j set-up
My baseline dataset was the internal Neo4j organisation structure which had been shared internally, so all we need to do is import the slack data into this existing Graph database.

In total 3 calls are required against the Slack API, the first is to retrieve the list of channels, the API is limited to only return public channels within the worksplace, so any private channels are not included.    The second call takes the channelID and retrieves the list of users who are members of the channel; here Slack provides just the users MemberID, so a 3rd call is required to get the actual name and email addresses of the channel members.

To help with data integrity I set-up a number of Constraints and Indexes.


### Constraints

`CREATE CONSTRAINT SlackID FOR (p:Person) REQUIRE p.SlackID IS UNIQUE`

### Indexes

- `CREATE INDEX ChannelName FOR (n:Channel) ON (n.name)`
- `CREATE INDEX PersonName FOR (n:Person) ON (n.name)`



## Importing the data from Slack

As the API calls into Slack require an authorization header to be provided, all the dataloads are completed with the APOC procedure [`apoc.load.jsonParams`](https://neo4j.com/labs/apoc/4.2/overview/apoc.load/apoc.load.jsonParams/), which allows for this information to be passed correctly to the API.


### List of channels

First we collect all the details of the **public** channels within the Slack Workspace

**Note : URI and Tokens set are parameters to help with the main queries.

`:param uri => "https://slack.com/api/conversations.list?limit=500"` <br>
`:param token => "Bearer xoxp-12345567889-123455678"`

This will create this simple Graph model for the returned data.

**(Person)-[:CREATED]->(Channel)**

~~~
CALL apoc.load.jsonParams($uri,{Authorization:$token},null) yield value
UNWIND value.channels as item
MERGE (c:Channel {channelid:item.id,name:item.name,description:item.purpose.value,is_archived:item.is_archived})
MERGE (p:Person {SlackID:item.purpose.creator})
MERGE (p)-[:CREATED]->(c)
~~~


![image](images/SlackImport.png)

### Retrieving the list of members
As mentioned above, we now need to retrieve the list of members of each Slack channel.   Here we will loop against each channelId and make a series of API calls for each channel. 
 
The Slack API required is https://api.slack.com/methods/conversations.members

Again, we've set some parameters to help with building out the required API calls.

`:param cMembers => "https://slack.com/api/conversations.members?channel="` <br>
`:param cMembersEnd => "&limit=1000&pretty=1"`

~~~
MATCH (c:Channel)
WITH c.channelid as channelid,c

CALL apoc.load.jsonParams($cMembers + channelid + $cMembersEnd,{Authorization:$token},null) yield value
UNWIND value.members as item
MERGE (p:Person {SlackID:item})
WITH p,c
MERGE (p)-[:MEMBEROF]->(c)
~~~

:warning: This one can take a little while as it iterates over every channel, pulling in the list of members for each.

![image](images/SlackImport2.png)


### Member details

The final call to make is to get the name and email of each slack user

https://api.slack.com/methods/users.profile.get

New parameters


`:param cProfile => "https://slack.com/api/users.profile.get?user="` <br>
`:param cProfileEnd => "&pretty=1"`

_not sure if the `cProfileEnd` parameter is actually needed._

~~~
MATCH (p:Person)
WITH p.SlackID as SlackID, p

CALL apoc.load.jsonParams ($cProfile + SlackID + $cProfileEnd,{Authorization:$token},null) yield value
UNWIND value.profile as item
set p.email = item.email
set p.name = item.real_name
~~~

![image](images/SlackAPI2.png)<br>

**Our Slack import is now complete**

![image](images/bloom-visualisation.png)<br>
---


## Gaining value from the collected data.

Whilst importing the data and playing around with the Slack API's and Neo4j is kind of fun; how can we use this information to bring some value to a team or an individual ?

Neodash is an excellent tool to quickly create PoC's or prototyping dashboards with no real coding required; just the same Cypher query knowledge used to import the data.

Install [NeoDash](https://nielsdejong.nl/neo4j%20projects/2020/11/16/neodash.html) in your desktop environment via the [GraphApp library](https://install.graphapp.io/)


### The organisation view
A very simple dashboard, but one to just return the total number of people and the most popular channels.

Option to select based on a given country - this is where NeoDash is really powerful, as you can set one of your dashboard views to be a parameter selection for the other charts and views you wish to create.

![images](images/neodash/dash1.png)<br>

### Team view
Again using the parameter selector, I am able to show the most popular channels for a selected team.

![images](images/neodash/dash2.png)<br>

### Individual view
This is the opportunity to provide some personal recommendations and not just recommendations based on the team, department or location you reside in; but recommendations based on membership of the groups you belong too. 

![images](images/neodash/dash3.png)<br>













