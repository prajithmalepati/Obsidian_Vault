
PySpark is an interface for Apache Spark in Python. With PySpark, you can write Python and SQL-like commands to manipulate and analyze data in a distributed processing environment.

PySpark is a popular choice for big data processing because it is easy to use and it is scalable.


[[Creating a DataFrame]]


## Ezz Notes

## Reading Files into a DataFrame
### CSV Format 
```python 
df = spark.read.format("csv").option("header", True).load("file_path")
df = spark.read.csv("file_path", header=True)
df = spark.read.format('csv').schema(schema).load('file_path')
```

- To load multiple files, provide either a list of all file paths or the path to the folder containing the files.
- Use `help` to explore available options for `.csv` or `.json`.

### JSON Format

```python
df = spark.read.format("json").option("multiline", True).load("file_path")
df = spark.read.json("file_path", multiline=True)
df = spark.read.format('json').schema(schema).load('file_path')
```


- For JSON files with multiline content, set the `multiline` option to `True`. By default, it's `False`.

### XML Format

```python
df = spark.read.format('xml').option('rowTag',"MRData").load('file_path')
df = spark.read.xml("file_path", rowTag='MRData')
```

-  For XML files, you need something called a `rowTag` which is basically the name of the first tag that contains everything in the file. **Something to keep** in mind is that whatever the name you give to `rowTag` will not be a column name

### Writing a DataFrame into a File


```python
df.write.format("csv").option("header", True).mode('ignore').save("file_path") 
df.write.csv("file_path", header=True, mode='ignore')
```

- Writing modes: "error", "append", "ignore", and "overwrite".
    - "error": Raises an error if the directory already exists (default).
    - "ignore": Silently ignores the operation if data already exists.
    - "overwrite": Replaces existing data.
    - "append": Adds to existing data.
- Note that `write` creates a folder instead of a file.
- reading and writing of parquet is similar to these above

### To show or Display

```python
df.show()
df.show(truncate=False)
display(df)
```

* By default truncate is true. so it only shows 20 rows and columns and truncates the extra ones
*  `df.show()` is preferred as it's a DataFrame method in PySpark, presenting data in a tabular view, with truncation by default to 20 rows and columns.
* `df.show()` is preferred as it's a DataFrame method in PySpark, presenting data in a tabular view, with truncation by default to 20 rows and columns.
* `df.show()` is commonly employed in general PySpark coding environments, while `display(df)` is specific to Databricks or similar platforms.

## To remove a file

```python
dbutils.fs.rm("dbfs:/FileStore/tables/heart.csv") 
```


## Importing

* It's advisable to import necessary modules before executing any significant operations.

``` python
from pyspark.sql.types import *
from pyspark.sql import functions as F
```

## Change or edit a column or its type


### to change the type of the column

```python
# Cast column 'salary' to Integer type
df1 = df.withColumn('salary',F.col('salary').cast('Integer'))
df1.printSchema()
```

* `withColumn` returns a new DataFrame without modifying the original.

```python
# Multiply 'salary' column by 2
df1 = df1.withColumn('salary',F.col('salary')*2)
```

* the first param in the withColumn is the name of the column. it changes that column if it already exists or create a new column altogether if it doesn't
* the second param in takes col type.
* so you can either import the `col` function or write in this manner `df.column_name`

```python
df1 = df1.withColumn('salary',df.salary*2)
```

```python
# Add a new column 'country' with a constant value 'India'
df1 = df1.withColumn('country',F.lit('India'))
```

* Use `F.lit()` to add a constant value to a new column.
* Column operations like addition, multiplication, etc., are performed with `F.col()`. Ensure that the column names are passed correctly to avoid errors.
* It's essential to understand that Spark DataFrames are immutable; therefore, operations like `withColumn` return new DataFrames rather than modifying the existing one.
* you can chain multiple `withColumn()` functions to add multiple new columns in a single statement

```python
df = df.withColumn("country",F.lit("India")).withColumn("City",F.lit("Nellore"))
```

### To rename a column

```python
df1 = df1.withColumnRenamed('country','Country of Birth')

# you can change multiple withColumnRenamed together
```

