
#### Data Lake (not Delta) (OLD)
- Traditional Data Lakes Often store data in its raw format, leading to potential inconsistencies and challenges in data quality.
-  Lacking strong data governance and [[ACID]] transactions, making data lineage and data integrity difficult to maintain.

### Delta Lake
So to address this issues they developed Delta Lake

- Delta Lake is an open-source project that enables building a data [[Lakehouse]] on top of existing cloud storage
- Delta Lake is the default format for tables created in Databricks
- Addresses these limitations by providing an open-source storage layer on top of data lakes.
- Leverages Apache Parquet for efficient data storage and retrieval.
- Introduces **[[ACID]]** transactions to ensure data consistency and reliability during writes and updates.
	- **Atomicity** - means all transactions either succeed or fail completely
	- **Consistency** - guarantees relate to how a given state of the data is observed by simultaneous operations
	- **Isolation** - refers to how simultaneous operations conflict with one another. The isolation guarantees that Delta Lake provides do differ from other systems
	- **Durability** - means that committed changes are permanent

- Maintains a transaction log, enabling features like:
    - **Time travel**: Accessing older versions of data for audits, rollbacks, or reproducing past states.
	    - This feature is useful for auditing, debugging, and recovering from errors in data pipelines.
	
    - **Efficient updates**: Modifying specific data subsets without rewriting entire datasets.

#### Benefits of using Delta Lake:

- **Improved data reliability and quality:** Ensures data consistency and prevents data corruption through ACID transactions.
- **Simplified data management:** Streamlines updates and data lineage tracking through features like time travel and schema enforcement.
- **Faster data processing:** Leverages Parquet format for efficient data access and query performance.
- **Unified platform:** Integrates seamlessly with other Databricks services like Spark and Structured Streaming for a cohesive data processing environment.