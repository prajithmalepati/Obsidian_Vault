
### Models

A model resides in the third layer of Unity Catalog’s three-level namespace. In this context, “model” refers to a machine learning model that is registered in the [MLflow Model Registry](https://docs.databricks.com/en/machine-learning/manage-model-lifecycle/index.html). To create a model in Unity Catalog, users must have the `CREATE MODEL` privilege for the catalog or schema. The user must also have the `USE CATALOG` privilege on the parent catalog and `USE SCHEMA` on the parent schema.