### Concatenate columns

```python
df = auto_df.withColumn(
    "concatenated", concat(col("name"), lit("_"), col("gender")))
```

### drop or remove column

```python
df = auto_df.drop("horsepower")
```

### Get the size of a DataFrame

```python
print(f"{df.count()} rows")
print(f"{len(df.columns) } columns")

# Code snippet result:
# 398 rows
# 9 columns
```
## select()

- The `.select()` function in PySpark is used to select one or more columns from a DataFrame.
- It allows you to specify the columns you want to include in the resulting DataFrame.

```python
# we select the 'name' and 'age' columns from the DataFrame into a new df
df_new = df.select('name', 'age')

# we compute a new column by adding 1 to the 'age' column.
df_new = df.select('name', df.age + 1)

# we rename the 'name' column to 'full_name' using the `alias()` function. and load that into a new df
df_new = df.select(df.name.alias('full_name'), 'age')
```

- The `.select()` function creates a new DataFrame with only the specified columns, leaving the original DataFrame unchanged.
- It is often used in conjunction with other DataFrame operations such as `.filter()`, `.groupBy()`, `.orderBy()`, etc., to perform complex transformations and data manipulations.
## Schema

- The schema determines the structure of a DataFrame, including column names and their corresponding data types.
- you can use `printSchema()` to print the schema

```python
df.printSchema()
```

- It provides a blueprint for organizing and interpreting the data within the DataFrame.
- Column names are defined within the `StructField` instances, while data types are specified using classes like `IntegerType`, `StringType`, `ArrayType`, and `MapType`.
- Schemas are essential for ensuring data consistency, integrity, and accurate interpretation during data processing and analysis.
- Defining a schema explicitly can improve performance and prevent data type inference errors, especially when working with structured data sources.

```python
schema = StructType([StructField('id', IntegerType()),
					 StructField('name', StringType()),
					 StructField('gender', StringType()),
					 StructField('salary', IntegerType()),
					 StructField('skills', ArrayType(StringType())),
					 StructField('properties', MapType(StringType(), StringType()))])
```

- The `StructType` class is used to define the schema for a DataFrame. It represents a collection of `StructField` instances, each defining a column in the DataFrame.
- Inside the `StructType` constructor, a list of `StructField` instances is provided, specifying the columns and their corresponding data types.
- These types doesn't work if you don't import the pyspark.sql.type.
#### ArrayType()

- The `ArrayType` only takes one data type. so if you need multiple data types you can nest another `StructType` within it.
- Accessing values in an `ArrayType` column is similar to accessing elements in a regular array.

```python
#copying the first value in the array to a new columnn called first_skill
df2 = df.withColumn('first_skill',F.col('skills')[0])
```

#### MapType()

- `MapType` is a data type in PySpark used to represent key-value pairs.
* The `MapType` takes 2 data types, one for the `key` and another for `value`
* you can use `map_keys()` and `map_values` to extract the keys and values from a map column.
* if you want the value of a key, we can use `getItem()` function. It takes a key as an argument and returns the corresponding value associated with that key.

```python
# creating 2 new columns with specific value and key as column
df_props = df.withColumn('hair_color',F.col('properties')['hair'])
df_props = df_props.withColumn('eye_color',F.col('properties')['eyes'])
# you can also use df.properties['hair'] or df.properties['eyes]
```

* you can also achieve the same thing with this

```python
df_props = df.withColumn(F.col('properties').getItem('hair').alias('hair_color'))
df_props = df_props.withColumn(F.col('properties').getItem('eyes').alias('eye_color'))
# you can chain the both withColumn if you want.

# or this
df_exploded2 = df.select('id','name'\
						 ,'gender',\
						 'salary'\
						 ,'skills',\	F.col('properties').getItem('hair').alias('hair_color'),\
						 F.col('properties').getItem('eyes').alias('eye_color'))
```


### .dtypes (Data Types)

-  To find the Data Types of all the columns in DataFrame, we can use a method called `.dtypes`
- when used on a dataFrame, it returns a list of tuples with column name and dataType of that coumn name in a tuple.

