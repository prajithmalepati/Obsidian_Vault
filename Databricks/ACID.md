

 **Atomicity** - means all transactions either succeed or fail completely
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