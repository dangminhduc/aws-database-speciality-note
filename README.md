# aws-solution-architect-note

# Database Specialist note
## Neptune(Graph database service)
- Using 3 types of query language: Gremlin, OpenCypher, SPARQL
  - Gremlin: for creating and querying property graphs. traversal query language
  - Import using csv
```
// Creating graph traversal
  g := gremlingo.Traversal_().WithRemote(driverRemoteConnection)

  // Perform traversal
  results, err := g.V().Limit(2).ToList()
```
  - OpenCypher: Inspired by SQL, that specifies a pattern of nodes and relationships to find in your graph using a motif syntax (like ()-[]->()). An openCypher query often starts with a MATCH clause, followed by other clauses such as WHERE, WITH, and RETURN.
  Gremlin and openCypher are both property-graph query languages, and they are complementary in many ways.
  - Import using csv
```
MATCH (n:airport) RETURN n //Find all node with label
```  
  - SPARQL: query language for the Resource Description Framework (RDF), which is a graph data format designed for the web. Amazon Neptune is compatible with SPARQL 1.1
    - `String queryString = "SELECT ?s ?p ?o WHERE { ?s ?p ?o } limit 10";`
    - Import using RDF file(ntriples, nquads, rdfxml, turtle) and must use UTF-8 format

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
- Oracle Data Guard Switchover operation is available on RDS. You will need an Oracle Database Enterprise Edition (EE) license to use replicas in mounted mode, and an additional Oracle Active Data Guard license to use replicas in read-only mode

### DMS
- To determine the best target direction for your overall environment, create a multiserver assessment report.
- ORA-01555 during extracting data from the source
  - Reduce the extract query runtime by optimizing the query in the source DB.
    - Optimze the query in the source DB
  - Make sure the source is retaining the old image of data longer than the extract query runtime.
    - Increase undo retention size in the source DB
  - Using a lager replication instance if the cause of slow extract is limited I/O from a replication instance.
  - Consider source-filter options and depending on the size of the table, you can run the tasks using different filters from different replication instances. This distributes the loading with more computational power, making extract faster.
  - Create an active data guard (read-only physical standby) and take it out of replication. Perfom data extraction on the new standby, take note of the starting timestamp so it can later be used to start the change data capture (CDC) from primary database.
  - DMS validation limitation
    - Data validation requires that the table has a primary key or unique index.
      - Primary key columns can't be of type CLOB, BLOB, or BYTE.
      - For primary key columns of type VARCHAR or CHAR, the length must be less than 1024.
      - An Oracle key created with the NOVALIDATE clause is not considered a primary key or unique index.
      - For an Oracle table with no primary key and only a unique key, the columns with the unique constraint must also have a NOT NULL constraint.
    - Validation of NULL PK/UK values aren't supported.
    - If the collation of the primary key column in the target PostgreSQL instance isn't set to "C", the sort order of the primary key is different compared to the sort order in Oracle. If the sort order is different between PostgreSQL and Oracle, data validation fails to validate the records.
    - Data validation generates additional queries against the source and target databases. You must ensure that both databases have enough resources to handle this additional load.
    - Data validation isn't supported when consolidating several databases into one.
    - For a source or target Oracle endpoint, AWS DMS uses DBMS_CRYPTO to validate LOBs. If your Oracle endpoint uses LOBs, then you must grant the execute permission on dbms_crypto to the user account used to access the Oracle endpoint.
    - If the target database is modified outside of AWS DMS during validation, then discrepancies might not be reported accurately. This result can occur if one of your applications writes data to the target table, while AWS DMS is performing validation on that same table.
    - If one or more rows are being continuously modified during validation, then AWS DMS can't validate those rows.
    - If AWS DMS detects more than 10,000 failed or suspended records, it stops the validation. Before you proceed further, resolve any underlying problems with the data.
    - AWS DMS doesn't support data validation of views.
    - AWS DMS doesn't support data validation when character substitution task settings are used.
    - AWS DMS doesn't support validating the Oracle LONG type.
    - AWS DMS doesn't support validating the Oracle Spatial type during heterogeneous migration. 

### QLDB
- Amazon Quantum Ledger Database (Amazon QLDB) is a fully managed ledger database that provides a transparent, immutable, and cryptographically verifiable transaction log.
- Serverless
- Data only can be appended, can not be edited or deleted

### Other
- Trusted Advisor is automatically refresh report once a week in Business and Enterpricse Support Plan only