```python
print(df.dtypes)
'''
output
[('gender', 'string'), ('id', 'bigint'), ('name', 'string'), ('properties', 'struct<eyes:string,hair:string>'), ('salary', 'bigint'), ('skills', 'array<string>')]
'''
```

- if you just want column names, we can use the `df.schema.names`

```python
colNames = df.schema.names
```
### explode()

- The `explode()` function in PySpark is used to transform an array column into multiple rows, one for each element in the array.
- It "explodes" the array elements, creating a new row for each element in the original array, while duplicating the values in other columns.

```python

# Explode the 'skills' array column into multiple rows in a new column called skill
df_exploded = df.withColumn('skill',F.explode(F.col('skills')))

```

### To flatten

- This is **different** from the method `flatten()` which is used to create a single array from an array of arrays
- In general , we **flatten** a dictionary and **explode** an array. Flattening a dictionary gives us more columns where as exploding an array gives us more rows.

```python
df_flattened = df.select('*',F.col('properties.eyes'),F.col('properties.hair'))
# in this line we flattened the dictionary properties which adds 2 columns
```
### split()

- The `split()` function is used to split a string column into an array of substrings based on a delimiter.
- It takes two parameters: the column to split and the delimiter pattern.

```python
# Create a DataFrame 
df = spark.createDataFrame([("This is a sentence",)], ["data"]) 
# Split the sentence with a limit of 2 (only keep the first two words) 
df = df.withColumn("split_data_limited", split(df.data, " ")) 
# Show the results 
df.show()
```

* This function is helpful for tasks like tokenizing text data or extracting information from structured strings.

### array()

- The `array()` function is used to create an array column from multiple input columns or literal values.
- It accepts an arbitrary number of parameters, each representing an element of the resulting array.

```python

df = df.withColumn('new_column',array(F.col('first_column'),F.col('second_column')))
# writing in a different way
df = df.withColumn('new_column',array(df.first_column,df.second_column))
```

### arrayContains()

- The `arrayContains()` function checks whether an array column contains a specified value.
- It takes two parameters: the array column to check and the value to search for.

```python
from pyspark.sql.functions import array_contains  
# Check if the 'skills' array column contains the value 'Python' 
df_with_contains = df.withColumn("contains_python", array_contains("skills", "Python"))
```

- This function is handy for filtering DataFrame rows based on the presence of a specific value in an array column.


### when() & otherwise()

- The `when()` function in PySpark is used for conditional processing similar to SQL's `CASE WHEN` statement.
- It allows you to apply conditional logic to DataFrame columns and create new columns based on specified conditions.
- The `when()` function takes two parameters: a condition and a value to return if the condition evaluates to true.
- you can chain multiple `when()` functions. These act like `elif` in python
- The `otherwise()` function is used in conjunction with `when()` to specify a default value when none of the conditions specified in `when()` are met.
- `otherwise()` is optional and can be used to provide a default value or handle unmatched conditions.

```python

# syntax
df = df.withColumn('new_column', F.when(condition1, value1).when(condition2, value2).otherwise(default_value))

### creates a new column category and assigns "poor" if salary is less than 10000 and "rich" otherwise
df_category = df.withColumn('category',\
							F.when(df.salary < 1000, 'bimbo')\
							.when(df.age < 10000, 'poor')\
							.otherwise('rich'))


lmao
```

* so when you `withColumn()` you can give the new column a name but when you use it with `select()` always follow it with an `alias()` too or else the name of the column would be the entire thing inside you `when()`

```python
# we followed the when other wise with an alias to name the column as we want
df_category = df.select('name','salary',\
						F.when(df.salary < 1000, 'bimbo')\
						.when(df.age < 10000,'poor')\
						.otherwise('rich').alias('category'))
```


### alias()

- The `alias()` function in PySpark is used to provide an alias or rename a column in a DataFrame.
- It allows you to assign a new name to a column, making it easier to reference or work with in subsequent DataFrame operations.
- You can use `alias()` when selecting columns, computing new columns, or performing transformations on DataFrames.


```python
# Renaming a column in a DataFrame 
df.select(df.column_name.alias('new_column_name'))
```
#### Example:

