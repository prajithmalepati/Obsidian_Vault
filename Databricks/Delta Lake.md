
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
		- Ensures that all changes in a transaction are recorded, or none are. If a transaction fails at any point, all changes made during the transaction are rolled back, and the data remains unaltered. This property is crucial for maintaining data integrity during operations
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


### Load Data into Delta tables

- we can either set up a delta table or  load data into a delta table using **CTAS (`CREATE TABLE AS SELECT`)** or **CRAS (`CREATE OR REPLACE TABLE AS SELECT`)**.
- By default, **managed** tables in a schema without the location specified will be created in the **`dbfs:/user/hive/warehouse/<schema_name>.db/`** directory.
- If you drop a managed table, The table's `directory` and its `log` and data files are deleted. Only the `schema (database)` directory remains.

### Version, optimize and zorder

- use `DESCRIBE HISTORY` to see all the versions available
- `OPTIMIZE` will create a new version.
- The time travel queries can be performed by either specifying the integer version or a timestamp.

```SQL
SELECT *
FROM STUDENTS VERSION AS OF 3 
```

> **Something to keep in Mind**                                                                                                With time travel we are not recreating a previous state of the table by undoing transactions against out current version; rather we're just querying all those data files that were indicated as valid as of the specified version.

- you can also `RESTORE` a table if you accidentally deleted it. 

```SQL
RESTORE TABLE STUDENTS TO VERSION AS OF 8
```

- `RESTORE` is considered a transaction and can be seen in the `DESCRIBE HISTORY`.

- So keeping all the data files for all the versions would be a very expensive task. So we can manually purge the old files that we don't need using `VACUUM`
- By default `VACUUM` will prevent you from deleting files less that 7 days old.
- we can still bypass this prevention but its not recommended.

```SQL
VACUUM STUDENTS RETAIN 240 HOURS
-- We are deleting everything that is older than 10 days
```

