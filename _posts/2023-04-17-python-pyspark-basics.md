---
layout: article
title : "PySpark - Basics"
toc : true
tags: [Python Intermediate, Tutorial]
author: <author_id>
show_edit_on_github: false
excerpt_separator: <!--more-->
---

PySpark is a powerful open-source framework for distributed computing that allows developers and data scientist's to process large amounts of data in parallel across a cluster of computers. PySpark is built on top of Apache Spark, a big data processing engine, and provides a simple and user-friendly interface for working with large datasets using Python.
<!--more-->

Whilst using PySpark with a distributed cluster provides benefits in terms of performance, it can also be used locally as we will do here. The syntax is identical so we will use a local CSV file to demonstrate various data transformations that can be achieved.

This guide demonstrates some common data analysis and transformation workflows using the well known mtcars dataset.

## Imports

Lets start by importing everything we will need.

```python
from PySpark.sql import SparkSession
import PySpark.sql.functions as F
from PySpark.sql.types import *
import time
```

## Create a spark session

In PySpark, a SparkSession is the entry point to programming with Spark. It is the central point of control that allows you to configure Spark settings, create and manipulate data frames, and perform distributed processing on a cluster. You need to create a SparkSession object in your PySpark application to interact with Spark.

To work locally with PySpark we can create a simple session.

```python
spark = SparkSession.builder.getOrCreate()
```

## Session configuration

We can get and set configuration options with the `spark.conf.get()` and `spark.conf.set()` methods.

```python
# get current configuration
spark.conf.get(<configuration name>)

# set configuration
spark.conf.set(<configuration name>, <configuration value>)
```

## Schema

In PySpark, a `StructType` is a data type used to represent a structured or complex data type. It is a collection of `StructField` objects, where each field represents a named column of the `StructType`. A `StructType` can be thought of as a schema that defines the structure of a dataset, specifying the data type of each column and its corresponding name.

### Define a schema

```python
mtcars_schema = StructType([
    StructField(name = "model", dataType = StringType(), nullable = False),
    StructField("mpg", DoubleType(), False),
    StructField("cyl", IntegerType(), False),
])
```

### Read CSV
We can use the `spark.read.csv` method to read a CSV file.

```python
mtcars_df = spark.read.csv(path = "mtcars.csv", header = True)

mtcars_df.show()
+-------------------+----+---+-----+---+----+-----+-----+---+---+----+----+
|              model| mpg|cyl| disp| hp|drat|   wt| qsec| vs| am|gear|carb|
+-------------------+----+---+-----+---+----+-----+-----+---+---+----+----+
|          Mazda RX4|  21|  6|  160|110| 3.9| 2.62|16.46|  0|  1|   4|   4|
|      Mazda RX4 Wag|  21|  6|  160|110| 3.9|2.875|17.02|  0|  1|   4|   4|
|         Datsun 710|22.8|  4|  108| 93|3.85| 2.32|18.61|  1|  1|   4|   1|
|     Hornet 4 Drive|21.4|  6|  258|110|3.08|3.215|19.44|  1|  0|   3|   1|
|  Hornet Sportabout|18.7|  8|  360|175|3.15| 3.44|17.02|  0|  0|   3|   2|
|            Valiant|18.1|  6|  225|105|2.76| 3.46|20.22|  1|  0|   3|   1|
|         Duster 360|14.3|  8|  360|245|3.21| 3.57|15.84|  0|  0|   3|   4|
|          Merc 240D|24.4|  4|146.7| 62|3.69| 3.19|   20|  1|  0|   4|   2|
|           Merc 230|22.8|  4|140.8| 95|3.92| 3.15| 22.9|  1|  0|   4|   2|
|           Merc 280|19.2|  6|167.6|123|3.92| 3.44| 18.3|  1|  0|   4|   4|
|          Merc 280C|17.8|  6|167.6|123|3.92| 3.44| 18.9|  1|  0|   4|   4|
|         Merc 450SE|16.4|  8|275.8|180|3.07| 4.07| 17.4|  0|  0|   3|   3|
|         Merc 450SL|17.3|  8|275.8|180|3.07| 3.73| 17.6|  0|  0|   3|   3|
|        Merc 450SLC|15.2|  8|275.8|180|3.07| 3.78|   18|  0|  0|   3|   3|
| Cadillac Fleetwood|10.4|  8|  472|205|2.93| 5.25|17.98|  0|  0|   3|   4|
|Lincoln Continental|10.4|  8|  460|215|   3|5.424|17.82|  0|  0|   3|   4|
|  Chrysler Imperial|14.7|  8|  440|230|3.23|5.345|17.42|  0|  0|   3|   4|
|           Fiat 128|32.4|  4| 78.7| 66|4.08|  2.2|19.47|  1|  1|   4|   1|
|        Honda Civic|30.4|  4| 75.7| 52|4.93|1.615|18.52|  1|  1|   4|   2|
|     Toyota Corolla|33.9|  4| 71.1| 65|4.22|1.835| 19.9|  1|  1|   4|   1|
+-------------------+----+---+-----+---+----+-----+-----+---+---+----+----+
only showing top 20 rows
```