```python
from pyspark.sql.functions import alias  
# Renaming the 'name' column to 'full_name' 
df.select(df.name.alias('full_name'))`
```


### filter() and where()

- The `filter()` function in PySpark is used to filter rows from a DataFrame based on a given condition.
- It allows you to specify a boolean expression as the filtering condition, and rows that satisfy this condition are retained in the resulting DataFrame.
- The `where()` function in PySpark is an alias for `filter()`. It provides the same functionality as `filter()` and is used interchangeably.

```python
#syntax
new_df = df.filter(condition)

# Filter rows where age is greater than 30 
new_df = df.where(df.age > 30) 

# Filter rows where name starts with 'A' 
new_df = df.where(df.name.startswith('A'))

new_df = df.filter((df.gender == 'Male') & (df.salary < 5000))
```


### like()

- The `like()` function in PySpark is used to filter rows from a DataFrame based on a given pattern matching condition.
- It is typically used to match string values against a pattern using wildcard characters.

```python
#syntax
df.filter(df.column_name.like(pattern))

# Filter rows where name starts with 'A'
df.filter(df.name.like('A%'))

# Filter rows where name ends with 'son'
df.filter(df.name.like('%son'))

# Filter rows where name contains 'John' anywhere
df.filter(df.name.like('%John%'))
```

* The `like()` function is case **insensitive** by default

### distinct() & dropDuplicates()

- The `distinct()` function in PySpark is used to remove duplicate rows from a DataFrame and return only distinct rows.
- It returns a new DataFrame containing unique rows, removing any duplicate rows that are identical across all columns.
- The `dropDuplicates()` function in PySpark is used to remove duplicate rows from a DataFrame based on specific columns.
- It returns a new DataFrame containing only distinct rows, removing any duplicate rows that have the same values across the specified columns.
- `dropDuplicates()` takes a list of column_names to check. which is optional. if nothing is specified, default is all.

```python
df_distinct = df.distinct()

# with drop duplicates , you can specify  the column names to check the duplicates 
df_distict = df.dropDuplicates([gender])
```

### sort() and orderBy()

- The `orderBy()` function in PySpark is used to sort the rows of a DataFrame by one or more columns in ascending or descending order.
- It sorts the DataFrame based on the specified column(s) and returns a new DataFrame with the sorted rows.
- The `sort()` function in PySpark is an alias for `orderBy()`. It provides the same functionality as `orderBy()` and is used interchangeably.
- By default both of these sort in **ascending** order. we can use `.desc()` to switch to **descending** if needed

```python
sorted_df = df.sort(df.age.desc())

