- A metastore is the top-level container of objects in Unity Catalog. It registers metadata about data and AI assets and the permissions that govern access to them. Databricks account admins should create one metastore for each region in which they operate and assign them to Databricks workspaces in the same region. For a workspace to use Unity Catalog, it must have a Unity Catalog metastore attached.

- A metastore can optionally be configured with a managed storage location in an S3 bucket or Cloudflare R2 bucket in your own cloud storage account.



>This metastore is distinct from the Hive metastore included in Databricks workspaces that have not been enabled for Unity Catalog. If your workspace includes a legacy Hive metastore, the data in that metastore will still be available alongside data defined in Unity Catalog, in a catalog named `hive_metastore`. Note that the `hive_metastore` catalog is not managed by Unity Catalog and does not benefit from the same feature set as catalogs defined in Unity Catalog.

- Metastore has two elements
	- Control plane
	- Cloud Storage