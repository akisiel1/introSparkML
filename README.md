# Introduction to Spark ML

Here you can find the lightweight introduction to Spark ML.

## Agenda

1. [What is Spark and what it's relationship with DataBricks?](#what-is-spark)
2. [A small overview of Spark's architecture.](#sparks-architecture)
3. [Let's play: set up the Azure DataBricks and create your first cluster.](#azure-databricks-set-up)
4. [Excersise with Aga: simple Machine Learning's excersise.](#ml-excersise)

# What is Spark?

Apache Spark is an analitics engine, created to large-scale data processing. 

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

# Spark's architecture



# Azure DataBricks set up

# ML excersise

Titanic excersise.

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/akisiel1/introSparkML/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