# here we first sort age by desc and then sort the names for that with asc
sorted_df = df.sort(df.age.desc(), df.name.asc())
```

### groupBy():

- The `groupBy()` operation is used to group the data in a DataFrame based on one or more columns.
- It divides the data into groups based on the unique combinations of values from the specified columns.
- Each unique combination of values becomes a separate group.
- `groupBy()` is typically followed by an aggregation operation using `agg()`.

```python
# we are exploding skills array into a skill column
df_exp = df.withColumn('skill',F.explode(df.skills))
# then we are grouping by skill , then got a count and changed the column name to 
df_grouped = df_exp.groupBy('skill').agg(F.count(F.col('skill')).alias('skill_count'))
# now we are sorting that said column we put our count in
df_count = df_grouped.orderBy(df_grouped.skill_count.desc())
df_count.show()
```
### agg():

- The `agg()` operation (short for "aggregate") is used to compute summary statistics or perform aggregation functions on grouped data.
- It's applied after `groupBy()` to specify how you want to summarize the grouped data.
- `agg()` takes one or more aggregation expressions, which can include functions like `count()`, `sum()`, `avg()`, `min()`, `max()`, etc.
- When using `agg()`, you specify the aggregation expressions along with the columns you want to aggregate. For example, `agg(F.count('*'))`, `agg(F.sum('column'))`, etc.
- If you use `count()` in `agg()`, it can take various arguments: `count('*')` or `count(1)` (count all rows), `count('column')` (count non-null values in a specific column), or `countDistinct('column')` (count distinct values in a specific column).
- Other aggregation functions include `sum()`, `avg()`, `min()`, `max()`, `first()`, `last()`, etc.
- you can chain multiple functions inside `agg()` to achieve what you want

### Important Functions in agg():

- `count()`: Counts the number of rows or non-null values in a column.
- `sum()`: Computes the sum of values in a column.
- `avg()`: Computes the average of values in a column.
- `min()`: Computes the minimum value in a column.
- `max()`: Computes the maximum value in a column.
- `first()`: Returns the first value in a column within each group.
- `last()`: Returns the last value in a column within each group.
### pivot():

- The `pivot()` operation is used to pivot (or rotate) the data from rows into columns.
- It's useful for reshaping the data, especially when you want to convert unique values from one column into multiple columns.
- `pivot()` requires three parameters: the pivot column (the column whose distinct values will become new columns), the values column (the column containing the values to populate the pivoted columns), and an optional aggregation function to aggregate values if there are multiple values for a single combination of pivot and value columns.
- Before pivoting, it's common to perform a `groupBy()` operation to organize the data into groups.
### Notes:

- If there are multiple values for a single combination of pivot and value columns, you need to specify an aggregation function (e.g., `sum()`, `avg()`, `first()`, `last()`, etc.) to aggregate those values.
- Be mindful of missing values. If a combination of pivot and value columns doesn't have any corresponding value, it will result in a null value in the pivoted DataFrame.

```python
# this is basically achieves the same thing as example in Maptype()
df_exploded_pivoted = df.select('id', 'name', 'gender', 'salary', 'skills', 
                                F.explode('properties').alias('key', 'value')) \
                        .groupBy('id', 'name', 'gender', 'salary', 'skills') \
                        .pivot('key') \
                        .agg(F.first('value'))
# we are selecting the columns and then exploding the properties which is a list with column names key and value and then grouping by all those columns to organize and then we pivoted the key column and then to populate it we used the first function inside the agg
```


### union() & unionAll()

- The `union()` operation is used to concatenate two DataFrames with the same schema or structure in PySpark.
- It combines the rows of both DataFrames, creating a new DataFrame containing all rows from both DataFrames, including duplicates.
- By default, `union()` does not remove duplicate rows. If duplicate removal is desired, `dropDuplicates()` or `distinct()` method can be used after the union operation.
- `unionAll()` works the exact same way as `union()`

```python
df_union = df1.union(df2)

# if you want to remove duplicates
df_union_distinct = df_union.distinct()
```

* something to keep in mind. if you try to do union on 2 df with a different column names but same dataType for that said column. then union will merge and put the df1 column name as the column name for the resulting df.
### unionByName()

- The `unionByName()` operation is used to concatenate two DataFrames with potentially different schemas in PySpark.
- It matches columns by name and concatenates the rows of the two DataFrames with the same column names.
- If a column exists in only one DataFrame, the corresponding rows in the other DataFrame will have `null` values for that column if we send `allowMissingColumns=True` as an argument.

```python
data1 = [(1,'prajith','23'),(2,'taylor','22')]
data2 = [(1,'sai','20000'),(2,'nick','24567')]
df1 = spark.createDataFrame(data1,['id','name','age'])
df2 = spark.createDataFrame(data2,['id','name','salary'])

# since we have some missing columns i put the option as true
dfu = df1.unionByName(df2,allowMissingColumns=True)