This approach reads the entirety of the CSV file and autodetect's the type and headers.

The `.show()` method can be used to print a dataframe to the console. We can also specify the number of rows to display, for example `.show(5)`.

We can specifically import only the desired columns and specify the types of those columns using a schema object.

```python
mtcars_df = spark.read.csv(
    path = "mtcars.csv",
    header = True,
    schema = mtcars_schema,
)

mtcars_df.show()

+-------------------+----+---+
|              model| mpg|cyl|
+-------------------+----+---+
|          Mazda RX4|21.0|  6|
|      Mazda RX4 Wag|21.0|  6|
|         Datsun 710|22.8|  4|
|     Hornet 4 Drive|21.4|  6|
|  Hornet Sportabout|18.7|  8|
|            Valiant|18.1|  6|
|         Duster 360|14.3|  8|
|          Merc 240D|24.4|  4|
|           Merc 230|22.8|  4|
|           Merc 280|19.2|  6|
|          Merc 280C|17.8|  6|
|         Merc 450SE|16.4|  8|
|         Merc 450SL|17.3|  8|
|        Merc 450SLC|15.2|  8|
| Cadillac Fleetwood|10.4|  8|
|Lincoln Continental|10.4|  8|
|  Chrysler Imperial|14.7|  8|
|           Fiat 128|32.4|  4|
|        Honda Civic|30.4|  4|
|     Toyota Corolla|33.9|  4|
+-------------------+----+---+
```

We can also read a CSV file using the `format` method and `option` method. This approach can provide greater flexibility when specifying multiple options. There should be no noticeable performance benefit in using either approach.

```python
mtcars_df = spark.read.format("csv").option("header", "true").load("mtcars.csv")
```

### Schema to parse nested column

We can also use a schema to parse nested data.

```python
# define schema
nested_schema = StructType([
    StructField("name", StringType(), True),
    StructField("details", StructType([
        StructField("age", IntegerType(), True),
        StructField("height", IntegerType(), True)
    ]), True)
])

# example data
nested_data = [
    ("John", {"age": 31, "height": 178}),
    ("Jane", {"age": 42, "height": 158})
]

# parse to spark dataframe
nested_df = spark.createDataFrame(data, schema=schema)

nested_df.show()

+----+---------+
|name|  details|
+----+---------+
|John|{31, 178}|
|Jane|{42, 158}|
+----+---------+
```

## Read/write parquet

Parquet is a columnar storage format for big data processing that optimizes performance by storing data in a compressed binary format. It's efficient for processing large datasets and supports advanced features like predicate pushdown, column pruning, and schema evolution.

Reading and writing parquet is straightforward.

```python

# write parquet
mtcars_df.write.format('parquet').save('mtcars.parquet', mode='overwrite')

# read parquet
mtcars_df = spark.read.format('parquet').load('mtcars.parquet')

# alternate syntax for read parquet
mtcars_df = spark.read.parquet('mtcars.parquet')
```

In Spark, it's common to begin with data sources such as CSV, JSON, or other formats. Although these formats offer versatility for loading data, they may not be the most efficient for Spark. The Parquet format is a column-oriented data store that enables Spark to leverage predicate pushdown for better performance.

## Perform SQL-like queries on the data

The `createOrReplaceTempView` function in PySpark allows you to create a temporary view of a DataFrame, which you can use to perform SQL-like queries on the data. The view is temporary, meaning it exists only for the duration of your SparkSession and is not saved to disk. This function is useful when you need to manipulate and analyze a DataFrame using SQL queries, especially when you have complex transformations that are easier to express in SQL.

```python
mtcars_df.createOrReplaceTempView("mtcars")

low_mpg_cars = spark.sql("SELECT * FROM mtcars WHERE mpg < 20 LIMIT 5")

low_mpg_cars.show()
+-------------------+----+---+
|              model| mpg|cyl|
+-------------------+----+---+
|  Hornet Sportabout|18.7|  8|
|            Valiant|18.1|  6|
|         Duster 360|14.3|  8|
|           Merc 280|19.2|  6|
+-------------------+----+---+
```

## Analysis and manipulation

### Investigate a dataframe's structure with `columns` and `printSchema()`

To get a list containing the names of a dataframe's columns, use the `columns` method.

```python
mtcars_df.columns

['model', 'mpg', 'cyl', 'disp', 'hp', 'drat', 'wt', 'qsec', 'vs', 'am', 'gear', 'carb']
```

For a detailed overview of a dataframe's schema use `printSchema()`.

