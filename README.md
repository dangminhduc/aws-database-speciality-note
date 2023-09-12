# aws-solution-architect-note

# Database Specialist note
## Neptune(Graph database service)
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

## Redshift Spectrum
- Just like Athena that can query directly from S3, but a cluster must be provisioned to ensure performance while Athena is rely on AWS free resources.

### Dynamo DB
- Point In Time Recovery is able to restore data accross regions
- Amazon Kinesis Data Firehose can convert the format of your input data from JSON to Apache Parquet or Apache ORC before storing the data in Amazon S3
- Provisioned mode:
  - One read capacity unit(RCU) represents **1 strongly consistent read** per second, or **2 eventually consistent reads** per second, for an item up to 4 KB in size.
  - One write capacity unit represents **1 write per second** for an item up to 1 KB in size.
- `TransactWriteItems` is a synchronous and idempotent write operation that groups up to 100 write actions in a single all-or-nothing operation. These actions can target up to 100 distinct items in one or more DynamoDB tables within the same AWS account and in the same Region. The aggregate size of the items in the transaction cannot exceed 4 MB.
- TransactGetItems is a synchronous read operation that groups up to 100 Get actions together. These actions can target up to 100 distinct items in one or more DynamoDB tables within the same AWS account and Region. The aggregate size of the items in the transaction can't exceed 4 MB. 
- Query operation is require partition key name and value specified. Scan operation will read all items in a table.
- Works with Gateway Endpoint service same as S3. Other service(such as SSM are using normal Private Link)

### RDS 
- To make a native backup of RDS Microsoft SQL Server (.bak file), we need `SQLSERVER_BACKUP_RESTORE` option added to an option group on your DB instance.
- RDS Aurora MySQL does not support MyISAM storage engine
- Point in Time Recovery and Automated Backup feature only supported on InnoDB(MySQL) or XtraDB(MariaDB) storage engine
- Oracle Data Guard Switchover operation is available . You will need an Oracle Database Enterprise Edition (EE) license to use replicas in mounted mode, and an additional Oracle Active Data Guard license to use replicas in read-only mode