display(dfu)
```

### join()

- The `join()` operation is used to combine two DataFrames based on a common column or a set of columns.
- It merges rows from both DataFrames based on the matching values in the specified columns.
- The default join type is an `inner` join, which returns only the rows where the key exists in both DataFrames.
- You can specify different types of joins using the `how` parameter: `'inner'`, `'left'`, `'right'`, `'outer'`, `'left_semi'`, `'left_anti'`, `'cross'`.
- it takes 3 arguments. `other`, `on` and `key`. `other` is df you wanna use it on. `on` is the condition for join and `key` decides what type of join we want to do

```python
# syntax
df_inner = df1.join(df2, on="key", how="inner") df_inner.show()
```

### Types of Joins:

1. **Inner Join** (`'inner'`):
    - Returns only the rows where the key exists in both DataFrames.
    - Non-matching rows are excluded from the result.
2. **Left Join** (`'left'` or `'left_outer'`):
    - Returns all rows from the left DataFrame and matching rows from the right DataFrame.
    - Non-matching rows from the right DataFrame will have `null` values.
3. **Right Join** (`'right'` or `'right_outer'`):
    - Returns all rows from the right DataFrame and matching rows from the left DataFrame.
    - Non-matching rows from the left DataFrame will have `null` values.
4. **Outer Join** (`'outer'` or `'full'` or `'full_outer'`):
    - Returns all rows from both DataFrames, combining the results of both left and right joins.
    - Non-matching rows from both DataFrame will have `null` values.
    - This is the same as UNION in SQL
1. **Left Semi Join** (`'left_semi'`):
    - Returns only the rows from the left DataFrame where the key exists in the right DataFrame.
    - The result contains only columns from the left DataFrame.
2. **Left Anti Join** (`'left_anti'`):
    - Returns only the rows from the left DataFrame where the key does not exist in the right DataFrame.
    - The result contains only columns from the left DataFrame.
3. **Cross Join** (`'cross'`):
    - Returns the Cartesian product of both DataFrames, combining every row from the left DataFrame with every row from the right DataFrame.
    - It results in a DataFrame with a large number of rows, especially if the input DataFrames are large.

### Notes:

- Inner, left, right, and outer joins are the most commonly used types of joins.
- Left semi join and left anti join are useful for filtering based on the existence of keys in the other DataFrame.
- Cross join is rarely used due to its computational complexity and potential for generating large result sets.

### unpivot()

- Transforms data from a wide format (multiple value columns) to a long format (one value column per row).
- **Arguments:**
    - `ids` Column(s) to use as identifiers. Can be a single column or column name, or a list or tuple for multiple columns. 
	- `cols` (list): Columns to unpivot (optional, defaults to all non-identifier columns).
    - `from_col` (string): Name of the column containing original column names (required).
    - `to_col` (string): Name of the column to store unpivoted values (required).

```python
# Sample DataFrame
df = spark.createDataFrame([(1, 11, 1.1), (2, 12, 1.2)],["id", "int", "double"])
df.show()

# id is the identifier and the list is the columns we are flipping
# var is the new column for the column names and val is the column name for values 
unpivoted_df = df.unpivot("id", ["int", "double"], "var", "val")

unpivoted_df.show()
```

### fill() & fillNa()

- The `fill()` function in PySpark is used to replace null (NaN) values in a DataFrame with specified non-null values.
- It takes a dictionary where keys are column names and values are the values to replace nulls with.
- They are the same. use whichever you fancy
```python
DataFrame.fill(value, subset=None)
```

- `value`: The value to replace nulls with. This can be a single value or a dictionary where keys are column names and values are replacement values.
- `subset` (optional): A list of column names to restrict replacement to. If not specified, all columns are considered.

```python
# Replace null values in column 'age' with 0 
df.fill(0, subset=['age'])  

#replace null values with 0 in column 'age' and 'gender'
df.fill(0,['age','salary'])

# Replace null values in multiple columns with different values
df.fill({'age': 'dead', 'salary': 0})`
```


### sample()[](https://spark.apache.org/docs/3.1.2/api/python/reference/api/pyspark.sql.DataFrame.sample.html)

- The `sample()` function in PySpark is used to randomly sample data from a DataFrame.

```python
DataFrame.sample(withReplacement, fraction, seed=None)
```

* It takes 3 params. 1 and 3 are optional.
	* **withReplacement**: A boolean flag indicating whether sampling should be done with replacement (True) or without replacement (False). **(default `False`)**
	* `fraction`: The fraction of rows to sample from the DataFrame. Should be a float value between 0 and 1. 0.1 is 10%, 0.27 is 27%
		* This is not guaranteed to provide exactly the fraction specified of the total count of the given.
	- `seed` (optional): The seed for random number generation. This ensures reproducibility of the sampled data.