```python
mtcars_df.printSchema()

root
 |-- model: string (nullable = true)
 |-- mpg: string (nullable = true)
 |-- cyl: string (nullable = true)
 |-- disp: string (nullable = true)
 |-- hp: string (nullable = true)
 |-- drat: string (nullable = true)
 |-- wt: string (nullable = true)
 |-- qsec: string (nullable = true)
 |-- vs: string (nullable = true)
 |-- am: string (nullable = true)
 |-- gear: string (nullable = true)
 |-- carb: string (nullable = true)
```

### Count rows with `count()`

To get the number of rows in a dataframe.

```python
mtcars_df.count()

32
```

### Order data with `orderBy()`, `F.desc()` and `F.asc()`

We can use the `orderBy()` method along with `F.desc()` and `F.asc()` to order a dataframe. The example's below demonstrate the ways in which this can be achieved.

```python
# default order (ascending)
mtcars_df.orderBy("mpg").show(1)


# ascending order
mtcars_df.orderBy("mpg", ascending=True).show(1)

+-------------------+----+---+----+---+----+-----+-----+---+---+----+----+
|              model| mpg|cyl|disp| hp|drat|   wt| qsec| vs| am|gear|carb|
+-------------------+----+---+----+---+----+-----+-----+---+---+----+----+
|Lincoln Continental|10.4|  8| 460|215|   3|5.424|17.82|  0|  0|   3|   4|
+-------------------+----+---+----+---+----+-----+-----+---+---+----+----+
only showing top 1 row

# descending order
mtcars_df.orderBy("mpg", ascending=False).show(1)

+--------------+----+---+----+---+----+-----+----+---+---+----+----+
|         model| mpg|cyl|disp| hp|drat|   wt|qsec| vs| am|gear|carb|
+--------------+----+---+----+---+----+-----+----+---+---+----+----+
|Toyota Corolla|33.9|  4|71.1| 65|4.22|1.835|19.9|  1|  1|   4|   1|
+--------------+----+---+----+---+----+-----+----+---+---+----+----+
only showing top 1 row

# descending order using F.desc()
mtcars_df.orderBy(F.desc("mpg")).show(1)

+--------------+----+---+----+---+----+-----+----+---+---+----+----+
|         model| mpg|cyl|disp| hp|drat|   wt|qsec| vs| am|gear|carb|
+--------------+----+---+----+---+----+-----+----+---+---+----+----+
|Toyota Corolla|33.9|  4|71.1| 65|4.22|1.835|19.9|  1|  1|   4|   1|
+--------------+----+---+----+---+----+-----+----+---+---+----+----+
only showing top 1 row

# ascending order using F.asc()
mtcars_df.orderBy(F.asc("mpg")).show(1)

+-------------------+----+---+----+---+----+-----+-----+---+---+----+----+
|              model| mpg|cyl|disp| hp|drat|   wt| qsec| vs| am|gear|carb|
+-------------------+----+---+----+---+----+-----+-----+---+---+----+----+
|Lincoln Continental|10.4|  8| 460|215|   3|5.424|17.82|  0|  0|   3|   4|
+-------------------+----+---+----+---+----+-----+-----+---+---+----+----+
only showing top 1 row
```

### Add unique ID's with `monotonically_increasing_id()`

The `monotonically_increasing_id()` function can be used to add unique ID's to a dataframe. When working with a large DataFrame, `monotonically_increasing_id()` can be computation and memory intensive.

```python
mtcars_df.withColumn("id", F.monotonically_increasing_id()).show(5)

+-----------------+----+---+----+---+----+-----+-----+---+---+----+----+---+
|            model| mpg|cyl|disp| hp|drat|   wt| qsec| vs| am|gear|carb| id|
+-----------------+----+---+----+---+----+-----+-----+---+---+----+----+---+
|        Mazda RX4|  21|  6| 160|110| 3.9| 2.62|16.46|  0|  1|   4|   4|  0|
|    Mazda RX4 Wag|  21|  6| 160|110| 3.9|2.875|17.02|  0|  1|   4|   4|  1|
|       Datsun 710|22.8|  4| 108| 93|3.85| 2.32|18.61|  1|  1|   4|   1|  2|
|   Hornet 4 Drive|21.4|  6| 258|110|3.08|3.215|19.44|  1|  0|   3|   1|  3|
|Hornet Sportabout|18.7|  8| 360|175|3.15| 3.44|17.02|  0|  0|   3|   2|  4|
+-----------------+----+---+----+---+----+-----+-----+---+---+----+----+---+
only showing top 5 rows
```

### Distinct rows with `distinct()`

We can get the distinct values from one or more columns with `distinct()`.

```python
# get all distinct rows
mtcars_df.distinct().count()

32

# get all distinct values from a specific column
mtcars_df.select("mpg").distinct().count()

25

# get all distinct values from specific columns
mtcars_df.select("carb", "cyl").distinct().count()

9
```

### Drop rows with duplicate values with `drop_duplicates()`

We can drop duplicate rows, whether duplicated in their entirety or across specific columns using the `drop_duplicates()` function. Note that when specifying columns to deduplicate on, the first unique record will be kept, so order is often important.

