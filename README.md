# aws-solution-architect-note

# Database Specialist note
## Neptune
- Using 3 types of query language: Gremlin, OpenCypher, SPARQL
  - Gremlin: for creating and querying property graphs. traversal query language
```
// Creating graph traversal
  g := gremlingo.Traversal_().WithRemote(driverRemoteConnection)

  // Perform traversal
  results, err := g.V().Limit(2).ToList()
```
  - OpenCypher: Inspired by SQL, that specifies a pattern of nodes and relationships to find in your graph using a motif syntax (like ()-[]->()). An openCypher query often starts with a MATCH clause, followed by other clauses such as WHERE, WITH, and RETURN.
  Gremlin and openCypher are both property-graph query languages, and they are complementary in many ways.
```
MATCH (n:airport) RETURN n //Find all node with label
```  
  - SPARQL: query language for the Resource Description Framework (RDF), which is a graph data format designed for the web. Amazon Neptune is compatible with SPARQL 1.1
    - `String queryString = "SELECT ?s ?p ?o WHERE { ?s ?p ?o } limit 10";`
