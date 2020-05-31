# Introduction to Spark ML

Here you can find the lightweight introduction to Spark ML.

## Agenda

1. [What is Spark and what it's relationship with DataBricks?](#what-is-spark)
2. [A small overview of Spark's architecture.](#sparks-architecture)
3. [Spark's Machine Learning explained](#sparks-machine-learning-explained)
4. [Let's play: set up the Azure DataBricks and create your first cluster.](#azure-databricks-set-up)
5. [Excersise with Aga: simple Machine Learning's excersise.](#ml-excersise)

# What is Spark?

Apache Spark is an **unified analitics engine**, created for **large-scale data processing**. Unified, because you can run the same method to work on static (csv) files or some real-time data (streams). 

It is **the best for large-scale**, not for small, one compute. **Spark is about processing**, not about the storage.

Main advatages are:
- it is fast
- it is easy to use (Python, Scala, R, Java and SQL)
- it is powerful (without anything to do, you can use SQL, DataFrame, MLib, GraphX and SPark Streaming)
- it runs almost everywhere (Kubernetes, DataBricks, Hadoop and mony others)

Example:
```
textFile = sc.textFile("hdfs://...")

# Creates a DataFrame having a single column named "line"
df = textFile.map(lambda r: Row(r)).toDF(["line"])
errors = df.filter(col("line").like("%ERROR%"))
# Counts all the errors
errors.count()
# Counts errors mentioning MySQL
errors.filter(col("line").like("%MySQL%")).count()
# Fetches the MySQL errors as an array of strings
errors.filter(col("line").like("%MySQL%")).collect()
```

## Operators

There are two types of operators:
- Transformation - is lazy, doesn't loade data into the memory 
  - Example: read, limit, select, drop, distict, dropDuplicates (DataFrames are immutable)
  - Cache acts as transformations but it is muttable
- Action - are not lazy, it fires Spark Job
  - Example: show, could
  - Get some statics, display the data

## Parquet format

Parquet is an file format for storing the data. It is characterized by a columnar format. 

| Row format (csv, json) | Columnar format (parquet, orc) |
|-|-|
| c1,c2/n1,2/n2,3 | c1:1,2,3/nc2:2,3,4 |

While CRUD operations work better with row format, for most of **analytical cases columnar structure is better**.

Parquet features:
- Columnar format
- Binary
- Has internal statistics - divides file for 32 records' blocks and keep some statistics for each block

## Performance tuning

There are many ways how to fix the performance of your application.

Two simplest way is to laverage:
- Convert every data to parquet
- Caching 

For some more, check [Spark's guideline](https://spark.apache.org/docs/latest/tuning.html)

# Spark's architecture

Spark's architecture is layered. Like ogrs. Or onions (but who like onion...).

![architecture](https://github.com/akisiel1/introSparkML/blob/master/images/architecture.png)

First layer is Spark Core. This is Distributed Engine.

Second is RDD (Resilient Distributed Datasets) is first, original API. It is slow (especially Python's API) and not optimized. It is not recomented to use it.

Next layer is so called Modern APIs: DataFrame, Dataset, Spark SQL. DataFrames API is most developed api to interact with sql-like table data. Dataframe API and SQL API are optimized equally. This is achieved by the Catalyst - query optimizer, which analyze the query and optimize it.  

The most top layer is the newest: Spark ML, Structured Streaming (not SparkStreaming), GraphFrames (not GraphX). 

## Cluser structure

Spark gets the best results when it runs on a cluster. 

![Spark's cluster architecture](https://github.com/akisiel1/introSparkML/blob/master/images/hdi-spark-architecture.png)

Cluster contains of **the Driver** and **the Workers**.

Each Cluster has _one driver_, which role is:
- Communicates with user (program)
- Plan the execution with Executors

Cluster can contain _0-N number of workers_. 
Worker is an VM.
Their role is a raw computation.

Each worker has it's own **executor**. 
It is a process launched for an application on a worker node, that runs tasks and keeps data in memory or disk storage across them. 
Each application has its own executors.
In DataBricks, one worker has one executor. 

There are two layers of parallelization:
1) how many executors do you have 
2) how many cores/slot have each worker (each core runs one task)


# Spark's Machine Learning explained

Sparks' ML is pretty nicely unified. It has 4 Filars:

1. Transformers
  - To run the transformation, call transform() method
  - Transformer analyses only on one row
  - Examples:
     - MLModel
     - VectorAssembler: `"Col value" => ["col values"]`
     - IndexToString
     - Pipeline

2. Estimator
  - To run the estimation (eg. train the model), call fit() method
  - Analyzes whole DF
  - Examples:
     - ML algorithms
     - StringIndexer `Cat => int`
     - OneHotEncoderEstimator
		
3. Pipeline
  - Is a special type of estimator
  - Consist of multiple stages
  - You can export it (save()) and load back (load()) as well
  - Example: ![pipeline example](https://github.com/akisiel1/introSparkML/blob/master/images/pipeline_example.png)
	
4.  Evaluators  
  - To run the evaluation, call the evaluate() method
  - Most of them are placed in Pyspark.ml.evaluation module
  - Examples:
     - RegressionEvaluator
     - BinaryClassyficationEvaluator
		 - MultiClassClasifficationEvaluator
     - ClusteringEvaluator

# Azure DataBricks set up



# ML excersise

Titanic excersise.

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/akisiel1/introSparkML/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