```python
# drop duplicate rows - NOTE there are no duplicated rows in the example data
mtcars_df.drop_duplicates().count()

32

# drop duplicates on a specific column
mtcars_df.drop_duplicates(['carb']).show()

+-----------------+----+---+-----+---+----+----+-----+---+---+----+----+
|            model| mpg|cyl| disp| hp|drat|  wt| qsec| vs| am|gear|carb|
+-----------------+----+---+-----+---+----+----+-----+---+---+----+----+
|       Datsun 710|22.8|  4|  108| 93|3.85|2.32|18.61|  1|  1|   4|   1|
|Hornet Sportabout|18.7|  8|  360|175|3.15|3.44|17.02|  0|  0|   3|   2|
|       Merc 450SE|16.4|  8|275.8|180|3.07|4.07| 17.4|  0|  0|   3|   3|
|        Mazda RX4|  21|  6|  160|110| 3.9|2.62|16.46|  0|  1|   4|   4|
|     Ferrari Dino|19.7|  6|  145|175|3.62|2.77| 15.5|  0|  1|   5|   6|
|    Maserati Bora|  15|  8|  301|335|3.54|3.57| 14.6|  0|  1|   5|   8|
+-----------------+----+---+-----+---+----+----+-----+---+---+----+----+

# drop duplicates on a specific columns
mtcars_df.drop_duplicates(['carb', "cyl"]).show()

+-----------------+----+---+-----+---+----+-----+-----+---+---+----+----+
|            model| mpg|cyl| disp| hp|drat|   wt| qsec| vs| am|gear|carb|
+-----------------+----+---+-----+---+----+-----+-----+---+---+----+----+
|       Datsun 710|22.8|  4|  108| 93|3.85| 2.32|18.61|  1|  1|   4|   1|
|   Hornet 4 Drive|21.4|  6|  258|110|3.08|3.215|19.44|  1|  0|   3|   1|
|        Merc 240D|24.4|  4|146.7| 62|3.69| 3.19|   20|  1|  0|   4|   2|
|Hornet Sportabout|18.7|  8|  360|175|3.15| 3.44|17.02|  0|  0|   3|   2|
|       Merc 450SE|16.4|  8|275.8|180|3.07| 4.07| 17.4|  0|  0|   3|   3|
|        Mazda RX4|  21|  6|  160|110| 3.9| 2.62|16.46|  0|  1|   4|   4|
|       Duster 360|14.3|  8|  360|245|3.21| 3.57|15.84|  0|  0|   3|   4|
|     Ferrari Dino|19.7|  6|  145|175|3.62| 2.77| 15.5|  0|  1|   5|   6|
|    Maserati Bora|  15|  8|  301|335|3.54| 3.57| 14.6|  0|  1|   5|   8|
+-----------------+----+---+-----+---+----+-----+-----+---+---+----+----+
```


### Merge two dataframe's with `union()`

PySpark's `union()` function is a transformation used to merge two or more dataframe's with the same schema or structure. If the schemas are not the same then the function returns an error.

Here we will perform the union using `mtcars_df` with itself.

```python
union_all_df = mtcars_df.union(mtcars_df)

union_all_df.count()

64

# keep only distinct rows from the union
union_distinct_df = mtcars_df.union(mtcars_df).distinct()

union_distinct_df.count()

32
```

### Filter rows in a dataframe with `filter()`, `where()`, `startswith()`, and `like()`

We can use the `filter()` or `where()` functions to filter a dataframe. We can combine `filter()` and `where()` with functions like `startswith()` and `like()`. 

`filter()` and `where()` can be used interchangeably.

