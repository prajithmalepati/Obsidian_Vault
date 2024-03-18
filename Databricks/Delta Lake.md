
#### Data Lake (not Delta) (OLD)
- Traditional Data Lakes Often store data in its raw format, leading to potential inconsistencies and challenges in data quality.
-  Lacking strong data governance and [[ACID]] transactions, making data lineage and data integrity difficult to maintain.

### Delta Lake
So to address this issues they eveloped Delta Lake

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

#### Overwrite

- you can also overwrites to atomically replace all the table in a table. Usually doing it this way is much faster as it doesn't need to list the directory recursively or delete any files.
	- Since overwrite is also a transaction, the old table can be still be accessed using `Time travel`
	- It's an atomic transaction, so all the concurrent queries can still read the table while you are deleting the table
	- There are two kinds of overwrites. Ones using `CRAS` and `INSERT OVERWRITE`
		1. **CRAS(`CREATE OR REPLACE TABLE AS SELECT`):** 
			- Pretty self explanatory
		1. **`INSERT OVERWRITE`:**
			- can only overwrite an existing table, not create a new table like our `CRAS`.
			- can only overwrite with new records that match our current table schema.
			- can overwrite individual partitions.

#### Append rows

- you can use `INSERT INTO` to atomically append new rows to an existing data table. This allows for incremental updates to existing tables, which is more efficient than overwriting each time.
- Note that insert into does not have any built in guarantees to prevent inserting same records multiple times. Re-executing the same command would add the same records to the target table, resulting in duplicate records
- This is where `MERGE INTO` comes into picture. `MERGE INTO` upserts from a source table, view or a DataFrame into a Delta table using the `MERGE` SQL operation.
- `MERGE INTO` updates, inserts and delete in a single transaction.
- multiple conditionals can be added in addition to the matching fields.

```SQL
MERGE INTO target a
USING source b
ON {merge_condition}
WHEN MATCHED THEN {matched_action}
WHEN NOT MATCHED THEN {not_matched_action}

-- Below, we'll only update records if the current row has a NULL email and the new row does not.

-- All unmatched records from the new batch will be inserted.

All unmatched records from the new batch will be inserted.
MERGE INTO users a
USING users_update b
ON a.user_id = b.user_id
WHEN MATCHED AND a.email IS NULL AND b.email IS NOT NULL THEN
  UPDATE SET email = b.email, updated = b.updated
WHEN NOT MATCHED THEN INSERT *
```

- When you have loads of data you want to add to the table without much Transformation, we can use something called `COPY INTO`
	- `COPY INTO` is used to load data from external storage systems into Delta tables in Databricks.
	- It provides efficient and scalable data ingestion capabilities.
	- Performance: Leverages distributed computing to parallelize data ingestion.
	- Scalability: Handles data loads of various sizes efficiently.
	- Fault Tolerance: Ensures data consistency and automatically retries on failures.
	- Incremental Loading: Supports loading only new or updated data into existing tables.
	- Schema Evolution: Adapts to changes in the source data schema.
	- Data Validation: Allows specifying validation rules to ensure data integrity.
	-  `COPY INTO` is idempotent.
```SQL
COPY INTO delta.`/path/to/delta/table`
FROM '<source_path>'
FILEFORMAT = <file_format>
[PATTERN = '<file_pattern>'] -- optional
[COPY_OPTIONS (<options>)]   -- optional
```
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
- We can use `DRY RUN` to print out all the records that would be deleted.

```SQL
VACUUM STUDENTS RETAIN 240 HOURS DRY RUN
```

- To bypass the 7 day limit , we can use the following. **(Not Recommended)**

```SQL
SET spark.databricks.delta.retentionDurationCheck.enabled = false;
SET spark.databricks.delta.vacuum.logging.enabled = true;
```

### Z - Order



