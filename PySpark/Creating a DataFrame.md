To create a DataFrame in pyspark, you can either create it with hardcode values on the go or load a file

```python
data = [(1,'ricky',23),((2,'nick',19))]
df = spark.createDataFrame(data)
```

This is a simple way to create a DataFrames with HardCoded values and no Schema

Usually when we do this we also need something called schema so we can get the names of the columns. if we just ran the above code and looked at the DataFrame, it would look something like this
<!--
+---+-----+---+
 |  _1|      _2|   _3| 
+---+-----+---+ 
 | 1   |  ricky|  23|
 | 2   |   nick|  19| 
+---+-----+---+
-->

![[Pasted image 20240228164416.png]]

Notice how the names of the columns are numbers? we can change that before we create a dataframe or after

```python
data = [(1,'ricky',23),((2,'nick',19))]
schema = ['id','name','age']
df = spark.createDataFrame(data,schema)
```

This is a rudimentary way of creating schema. to actually control every part of the [[Git_vault/PySpark/Schema]] we will use something called StructType.