```python
# return rows where mpg == 22.8
mtcars_df.filter(mtcars_df.mpg == 22.8).show()

+----------+----+---+-----+---+----+----+-----+---+---+----+----+
|     model| mpg|cyl| disp| hp|drat|  wt| qsec| vs| am|gear|carb|
+----------+----+---+-----+---+----+----+-----+---+---+----+----+
|Datsun 710|22.8|  4|  108| 93|3.85|2.32|18.61|  1|  1|   4|   1|
|  Merc 230|22.8|  4|140.8| 95|3.92|3.15| 22.9|  1|  0|   4|   2|
+----------+----+---+-----+---+----+----+-----+---+---+----+----+

# return rows where model starts with 'M'
mtcars_df.filter(mtcars_df.model.startswith('M')).show()

+-------------+----+---+-----+---+----+-----+-----+---+---+----+----+
|        model| mpg|cyl| disp| hp|drat|   wt| qsec| vs| am|gear|carb|
+-------------+----+---+-----+---+----+-----+-----+---+---+----+----+
|    Mazda RX4|  21|  6|  160|110| 3.9| 2.62|16.46|  0|  1|   4|   4|
|Mazda RX4 Wag|  21|  6|  160|110| 3.9|2.875|17.02|  0|  1|   4|   4|
|    Merc 240D|24.4|  4|146.7| 62|3.69| 3.19|   20|  1|  0|   4|   2|
|     Merc 230|22.8|  4|140.8| 95|3.92| 3.15| 22.9|  1|  0|   4|   2|
|     Merc 280|19.2|  6|167.6|123|3.92| 3.44| 18.3|  1|  0|   4|   4|
|    Merc 280C|17.8|  6|167.6|123|3.92| 3.44| 18.9|  1|  0|   4|   4|
|   Merc 450SE|16.4|  8|275.8|180|3.07| 4.07| 17.4|  0|  0|   3|   3|
|   Merc 450SL|17.3|  8|275.8|180|3.07| 3.73| 17.6|  0|  0|   3|   3|
|  Merc 450SLC|15.2|  8|275.8|180|3.07| 3.78|   18|  0|  0|   3|   3|
|Maserati Bora|  15|  8|  301|335|3.54| 3.57| 14.6|  0|  1|   5|   8|
+-------------+----+---+-----+---+----+-----+-----+---+---+----+----+

# return rows using like()
mtcars_df.filter(mtcars_df.model.like('M%') & (mtcars_df.mpg > 21)).show()

+---------+----+---+-----+---+----+----+----+---+---+----+----+
|    model| mpg|cyl| disp| hp|drat|  wt|qsec| vs| am|gear|carb|
+---------+----+---+-----+---+----+----+----+---+---+----+----+
|Merc 240D|24.4|  4|146.7| 62|3.69|3.19|  20|  1|  0|   4|   2|
| Merc 230|22.8|  4|140.8| 95|3.92|3.15|22.9|  1|  0|   4|   2|
+---------+----+---+-----+---+----+----+----+---+---+----+----+

# alternate syntax using where
mtcars_df.where(mtcars_df.model.like('M%') & (mtcars_df.mpg > 21)).show()

+---------+----+---+-----+---+----+----+----+---+---+----+----+
|    model| mpg|cyl| disp| hp|drat|  wt|qsec| vs| am|gear|carb|
+---------+----+---+-----+---+----+----+----+---+---+----+----+
|Merc 240D|24.4|  4|146.7| 62|3.69|3.19|  20|  1|  0|   4|   2|
| Merc 230|22.8|  4|140.8| 95|3.92|3.15|22.9|  1|  0|   4|   2|
+---------+----+---+-----+---+----+----+----+---+---+----+----+
```

### Select columns in a dataframe with `select()`

We can use `select()` to keep specified columns.

```python
mtcars_df.select("model", "mpg").show(5)

+-----------------+----+
|            model| mpg|
+-----------------+----+
|        Mazda RX4|  21|
|    Mazda RX4 Wag|  21|
|       Datsun 710|22.8|
|   Hornet 4 Drive|21.4|
|Hornet Sportabout|18.7|
+-----------------+----+
only showing top 5 rows
```

### Drop columns in a dataframe with `drop()`

We can use `drop()` to drop specified columns.

```python
mtcars_df.drop("cyl", "gear", "carb").show(5)

+-----------------+----+----+---+----+-----+-----+---+---+
|            model| mpg|disp| hp|drat|   wt| qsec| vs| am|
+-----------------+----+----+---+----+-----+-----+---+---+
|        Mazda RX4|  21| 160|110| 3.9| 2.62|16.46|  0|  1|
|    Mazda RX4 Wag|  21| 160|110| 3.9|2.875|17.02|  0|  1|
|       Datsun 710|22.8| 108| 93|3.85| 2.32|18.61|  1|  1|
|   Hornet 4 Drive|21.4| 258|110|3.08|3.215|19.44|  1|  0|
|Hornet Sportabout|18.7| 360|175|3.15| 3.44|17.02|  0|  0|
+-----------------+----+----+---+----+-----+-----+---+---+
only showing top 5 rows
```


### Negate a condition with `~`

We can use the `~` symbol to negate boolean expressions or conditions.

