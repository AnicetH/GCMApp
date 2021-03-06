# 2 ways to handle recommendation with Neo4J:
 
# Content-based filtering:
 
 // Find similar repositories to "symphony" by common language 
 
MATCH (r:Repo)<-[:USED_FOR]-(l:Lang)-[:USED_FOR]->(rec:Repo)

WHERE r.repo_name = "symphony"

WITH rec as Recommend, COLLECT(l.language) AS Language, COUNT(*) AS NberCommonLang

RETURN Recommend.repo_name, Language, NberCommonLang
ORDER BY NberCommonLang DESC LIMIT 10;

***************************


// Personalized Content recommendation by overlapping Language

MATCH (u:User {actor_name: "Sanchez Eric"})-[f:FORK]->(r:Repo),
  (r)<-[:USED_FOR]-(l:Lang)-[:USED_FOR]->(rec:Repo)
  
WHERE NOT EXISTS( (u)-[:FORK]->(rec) )

WITH rec, [l.language, COUNT(*)] AS scores, 

RETURN rec.repo_name AS recommendation, COLLECT(scores) AS scoreComponents,
REDUCE (s=0,x in COLLECT(scores) | s+x[1]) AS score

ORDER BY score DESC LIMIT 10

****************************


// Find similar repositories by common Language with weight

MATCH (r:Repo) WHERE r.repo_name = "symphony"

MATCH (r)<-[:USED_FOR]-(l:Lang)-[:USED_FOR]->(rec:Repo)

WITH r, rec, COUNT(*) AS langs

OPTIONAL MATCH (r)<-[:WATCH]-(w:User)-[:WATCH]->(rec)
WITH r, rec, langs, COUNT(w) AS watchers

OPTIONAL MATCH (r)<-[:FORK]-(f:User)-[:FORK]->(rec)
WITH r, rec, langs, watchers, COUNT(f) AS forkers

RETURN rec.repo_name AS recommendation, (5*langs)+(3*watchers)+(4*forkers) AS score ORDER BY score DESC LIMIT 5

*****************************

// Find similar repo to "symphony" using jaccard index 

MATCH (r:Repo {repo_name: "symphony"})<-[:USED_FOR]-(l:Lang)-[:USED_FOR]->(other:Repo)

WITH r, other, COUNT(l) AS intersection, COLLECT(l.language) AS i

MATCH (r)<-[:USED_FOR]-(rl:Lang)

WITH r,other, intersection,i, COLLECT(rl.language) AS s1

MATCH (other)<-[:USED_FOR]-(ol:Lang)

WITH r,other,intersection,i, s1, COLLECT(ol.language) AS s2

WITH r,other,intersection,s1,s2

WITH r,other,intersection,s1+filter(x IN s2 WHERE NOT x IN s1) AS union, s1, s2

RETURN r.repo_name, other.repo_name, s1,s2,((1.0*intersection)/SIZE(union)) AS jaccard ORDER BY jaccard DESC LIMIT 10

******************************


# Collaborative filtering:

// Simple collaborative filtering

MATCH (u:User {actor_name: "Sanchez Eric"})-[:FORK]->(r:Repo)<-[:FORK]-(o:User)

MATCH (o)-[:FORK]->(rec:Repo)

WHERE NOT EXISTS( (u)-[:FORK]->(rec) )

RETURN rec.repo_name
LIMIT 25

*****************************

