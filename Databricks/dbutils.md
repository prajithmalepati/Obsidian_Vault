
### **Databricks utilities**




1. **File System (`dbutils.fs`)**: Provides methods to manage files and directories in Databricks File System (DBFS). 
	- `dbutils.fs.ls(path)`: Lists all files and directories at the specified path.
	- `dbutils.fs.mv(sourcePath, destPath)`: Moves a file or directory from one location to another.
	- `dbutils.fs.cp(sourcePath, destPath, recurse=False)`: Copies a file or directory to a new location, optionally recursively copying directories.
	- `dbutils.fs.rm(path, recurse=False)`: Deletes a file or directory, with an option to do so recursively.
	- `dbutils.fs.mkdirs(path)`: Creates the given directory and necessary parent directories if they do not exist.
	- `dbutils.fs.put(path, contents, overwrite=False)`: Writes a string to a file at the specified path, with an option to overwrite.
	- `dbutils.fs.head(path, maxBytes)`: Returns the beginning of a file up to the specified max bytes.
2. **Notebook Utilities (`dbutils.notebook`)**: Helps in controlling and coordinating the execution of notebooks.
    
    - `dbutils.notebook.run(path, timeout_seconds, arguments)`: Runs another notebook within the current notebook, with an optional timeout and arguments.
    - `dbutils.notebook.exit(value)`: Exits the current notebook with the specified return value.
3. **Secret Management (`dbutils.secrets`)**: Provides functions to securely access secrets stored in Databricks Secret Scope.
    
    - `dbutils.secrets.get(scope, key)`: Retrieves the value of the secret with the specified key from the named scope.
    - `dbutils.secrets.list(scope)`: Lists the secrets stored in the specified scope.
4. **Widgets`dbutils.widgets`**: This command allows you to create and manage input widgets in notebooks.
	- `dbutils.widgets.text(name, defaultValue, label)`: Creates a text input widget.
	- `dbutils.widgets.dropdown(name, defaultValue, choices, label)`: Creates a dropdown widget.
	- `dbutils.widgets.combobox(name, defaultValue, choices, label)`: Creates a combobox widget, allowing for selection or text input.
	- `dbutils.widgets.multiselect(name, defaultValue, choices, label)`: Creates a widget for selecting multiple options from given choices.
	- `dbutils.widgets.get(name)`: Retrieves the value of a widget.
	- `dbutils.widgets.remove(name)`: Removes the widget with the specified name.
	- `dbutils.widgets.removeAll()`: Removes all widgets.