```python
# return rows where cyl is equal to 6
mtcars_df.filter(mtcars_df.cyl == 6).show()

+--------------+----+---+-----+---+----+-----+-----+---+---+----+----+
|         model| mpg|cyl| disp| hp|drat|   wt| qsec| vs| am|gear|carb|
+--------------+----+---+-----+---+----+-----+-----+---+---+----+----+
|     Mazda RX4|  21|  6|  160|110| 3.9| 2.62|16.46|  0|  1|   4|   4|
| Mazda RX4 Wag|  21|  6|  160|110| 3.9|2.875|17.02|  0|  1|   4|   4|
|Hornet 4 Drive|21.4|  6|  258|110|3.08|3.215|19.44|  1|  0|   3|   1|
|       Valiant|18.1|  6|  225|105|2.76| 3.46|20.22|  1|  0|   3|   1|
|      Merc 280|19.2|  6|167.6|123|3.92| 3.44| 18.3|  1|  0|   4|   4|
|     Merc 280C|17.8|  6|167.6|123|3.92| 3.44| 18.9|  1|  0|   4|   4|
|  Ferrari Dino|19.7|  6|  145|175|3.62| 2.77| 15.5|  0|  1|   5|   6|
+--------------+----+---+-----+---+----+-----+-----+---+---+----+----+

# return rows where cyl is NOT equal to 6
mtcars_df.filter(~(mtcars_df.cyl == 6)).show()

+-------------------+----+---+-----+---+----+-----+-----+---+---+----+----+
|              model| mpg|cyl| disp| hp|drat|   wt| qsec| vs| am|gear|carb|
+-------------------+----+---+-----+---+----+-----+-----+---+---+----+----+
|         Datsun 710|22.8|  4|  108| 93|3.85| 2.32|18.61|  1|  1|   4|   1|
|  Hornet Sportabout|18.7|  8|  360|175|3.15| 3.44|17.02|  0|  0|   3|   2|
|         Duster 360|14.3|  8|  360|245|3.21| 3.57|15.84|  0|  0|   3|   4|
|          Merc 240D|24.4|  4|146.7| 62|3.69| 3.19|   20|  1|  0|   4|   2|
|           Merc 230|22.8|  4|140.8| 95|3.92| 3.15| 22.9|  1|  0|   4|   2|
|         Merc 450SE|16.4|  8|275.8|180|3.07| 4.07| 17.4|  0|  0|   3|   3|
|         Merc 450SL|17.3|  8|275.8|180|3.07| 3.73| 17.6|  0|  0|   3|   3|
|        Merc 450SLC|15.2|  8|275.8|180|3.07| 3.78|   18|  0|  0|   3|   3|
| Cadillac Fleetwood|10.4|  8|  472|205|2.93| 5.25|17.98|  0|  0|   3|   4|
|Lincoln Continental|10.4|  8|  460|215|   3|5.424|17.82|  0|  0|   3|   4|
|  Chrysler Imperial|14.7|  8|  440|230|3.23|5.345|17.42|  0|  0|   3|   4|
|           Fiat 128|32.4|  4| 78.7| 66|4.08|  2.2|19.47|  1|  1|   4|   1|
|        Honda Civic|30.4|  4| 75.7| 52|4.93|1.615|18.52|  1|  1|   4|   2|
|     Toyota Corolla|33.9|  4| 71.1| 65|4.22|1.835| 19.9|  1|  1|   4|   1|
|      Toyota Corona|21.5|  4|120.1| 97| 3.7|2.465|20.01|  1|  0|   3|   1|
|   Dodge Challenger|15.5|  8|  318|150|2.76| 3.52|16.87|  0|  0|   3|   2|
|        AMC Javelin|15.2|  8|  304|150|3.15|3.435| 17.3|  0|  0|   3|   2|
|         Camaro Z28|13.3|  8|  350|245|3.73| 3.84|15.41|  0|  0|   3|   4|
|   Pontiac Firebird|19.2|  8|  400|175|3.08|3.845|17.05|  0|  0|   3|   2|
|          Fiat X1-9|27.3|  4|   79| 66|4.08|1.935| 18.9|  1|  1|   4|   1|
+-------------------+----+---+-----+---+----+-----+-----+---+---+----+----+
```

### Round a number with `F.round()`

The `round()` function is used to round the values in a column to a specified number of decimal places. 

```python
mtcars_df.select(F.round("wt", 1)).show(5)

+------------+
|round(wt, 1)|
+------------+
|         2.6|
|         2.9|
|         2.3|
|         3.2|
|         3.4|
+------------+
only showing top 5 rows
```

### Add or mutate an existing column with `withColumn()`

We can use the `withColumn()` function to add a new column to a dataframe or to replace an existing column with a new one based on a specified transformation.

The `withColumn()` function takes two arguments: the name of the new or replacement column, and the the transformation expression that defines the values of the new or replacement column.

Let's add a new column showing the displacement per cylinder. We can round the value to make it more presentable in the same operation.

```python
mtcars_df.withColumn("disp_per_cyl", F.round(mtcars_df.disp / mtcars_df.cyl, 1)).show(5)

+-----------------+----+---+----+---+----+-----+-----+---+---+----+----+------------+
|            model| mpg|cyl|disp| hp|drat|   wt| qsec| vs| am|gear|carb|disp_per_cyl|
+-----------------+----+---+----+---+----+-----+-----+---+---+----+----+------------+
|        Mazda RX4|  21|  6| 160|110| 3.9| 2.62|16.46|  0|  1|   4|   4|        26.7|
|    Mazda RX4 Wag|  21|  6| 160|110| 3.9|2.875|17.02|  0|  1|   4|   4|        26.7|
|       Datsun 710|22.8|  4| 108| 93|3.85| 2.32|18.61|  1|  1|   4|   1|        27.0|
|   Hornet 4 Drive|21.4|  6| 258|110|3.08|3.215|19.44|  1|  0|   3|   1|        43.0|
|Hornet Sportabout|18.7|  8| 360|175|3.15| 3.44|17.02|  0|  0|   3|   2|        45.0|
+-----------------+----+---+----+---+----+-----+-----+---+---+----+----+------------+
only showing top 5 rows
```


