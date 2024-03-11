

 **Atomicity** - means all transactions either succeed or fail completely.
- In Delta Lake, write operations (inserts, updates, and deletes) are atomic, meaning they either succeed completely or fail completely.
- If a write operation fails, Delta Lake ensures that the data remains unchanged, and the partially written data is not visible to readers.
- This guarantees that the data lake is always in a consistent state, even in the presence of failures or interruptions.
- Delta Lake achieves this by using a `transaction log` that records every change.
 **Consistency** - guarantees relate to how a given state of the data is observed by simultaneous operations
 **Isolation** - refers to how simultaneous operations conflict with one another. The isolation guarantees that Delta Lake provides do differ from other systems
 **Durability** - means that committed changes are permanent 

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