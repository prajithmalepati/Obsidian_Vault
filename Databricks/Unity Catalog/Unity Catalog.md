
- Unity Catalog is a **unified governance solution** designed for data and AI assets on the Databricks [[Lakehouse]].
- It provides centralized access control, auditing, lineage, and data discovery capabilities across various **Databricks workspaces**.

#### Overview

- **Centralized Access Control:** Define and manage user permissions and access privileges for data objects (catalogs, databases, tables, views) in one place, simplifying administration across multiple workspaces.
- **Standards-compliant security model**: Utilizes familiar ANSI SQL syntax for granting permissions, making it easy for administrators to leverage existing knowledge and tools for data security management.
- **Built-in auditing and lineage**: Automatically tracks the lineage of data across various workloads (SQL, R, Python, Scala), enabling you to understand the origin, dependencies, and transformations applied to data throughout its lifecycle. This lineage information can be retrieved via a REST API for integration with other data governance tools.
- **Data Discovery:** Facilitates easier discovery of data assets across workspaces through a unified catalog, allowing users to find relevant data for their needs efficiently.
- **System tables:** Unity Catalog lets you easily access and query your account’s operational data, including audit logs, billable usage, and lineage.
- Secure data sharing with [[Delta Sharing]]

#### How Unity Catalog Governs Access:

- Configure all access to **cloud object storage** using Unity Catalog.
- **Storage credentials** encapsulate a long-term cloud credential that provides access to cloud storage.
- **External locations** contain a reference to a storage credential and a cloud storage path.
- **Managed storage locations** associate a storage location in an Azure Data Lake Storage Gen2 container or Cloudflare R2 bucket in your own cloud storage account with a metastore, catalog, or schema.
- **Volumes** provide access to non-tabular data stored in cloud object storage
- **Tables** provide access to tabular data stored in cloud object storage.

## The Unity Catalog object model

In Unity Catalog, the hierarchy of primary data objects flows from metastore to table or volume:
- **[[Metastores]]**: The top-level container for metadata. Each metastore exposes a three-level namespace (`catalog`.`schema`.`table`) that organizes your data.
- **[[Catalog]]**: The first layer of the object hierarchy, used to organize your data assets.
- **[[Git_vault/Databricks/Unity Catalog/Schema|Schema]]**: Also known as databases, schemas are the second layer of the object hierarchy and contain tables and views.
- **[[Tables, Views & Volumes]]**: At the lowest level in the data object hierarchy are tables, views, and volumes. Volumes provide governance for non-tabular data.
- **[[Models]]**: Although they are not, strictly speaking, data assets, registered models can also be managed in Unity Catalog and reside at the lowest level in the object hierarchy.
- Official Databricks page on **Unity Catalog** [here](https://docs.databricks.com/en/data-governance/unity-catalog/index.html).

![[Unity Catalog object model.png]]

- This is a simplified view of securable Unity Catalog objects. For more details, see [Securable objects in Unity Catalog](https://learn.microsoft.com/en-us/azure/databricks/data-governance/unity-catalog/manage-privileges/privileges#securable-objects).
- You reference all data in Unity Catalog using a three-level namespace: `catalog.schema.asset`, where `asset` can be a table, view, volume, or model.