### Cast a column to a specified type with `cast()`

We can change a column type with `cast()`

```python
mtcars_df.select("cyl").printSchema()

root
 |-- cyl: string (nullable = true)

mtcars_df.withColumn("cyl", mtcars_df.cyl.cast("float")).select("cyl").printSchema()

root
 |-- cyl: float (nullable = true)
```

### Aggregation and grouping with `agg()` and `groupby()`

#### Group minimum and maximums with `F.min()` and `F.max()`

The `agg()` method is often (though not exclusively) used in combination with the `groupBy()` method to group data by one or more columns and then perform aggregate calculations on the groups.

Let's look at the minimum and maximum values for mpg by cylinder.

```python
mtcars_df.groupBy("cyl").agg(F.min("mpg")).show()

+---+--------+
|cyl|min(mpg)|
+---+--------+
|  4|    21.4|
|  6|    17.8|
|  8|    10.4|
+---+--------+

mtcars_df.groupBy("cyl").agg(F.max("mpg")).show()

+---+--------+
|cyl|max(mpg)|
+---+--------+
|  4|    33.9|
|  6|    21.4|
|  8|    19.2|
+---+--------+
```

To get the min and max values without any grouping we can omit the `groupBy()` operation.

```python
mtcars_df.agg(F.min("mpg")).show()
+--------+
|min(mpg)|
+--------+
|    10.4|
+--------+

mtcars_df.agg(F.max("mpg")).show()
+--------+
|max(mpg)|
+--------+
|    33.9|
+--------+
```

#### Specify the name of aggregated columns with `alias()`

The `alias()` method can be used to rename a column or to assign an alias to an expression.

Here we can use `alias()` to avoid the newly created columns receiving the default names `min(mpg)` and `max(mpg)`.

```python
mtcars_df.groupBy("cyl").agg(F.min("mpg").alias("min_mpg"), F.max("mpg").alias("max_mpg")).show()

+---+-------+-------+
|cyl|min_mpg|max_mpg|
+---+-------+-------+
|  4|   21.4|   33.9|
|  6|   17.8|   21.4|
|  8|   10.4|   19.2|
+---+-------+-------+
```
### Join two dataframe's with `join()`

We can use the `join()` method to join two dataframe's. First, lets store the min and max mpg values by cylinder in a new dataframe.

```python
cyl_min_max_df = mtcars_df.groupBy("cyl").agg(F.min("mpg").alias("min_mpg_by_cyl"), F.max("mpg").alias("max_mpg_by_cyl"))

cyl_min_max_df.show()

+---+-------+-------+
|cyl|min_mpg|max_mpg|
+---+-------+-------+
|  4|   21.4|   33.9|
|  6|   17.8|   21.4|
|  8|   10.4|   19.2|
+---+-------+-------+
```
We can now join the min and max values to `mtcars_df`. Note that we specify the join column using the `on` argument, which can accept a list.

```python
mtcars_df.join(cyl_min_max_df, on="cyl").select("model", "cyl", "min_mpg_by_cyl", "max_mpg_by_cyl").show(5)

+-------------+---+--------------+--------------+
|        model|cyl|min_mpg_by_cyl|max_mpg_by_cyl|
+-------------+---+--------------+--------------+
|   Volvo 142E|  4|          21.4|          33.9|
| Lotus Europa|  4|          21.4|          33.9|
|Porsche 914-2|  4|          21.4|          33.9|
|    Fiat X1-9|  4|          21.4|          33.9|
|Toyota Corona|  4|          21.4|          33.9|
+-------------+---+--------------+--------------+
```

We can also specify the type of join that is required using the `how` argument. As an example, using an anti-join here will return an empty dataframe.

```python
mtcars_df.join(cyl_min_max_df, on="cyl", how="anti").show(5)

+---+-----+---+----+---+----+---+----+---+---+----+----+
|cyl|model|mpg|disp| hp|drat| wt|qsec| vs| am|gear|carb|
+---+-----+---+----+---+----+---+----+---+---+----+----+
+---+-----+---+----+---+----+---+----+---+---+----+----+
```

### Improve join performance with `F.broadcast()`

We can use the `F.broadcast()` function to improve join performance. When we broadcast a dataframe, it is sent to all worker nodes in the cluster so that the join operation can be performed locally. This can reduce the amount of data that needs to be transferred over the network and thus speed up the join operation.

Broadcasting can be especially effective when one dataframe is significantly smaller than the memory available on each worker node. If the dataframe is too large to fit in memory, broadcasting it may not be effective and can actually slow down the join operation.

Using `broadcast()` is syntactically very simple.