```python
# Sample 20% of the DataFrame without replacement 
sampled_df = df.sample(False, 0.2) 
# Sample 10% of the DataFrame with replacement and a specified seed 
sampled_df = df.sample(True, 0.1, seed=42)
```

* The `withReplacement` parameter determines whether rows can be sampled multiple times (with replacement) or only once (without replacement).
* When `withReplacement` is set to True, the size of the sampled DataFrame may exceed the original DataFrame, as rows can be duplicated.

### Row() Class

- The `Row()` class in PySpark represents a row of data in a DataFrame.
- Rows are commonly used to create DataFrames, as they provide a convenient way to represent structured data.
- these are like tuples and can be accessed like them.

```python
from pyspark.sql import Row  
# Creating a Row object with named arguments
row1 = Row(id=1, name='Alice', age=30)  
# Creating a Row object by passing values directly
row2 = Row(id =2,name='Malenia',age=57)
df = spark.createDataFrame([row1,row2])
print(row1.id)   # Output: 1 print(row2[1])   # Output: 'Alice'
```

* Since this is a class we can use it like one. that means we can do things like this

```python
Person = Row('id','name','age')
p1 = Person(1,'Alice',30)
p2 = Person(2,'Malenia',57)
# this is same as above
df = spark.createDataFrame([p1,p2])
```

* you can also do nested Row classes

```python
# the dataType of the props column would be Struct
data = [Row(id=1,name='Alice',age=30,props = Row(hair_color = 'blonde' , eye_color = "blue")),\
	   Row(id=2,name='Malenia',age=57,props = Row(hair_color = 'red' , eye_color = " darkblue"))]
df = spark.createDataFrame(data)
df.show()
```


### column class

- The `Column()` class in PySpark represents a column expression or operation in a DataFrame.
- you have to import it from pyspark.sql.functions

```python
from pyspark.sql.functions import col,lit
from pyspark.sql import functions as F  # can use them as F.col or F.lit (recommended)

df.select(df.gender).show()
df.select(df['gender']).show()
df.select(col('gender')).show() 
df.select('gender').show()
#you can use F.col if you imported as F
# all theese 4 lines above achieve the same thing
```


### collect() 
- The `collect()` function in PySpark is used to retrieve all the elements of a DataFrame or RDD and return them to the driver program as a list or array in Python. 
- It gathers all the elements distributed across different partitions and brings them back to the driver program. 
```python
# Assuming 'df' is a DataFrame 
result = df.collect() # result contains a list of Row class
# [Row('id'=1,'name' = 'blah'),Row('id'= 2,'name' = 'pogo')]
```

* After performing aggregations or transformations, `collect()` can be used to retrieve the aggregated results for further analysis or reporting.
* Avoid using collect on the large datasets to avoid potential performance issues

### transform()

- The `transform()` function in PySpark is used to apply a transformation function to each element of a DataFrame column and return the results as a new column.
- This is a `DataFrame.transform()`. there is an other one that is a part of the functions. we will get to it in a sec.

```python
DataFrame.transform(func)
```

- `func`: The transformation function to apply to each element of the DataFrame column. This function can be a built-in function, a user-defined function (UDF), or a lambda function.
- `transform()` is useful for applying complex transformations to DataFrame columns, especially when the transformation logic cannot be expressed using built-in functions alone.

```python
# Define a transformation function
def doubleTheSalary(df):
    return df.withColumn('new_salary',df.salary*2)
    
# Apply the transformation function using transform()
transformed_df = df.transform(doubleTheSalary)

# we can also chain the transforms if you have multiple functions
def toUpper(df):
	return df.withColumn('name_upper',F.upper(df.name))

#chained both transforms together
transformed_df = df.transform(doubleTheSalary).transform(toUpper)

```

### transform() from functions

* This is only used to apply a transformation on a **ArrayType**.
* This function applies the transformation on every element in the array and returns an **ArrayType**
* it needs to 2 arguments. the column with the array we want to transform and a function that applies the transformation.

```python
# this is different from the transform we saw before this
# you need to import this from functions
from pyspark.sql import functions as F

def toUpper(x):
	return F.upper(x)

df_new = df.select('id','name','salary',F.transform('skills',toUpper).alias('SKILLS'))

# you can also use a lambda function if it is a one and done kinda thing
df_new = df.select('id','name','salary',\
				   F.transform('skills',lambda x: F.upper(x)).alias('SKILLS'))
```

