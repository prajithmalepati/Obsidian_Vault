

### **Atomicity** 
- means all transactions either succeed or fail completely.
- In Delta Lake, write operations (inserts, updates, and deletes) are atomic, meaning they either succeed completely or fail completely.
- If a write operation fails, Delta Lake ensures that the data remains unchanged, and the partially written data is not visible to readers.
- This guarantees that the data lake is always in a consistent state, even in the presence of failures or interruptions.
- Delta Lake achieves this by using a `transaction log`(Delta log) that records every change.
### **Consistency** 
-  guarantees relate to how a given state of the data is observed by simultaneous operations
- Delta Lake maintains data consistency by enforcing schema validation and evolution.
- It ensures that all data written to the Delta table adheres to the defined schema, preventing data inconsistencies.
- Delta Lake supports schema evolution, allowing users to make changes to the table schema over time while maintaining data consistency.
- It also provides support for constraints, such as NOT NULL and CHECK constraints, to enforce data integrity rules.
- It achieves this by maintaining metadata safely in the transactional log.
### **Isolation** 
- Isolation means that concurrently executing transactions do not affect each other.
- refers to how simultaneous operations conflict with one another. The isolation guarantees that Delta Lake provides do differ from other systems
- Delta Lake provides snapshot isolation for read operations, ensuring that readers always see a consistent snapshot of the data.
- Readers can access a specific version of the Delta table using the time travel feature, allowing them to query historical data as of a specific timestamp or version.
- Concurrent write operations are serialized, meaning they are applied one after another, preventing conflicts and maintaining isolation between writers.
### **Durability** 
- means that committed changes are permanent 
- Durability ensures that once a transaction is committed, it will persist and not be undone, even in the event of a system failure.
- Delta Lake achieves durability by storing the transaction log and the actual data in a reliable, fault-tolerant storage system like HDFS, S3, or Azure Blob Storage.
- The transaction log is written to disk before a transaction is marked as committed, ensuring that the changes are durable.

#### Problems solved by ACID
- Hard to append data
	- appends will not fail due to conflicts even when writing from many sources simultaneously
- Modification of existing data difficult
- Jobs failing mid way
	- changes are not committed until a job is completed. jobs will either fail or succeed completely
- Real-time operations hard
- Costly to keep historical data versions
	- with **Time travel**: Accessing older versions of data for audits, rollbacks, or reproducing past states.
	    - This feature is useful for auditing, debugging, and recovering from errors in data pipelines.