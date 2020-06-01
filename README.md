# Introduction to Spark ML

Here you can find the lightweight introduction to Spark ML.

## Agenda

1. [What is Spark and what it's relationship with DataBricks?](#what-is-spark)
2. [A small overview of Spark's architecture.](#sparks-architecture)
3. [Spark's Machine Learning explained](#sparks-machine-learning-explained)
4. [Let's play: set up the Azure DataBricks and create your first cluster.](#azure-setup)
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

## DataBricks

The easiest way to enjoy Spark in Azure world is to use the [DataBricks](https://docs.databricks.com/index.html#). DataBricks is an Unified Data Analytics Platform, which consist of a number of usefull libraries and tools for data engineering and data science. Spark is just one of the tools, others are: Data Lake, MLFlow, TensorFlow, PyTorch and many others. 

![DataBricks](images/Marketecture.png)

## Performance tuning

There are many ways how to fix the performance of your application.

Two simplest way is to laverage:
- Convert every data to parquet
- Caching 

For some more, check [Spark's guideline](https://spark.apache.org/docs/latest/tuning.html)

# Spark's architecture

Spark's architecture is layered. Like ogrs. Or onions (but who likes onion...).

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
  - Example: 
![pipeline example](https://github.com/akisiel1/introSparkML/blob/master/images/pipeline_example.png)
	
4.  Evaluators  
  - To run the evaluation, call the evaluate() method
  - Most of them are placed in Pyspark.ml.evaluation module
  - Examples:
     - RegressionEvaluator
     - BinaryClassyficationEvaluator
     - MultiClassClasifficationEvaluator
     - ClusteringEvaluator

# Azure setup

## Azure DataBricks setup 

Before we will start with our ML excersise, we need to set up environments for it. Follow thos steps:

0. Create a resource group where you will keep all excersise-related services.
1. Create a DataBricks service within your VS subscription:
![](https://github.com/akisiel1/introSparkML/blob/master/images/DataBricks_setup/Annotation%202020-05-31%20093437.png)
![](https://github.com/akisiel1/introSparkML/blob/master/images/DataBricks_setup/Annotation%202020-05-31%20093600.png)
2. After resource will be deployed, launch the Workspace:
![](https://github.com/akisiel1/introSparkML/blob/master/images/DataBricks_setup/Annotation%202020-05-31%20094847.png)
3. To use the DataBricks you need to create a cluster. First, go to the Clusters:
![](https://github.com/akisiel1/introSparkML/blob/master/images/DataBricks_setup/Annotation%202020-05-31%20095007.png)
and click "Create Cluster":
![](https://github.com/akisiel1/introSparkML/blob/master/images/DataBricks_setup/Annotation%202020-05-31%20095034.png)
4. Set up the cluster with following settings:
![](https://github.com/akisiel1/introSparkML/blob/master/images/DataBricks_setup/Annotation%202020-05-31%20095349.png)

Creation will take some longer time.

## Azure Storage setup

Another service needed for te excersise is Azure Storage, where we will keep the data.

1. In your resource group, add Azure Storage service:
![](https://github.com/akisiel1/introSparkML/blob/master/images/DataBricks_setup/Annotation%202020-05-31%20101103.png)
![](https://github.com/akisiel1/introSparkML/blob/master/images/DataBricks_setup/Annotation%202020-05-31%20101145.png)
2. Create a Blob Container where you will keep your data:
![](https://github.com/akisiel1/introSparkML/blob/master/images/DataBricks_setup/Annotation%202020-05-31%20110813.png)
![](https://github.com/akisiel1/introSparkML/blob/master/images/DataBricks_setup/Annotation%202020-05-31%20110939.png)
3. Download data from [Kaggle](https://www.kaggle.com/c/titanic/data) and upload it into your container.

# ML excersise

Our excersise will be solve known ML problem: predict who will survive the Titanic catastrophy. More about the problem you can found on [Kaggle](https://www.kaggle.com/c/titanic/overview).

What we will do?

- Load the data.
- Handle the null values: mean, average.
- Combine columns: FamilyMembers, IsAlone, FarePerPerson.
- OneHotEncoding for categorical features.
- Check the correlation between features and label. 
- Use Logistic regression to learn the model.
- Evaluate the model.
- Apply pipeline to control learning.

#### Load the data

There are few of ways to load the data from Azure Blob. Simplest one is to connect the blob container:
```
spark.conf.set(
  "fs.azure.sas.<container-name>.<storage-account-name>.blob.core.windows.net",
  "<complete-query-string-of-sas-for-the-container>")
```
and then read file `val df = spark.read.csv("wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/<file-name>")`

Other is to mount the container as a disk:

```
dbutils.fs.mount(
  source = "wasbs://<container-name>@<storage-account-name>.blob.core.windows.net",
  mount_point = "/mnt/titanic_data",
  extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})
```
where:
- <mount-name> is a DBFS path representing where the Blob storage container or a folder inside the container (specified in source) will be mounted in DBFS.
- <conf-key> can be either fs.azure.account.key.<storage-account-name>.blob.core.windows.net or fs.azure.sas.<container-name>.<storage-account-name>.blob.core.windows.net
- dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>") gets the key that has been stored as a secret in a secret scope.
	
Then you can read file: `test_df = spark.read.csv("/mnt/<mount-name>/test")`

#### Check the relevance of features


