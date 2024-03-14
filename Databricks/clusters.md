- A cluster is a collection of Virtual Machine instances on which you can run Data Engineering, Data Science and Data Analytics workloads such as production ETL Pipelines, Streaming Analytics, Ad Hoc Analytics and Machine Learning.
- Clusters allow you to treat a group of computers or worker nodes as a single computer orchestrated by a Driver node
- [[Databricks]] provide two main type of clusters
	- **All-Purpose** - Clusters for interactive development
		- Analyze data collaboratively using interactive notebooks.
		- you can create an **All-purpose cluster** from a workspace or programmatically using CLI or REST API. 
		- you can manually terminate and restart an **All-purpose** cluster.
		- multiple users can share an **All-purpose** cluster to do collaborative and innovative analysis
	- **Job** - clusters for automating workloads
		- The [[Databricks]] job Scheduler creates job clusters when running jobs
		- you cannot restart a job cluster
		- There are two types of cluster nodes
			- Single node
				- needs only 1 VM instance running driver and there are no worker instances in this configuration
				- Low-cost single-instance cluster catering to single-node machine learning workloads and lightweight exploratory analysis
			- Standard (Multi Node)
				- Default mode for workloads developed in any supported language (requires at least two VM instances) (1 Driver and 1 Worker )

### [[Databricks]] Runtime Version
 **Standard**
 - Apache Spark and many other components and updates to provide an optimized big data analytics experiences
 
**Photon**
- An optional add-on to optimize Spark queries (e.g. SQL, DataFrame)

 **Machine learning**
 - Adds popular machine learning libraries like TensorFlow, Keras, PyTorch, and XGBoost.

### Cluster Policies

Cluster policies can help to achieve the following:
- Standardize cluster configurations
- Provide predefined configurations targeting specific use cases
- Simplify the user experience
- Prevent excessive use and control cost
- Enforce correct tagging
Only Admin users can Create, Edit and Delete a cluster policies and also have access to all policies

### Cluster pools






