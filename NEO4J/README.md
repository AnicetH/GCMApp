# Neo4j is the leader of graphdatabase
For a developer's use, you can download the desktop Enterprise version for free or the community version on Neo4j's website.
It powerful to make a recommendation engine and network analysis with cypher and graph algorythms.
First of all, we will build a model and then import only data which feet our data model in the graph database.
Extracted and transformed data from Bigquery are stored on Cloud Storage (Google Cloud Platform).

# Data model
The datas is about Github and users events.
The users can make events with repositories. We will choose only 8 events (CreateEvent, WatchEvent, IssuesEvent, IssueCommentEvent, PullRequestEvent, ForkEvent, PullRequestReviewCommentEvent, CommitCommentEvent). 
So we will create 8 relationships between users and repositories.

For the recommendation engine, we need to know the skills of the users and they link to repositories.
We also need to know for our analysis who owns the repositories and their License.

Taking into account all this, we can build a data model as follow --> https://github.com/AnicetH/GCMApp/blob/master/NEO4J/Data%20Model.png