```python
mtcars_df.join(F.broadcast(cyl_min_max_df), on="cyl").select("model", "cyl", "min_mpg_by_cyl", "max_mpg_by_cyl").show(5)

+-----------------+---+--------------+--------------+
|            model|cyl|min_mpg_by_cyl|max_mpg_by_cyl|
+-----------------+---+--------------+--------------+
|        Mazda RX4|  6|          17.8|          21.4|
|    Mazda RX4 Wag|  6|          17.8|          21.4|
|       Datsun 710|  4|          21.4|          33.9|
|   Hornet 4 Drive|  6|          17.8|          21.4|
|Hornet Sportabout|  8|          10.4|          19.2|
+-----------------+---+--------------+--------------+
only showing top 5 rows
```

Note that you will need to be using a distributed computing environment to benefit from using `broadcast()`.

### Create user defined functions (UDF) with `F.udf()`

#### Standalone udf's

When we use a UDF, Spark serializes the data, sends it to a worker node, applies the UDF to the data, and then deserializes the result. This process can be slower than vectorized operations, especially for large datasets.

We start by creating a function in the typical way, in this case let's create a function that reverses a string.

```python
def reverse_string(s: str) -> str:
    """
    Reverse a string
    :param s: string to reverse
    :return: reversed string
    """
    return s[::-1]

assert reverse_string("hello") == "olleh"
```

Unfortunately, if we try to apply our function to a spark dataframe column we receive an error.

```python
mtcars_df.withColumn("reverse_model", reverse_string(mtcars_df.model)).show(5)

ValueError: slice with step is not supported.
```

In order to apply our function to a column in our dataframe we need to wrap it with `udf()`.

```python
reverse_string_udf = F.udf(reverse_string, StringType())
```

We can now use `reverse_string_udf()` to achieve our desired result.

```python
mtcars_df.withColumn("reverse_model", reverse_string_udf(mtcars_df.model)).select("model", "reverse_model").show(5)

+-----------------+-----------------+
|            model|    reverse_model|
+-----------------+-----------------+
|        Mazda RX4|        4XR adzaM|
|    Mazda RX4 Wag|    gaW 4XR adzaM|
|       Datsun 710|       017 nustaD|
|   Hornet 4 Drive|   evirD 4 tenroH|
|Hornet Sportabout|tuobatropS tenroH|
+-----------------+-----------------+
only showing top 5 rows
```

#### Singleton for udf's

We can use a singleton design pattern to create a module that contains multiple related UDFs.

```python
class UDFs:

    def __init__(self):
        self.reverse_string_udf = F.udf(self._reverse_string, StringType())
    
    def reverse_string(self, s: str) -> str:
        return self.reverse_string_udf(s)

    @classmethod
    def _reverse_string(cls, s: str) -> str:
        """
        Reverse a string
        :param s: string to reverse
        :return: reversed string
        """
        return s[::-1]

# instantiate the class
udfs = UDFs()

# use the class
mtcars_df.withColumn("reverse_model", udfs.reverse_string(mtcars_df.model)).select("model", "reverse_model").show(5)

+-----------------+-----------------+
|            model|    reverse_model|
+-----------------+-----------------+
|        Mazda RX4|        4XR adzaM|
|    Mazda RX4 Wag|    gaW 4XR adzaM|
|       Datsun 710|       017 nustaD|
|   Hornet 4 Drive|   evirD 4 tenroH|
|Hornet Sportabout|tuobatropS tenroH|
+-----------------+-----------------+
only showing top 5 rows
```

### Caching
Caching a dataframe in memory can improve performance when the same dataframe is used multiple times. This is because the dataframe is only read from disk once and then stored in memory. Subsequent reads of the dataframe will be faster as the data is already in memory. 

When using `cache()` it is important to ensure that the data will fit in the memory available. If the data is too large to fit in memory then it's better to use a disk based backing store such as parquet.

`cache()` is a transformation so it will not be evaluated until an action is performed.  This means that the dataframe will not be cached until an action is performed.

```python
mtcars_df = mtcars_df.distinct().cache()

start_time = time.time()

print("`count()` of %d rows took %f seconds" % (mtcars_df.count(), time.time() - start_time))

`count()` of 32 rows took 0.500973 seconds

start_time = time.time()

print("`count()` of %d rows took %f seconds with cached data" % (mtcars_df.count(), time.time() - start_time))


`count()` of 32 rows took 0.291104 seconds with cached data
```

We can check whether a dataframe is currently cached with `.iscached` and uncache it with `unpersist()`.

```python
mtcars_df.is_cached
True

mtcars_df.unpersist()

mtcars_df.is_cached

False
```

## Next steps

PySpark is a powerful tool for processing big data and solving complex data problems. It's ease of use and syntactic similarity to popular data science libraries make it a popular choice for data scientists and engineers alike. Whether you're just getting started with big data or you're an experienced professional, PySpark is sure to be a valuable addition to your data processing toolkit.

