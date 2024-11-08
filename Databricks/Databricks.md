-  Databricks is a unified, open analytics platform for building, deploying, sharing, and maintaining enterprise-grade data, analytics, and AI solutions at scale. The Databricks Data Intelligence Platform integrates with cloud storage and security in your cloud account, and manages and deploys cloud infrastructure on your behalf.
- Databricks was founded in 2013 by the original creators of the **Apache spark**, **Delta lake** and **mlflow**
- Databricks offers a powerful suite of tools specifically geared towards data engineers, allowing them to build, manage, and deploy complex data pipelines and solutions.

![[Pasted image 20240306102724.png]]

### Data Ingestion and Transformation:

- [[Git_vault/Databricks/Delta Lake]]: An open-source storage format providing reliability through [[ACID]] transactions and efficient data management. [[Git_vault/Databricks/Delta Lake]] tables can serve as both raw and curated data sources for your pipelines.
- **Structured Streaming**: Enables real-time data processing and stream processing capabilities, allowing you to work with continuously arriving data streams.
- **ETL Pipelines**: Databricks provides tools like Delta Live Tables to simplify building and managing ETL pipelines for batch and streaming data. These pipelines can be declaratively defined, offering better code maintainability and facilitating error handling.

### Data [[Lakehouse]] Architecture:

- **Unity Catalog**: Enables a unified data governance approach by providing consistent metadata management and access control across your data lake (Delta Lake) and data warehouse (if integrated).
- **Openness and Flexibility**: Databricks supports various data sources and allows integration with existing data infrastructure, offering flexibility in designing your data [[Lakehouse]] architecture.

### [[clusters]]
- Databricks need [[clusters]] to run

### [[Data Storage]]


### [[Unity Catalog]]





- Databricks has two main task orchestration services
	- [[Workflows]] Jobs (Workflows)
		- workflows for every job
		- workflows is the service for orchestrating all types of tasks. In other words it can orchestrate any combination of Notebooks, SQL, Spark, ML Model and DLT(Delta Live Tables)
	- [[Delta Live Tables (DLT)]]
		- Automated data pipelines for Delta Lake
		- DLT are designed for data teams to easily create batch and streaming data pipelines using python or SQL 

>DLT pipeline can be a task in a workflow



### Data Objects in the lakehouse

![[Dataobjects.png]]



- **Managed Table:** A Managed table is based on a file that is stored in Managed store's location that is configured to Metastore. So when you drop a table, it will delete the table and its underlying data
- **External table:** An External table is a table whose datafile is stored in a cloud storage location, outside of the managed storage location. So only the table meta data is managed. When you drop a table, only the table gets deleted and all the underlying data remains.


### [[Git_vault/Databricks/dbutils]]