* always follow it with an `alias()` as this is same as creating a new column with `when` and gives you long ass name.

### createOrReplaceTempView() Method in PySpark

- The `createOrReplaceTempView()` method in PySpark is used to create or replace a temporary view from a DataFrame.
- Temporary views are temporary tables that exist only for the duration of the SparkSession.
- These views are useful for running SQL queries against DataFrame data, enabling SQL-like operations on structured data.
- The method takes two parameters: the name of the temporary view and optionally a SQL query string. If a query is provided, the view will contain the result of executing that query against the DataFrame.
- If a view with the same name already exists, it will be replaced by the new DataFrame.

```python
df.createOrReplaceTempView('my_table')
# can use SQL queries
df1 = spark.sql("SELECT id,name FROM my_table")

# can be used like this to create another tmp view from one tmp view
spark.sql(""" SELECT id, UPPER(name) AS uppercase_name FROM people_view WHERE id > 1 """).createOrReplaceTempView("filtered_people_view")
```

* To drop a temporary view, you can use the `spark.catalog.dropTempView()` method.

```python
# Drop a temporary view
spark.catalog.dropTempView("people_view")
```

### createOrReplaceGlobalTempView()

* The `createOrReplaceGlobalTempView()` method in PySpark is used to create temporary view or tables globally from a DataFrame.
* to query these tables, we need to use `global_temp.<tablename`.

```python
df.createOrReplaceGlobalTempView('my_table')
df1 = spark.sql("SELECT id,name FROM global_temp.my_table")
df1.show()
```

### spark.catalog.listTables()

* we can use this to find the temp or global views available

```python
print(spark.catalog.currentDatabase()) # results in 'default'

spark.catalog.listTables(spark.catalog.currentDatabase()) # or ('default')

# to find the global views
spark.catalog.listTables('global_temp')
```

## UDF (User Defined Functions)

- User-Defined Functions (UDFs) in PySpark enable you to define custom functions to apply transformations to DataFrame columns.
- UDFs are particularly useful when built-in functions from `pyspark.sql.functions` do not suffice for the required transformation.
- it takes 2 arguments, `function` and `return type`.

```python
# Define a function
def square(x):
    return x * x
    
# Register the UDF
square_udf = udf(square, IntegerType())

df_ids = df.withColumn('id_squared',square_udf(df["id"]))

# can achieve the same thing with lambda functions too
square_udf = udf(lambda x:x*x, IntegerType())
df_ids = df.withColumn('id_squared',square_udf(df["id"]))
```

* for something as simple as the above, i would recommend using inbuilt functions. i mean something like this

```python
df_ids = df.withColumn('id_squared',df.id * df.id)
```

* we can also register UDF as annotation

```python
@udf(returnType=IntegerType()) # so this auto takes the function below as a udf
def square(x):
    return x*x

df_ids = df.withColumn('id_squared',square(df.id))

df_ids.show()
```

* if used with `select()`, pair it with an `alias()` to name the column

## RDD

* RDD (Resilient Distributed Dataset) is the fundamental data structure of Apache Spark, representing an immutable distributed collection of objects that can be operated on in parallel. 
* RDDs are fault-tolerant, meaning if a partition of data is lost, it can be rebuilt using the transformations applied to the original RDD.
* RDDs are distributed across multiple nodes in a cluster. Each RDD is divided into multiple partitions, and these partitions are processed in parallel across the nodes in the cluster.
* RDDs are immutable, meaning their content cannot be changed once they are created. Instead, transformations applied to RDDs create new RDDs.

### convert data into an RDD

* you can convert some data into an RDD by using `parallelize()`, which is a method of `sparkContext` .

```python
data = [(1,'nami'),(2,'praj')]

rdd = spark.sparkContext.parallelize(data)

rdd = sc.parallelize(data)
print(rdd.collect())
```

### convert an RDD into a DataFrame

* To convert an RDD into a DataFrame, we can use `.toDF()`  