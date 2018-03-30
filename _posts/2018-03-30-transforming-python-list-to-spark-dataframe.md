---
layout: full-width
title:  "Transforming Python Lists into Spark Dataframes"
date:   2018-03-30
---

Data represented as dataframes are generally much easier to transform, filter, or write to a target source. In Spark, loading or querying data from a source will automatically be loaded as a dataframe. 

Here's an example of loading, querying, and writing data using PySpark and SQL:

``` python
import pyspark

# Define your SparkContext and SparkSession
sc = pyspark.context.SparkContext(master='host', appName='Sample App')
session = pyspark.sql.session.SparkSession(sc)

"""
Load your data using:
  - spark.read.json('some/path/or/url') 
  - spark.read.parquet('some/path/or/url')
  - spark.read.csv('some/path/or/url')
  - spark.read.text('some/path/or/url'), etc.
"""
data = spark.read.json('some/path/or/url') 
data.createOrReplaceTempView("table")

# Apply some SQL query to the data, which results in a DataFrame
df = session.sql("""
  select col1, col2, sum(col3)
  from table
  where col4 = 'some_val'
  group by col1, col2
""")

# Write the query results to a target in your desired format (say, JSON)
df.write.json('target/path/')
```

The example above works conveniently if you can easily load your data as a dataframe using PySpark's built-in functions. But sometimes you're in a situation where your processed data ends up as a list of Python dictionaries, say when you weren't required to use `spark.read` and/or `session.sql`. How can you load your data as a Spark DataFrame in order to take advantage of its capabilities?

<!--more-->

Let's say you have a list of dictionaries as follows:

``` python
data = [
    {
        'id': 123,
        'uuid': '123e4567-e89b-12d3-a456',
        'description': 'lorem ipsum',
        'price': 45.67,
        'some_date': date_object,
        'some_timestamp': datetime_object,
        'binary_file': b'binary-encoding',
        'deleted': False,
        'tags': ['tag1', 'tag2', 'tag3'],
        'metadata': {
            'source': 'universe',
            'original_price': 33,
        }
    },
    # ...
]
```

Converting this into a Spark DataFrame is as simple as knowing how the datatype of each key-value pair of its dictionaries map to one of PySpark's DataType subclasses. You can find the latest list of available PySpark data types [here](https://spark.apache.org/docs/latest/api/python/_modules/pyspark/sql/types.html).

``` python
from pyspark.sql.types import *

fields = [
    StructField('id', IntegerType(), False),  
    StructField('uuid', StringType(), False),  
    StructField('description', TextType(), False), 
    StructField('price', DecimalType(precision=10, scale=2), False),
    StructField('some_date', DateType(), False),  
    StructField('some_timestamp', TimestampType(), False),
    StructField('binary_file', BinaryType(), False),  
    StructField('deleted', BooleanType(), False),
    StructField('tags', ArrayType(StringType()), False),
    StructField('metadata', StructType(), False)
]

schema = StructType(fields)
df = spark.createDataFrame(data, schema)

# DataFrame related stuff here
df.write.mode('overwrite').parquet('/some/target')
```

You can actually skip the type matching above and let Spark infer the datatypes contained in the dictionaries. But I personally do not encourage this because automatically inferring data types may lead to hard-to-debug side effects when you process the data downstream. It's better to be explicit right from the start so you can confidently handle the data moving forward knowing that the data types for the fields are what you specified them to be.
