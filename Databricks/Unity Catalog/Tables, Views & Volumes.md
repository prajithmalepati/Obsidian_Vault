### Tables

A table resides in the third layer of Unity Catalog’s three-level namespace. It contains rows of data. To create a table, users must have `CREATE` and `USE SCHEMA` permissions on the schema, and they must have the `USE CATALOG` permission on its parent catalog. To query a table, users must have the `SELECT` permission on the table, the `USE SCHEMA` permission on its parent schema, and the `USE CATALOG` permission on its parent catalog.

A table can be _managed_ or _external_.

#### Managed tables

Managed tables are the default way to create tables in Unity Catalog. Unity Catalog manages the lifecycle and file layout for these tables. You should not use tools outside of Databricks to manipulate files in these tables directly. Managed tables always use the [Delta](https://docs.databricks.com/en/delta/index.html) table format.

For workspaces that were enabled for Unity Catalog manually, managed tables are stored in the root storage location that you configure when you create a metastore. You can optionally specify managed table storage locations at the catalog or schema levels, overriding the root storage location.

For workspaces that were enabled for Unity Catalog automatically, the metastore root storage location is optional, and managed tables are typically stored at the catalog or schema levels.

When a managed table is dropped, its underlying data is deleted from your cloud tenant within 30 days.

#### External tables

External tables are tables whose data lifecycle and file layout are not managed by Unity Catalog. Use external tables to register large amounts of existing data in Unity Catalog, or if you require direct access to the data using tools outside of Databricks clusters or Databricks SQL warehouses.

When you drop an external table, Unity Catalog does not delete the underlying data. You can manage privileges on external tables and use them in queries in the same way as managed tables.

External tables can use the following file formats:

- DELTA
- CSV
- JSON
- AVRO
- PARQUET
- ORC
- TEXT
### Views

A view is a read-only object created from one or more tables and views in a metastore. It resides in the third layer of Unity Catalog’s three-level namespace. A view can be created from tables and other views in multiple schemas and catalogs. You can create [dynamic views](https://docs.databricks.com/en/data-governance/unity-catalog/index.html#) to enable row- and column-level permissions.
### Volumes (preview)

This feature is in [Public Preview](https://docs.databricks.com/en/release-notes/release-types.html).

A volume resides in the third layer of Unity Catalog’s three-level namespace. Volumes are siblings to tables, views, and other objects organized under a schema in Unity Catalog.

Volumes contain directories and files for data stored in any format. Volumes provide non-tabular access to data, meaning that files in volumes cannot be registered as tables.

- To create a volume, users must have `CREATE VOLUME` and `USE SCHEMA` permissions on the schema, and they must have the `USE CATALOG` permission on its parent catalog.
- To read files and directories stored inside a volume, users must have the `READ VOLUME` permission, the `USE SCHEMA` permission on its parent schema, and the `USE CATALOG` permission on its parent catalog.
- To add, remove, or modify files and directories stored inside a volume, users must have `WRITE VOLUME` permission, the `USE SCHEMA` permission on its parent schema, and the `USE CATALOG` permission on its parent catalog.

A volume can be _managed_ or _external_.

> When you define a volume, you can no longer access any paths that overlap the volume location using external locations in Catalog Explorer or cloud URIs.

#### Managed volumes

Managed volumes are a convenient solution when you want to provision a governed location for working with non-tabular files.

Managed volumes store files in the Unity Catalog default storage location for the schema in which they’re contained. For workspaces that were enabled for Unity Catalog manually, managed volumes are stored in the root storage location that you configure when you create a metastore. You can optionally specify managed volume storage locations at the catalog or schema levels, overriding the root storage location. For workspaces that were enabled for Unity Catalog automatically, the metastore root storage location is optional, and managed volumes are typically stored at the catalog or schema levels.

The following precedence governs which location is used for a managed volume:

- Schema location
- Catalog location
- Unity Catalog metastore root storage location

When you delete a managed volume, the files stored in this volume are also deleted from your cloud tenant within 30 days.
#### External volumes

An external volume is registered to a Unity Catalog external location and provides access to existing files in cloud storage without requiring data migration. Users must have the `CREATE EXTERNAL VOLUME` permission on the external location to create an external volume.

External volumes support scenarios where files are produced by other systems and staged for access from within Databricks using object storage or where tools outside Databricks require direct file access.

Unity Catalog does not manage the lifecycle and layout of the files in external volumes. When you drop an external volume, Unity Catalog does not delete the underlying data.
