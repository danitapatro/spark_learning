# 01 — Apache Spark Introduction
 
> 📚 **Series:** PySpark for Data Engineering | **Topic:** 1
> ✍️ **Level:** Beginner | **Read time:** 7 minutes
 
---
 
## Table of Contents
 
1. [Apache Spark](#apache-spark)
2. [Core Processing Capabilities](#core-processing-capabilities)
3. [Key Features and Advantages](#key-features-and-advantages)
4. [Spark Ecosystem (Built-in Libraries)](#spark-ecosystem-built-in-libraries)
5. [Real-World Use Cases](#real-world-use-cases)
6. [Advantages Over Hadoop MapReduce](#advantages-over-hadoop-mapreduce)
7. [Quick Code Example](#quick-code-example)
8. [Interview Questions](#interview-questions)
---
 
## Apache Spark
 
- Apache Spark is an open-source, distributed computing engine designed to process large-scale data quickly and efficiently.
- It provides a unified analytics engine with built-in modules for diverse data processing tasks.
---
 
## Core Processing Capabilities
 
| Capability | Description |
|-----------|-------------|
| **Batch Processing** | Analyze large volumes of historical data |
| **Real-time Streaming** | Ingest and process live data streams for instant insights |
| **Spark SQL** | Analyze structured and semi-structured data using familiar SQL syntax |
| **Machine Learning (MLlib)** | Build, train, and scale machine learning models |
| **Graph Processing (GraphX)** | Process graph-structured data such as social networks, routing, or dependencies |
 
---
 
## Key Features and Advantages
 
- **In-Memory Processing** — Faster computations compared to disk-based systems like Hadoop MapReduce
- **Distributed Computing** — Scales horizontally across clusters to handle petabytes of data
- **Unified Platform** — One framework for batch, streaming, SQL, ML, and graph processing
- **Multiple Language Support** — APIs available in Java, Scala, Python (PySpark), and R
- **Fault Tolerance** — Uses Resilient Distributed Datasets (RDDs) with lineage information to recover lost data
---
 
## Spark Ecosystem (Built-in Libraries)
 
```
Apache Spark
│
├── Spark SQL       → Query structured data with SQL & DataFrames
├── Spark Streaming → Process real-time streams (Kafka, Flume, etc.)
├── MLlib           → Machine learning algorithms & utilities
└── GraphX          → Graph processing & analytics
```
 
---
 
## Real-World Use Cases
 
- **Fraud Detection** — Banking & fintech detect anomalies in transactions
- **Personalized Recommendations** — E-commerce, OTT platforms suggest relevant products/content
- **Real-Time Analytics** — IoT devices, log monitoring, and clickstream analysis
- **ETL Pipelines** — Large-scale data cleaning, transformation, and loading into warehouses
---
 
## Advantages Over Hadoop MapReduce
 
| Feature | Hadoop MapReduce | Apache Spark |
|---------|-----------------|--------------|
| Data processing | Disk-based (slow) | In-memory (up to 100x faster) |
| API richness | Low-level only | RDD, DataFrame, Dataset |
| Workload support | Batch only | Batch + Streaming + ML + Graph |
| Ease of use | Complex Java code | Simple Python/Scala APIs |
| Fault tolerance | Yes | Yes (via RDD lineage) |
 
- Stores intermediate data in memory (RAM) instead of writing to disk → much faster
- Provides a richer API (RDD, DataFrame, Dataset) compared to MapReduce's low-level APIs
- Supports multiple workloads (batch, streaming, ML, graph) in a single framework
---
 
## Quick Code Example
 
Here is the simplest PySpark program to see Spark in action:
 
```python
from pyspark.sql import SparkSession
 
# Step 1 — Create a Spark session (entry point to Spark)
spark = SparkSession.builder \
    .appName("MyFirstSparkApp") \
    .getOrCreate()
 
# Step 2 — Create a simple DataFrame
data = [("Alice", 30), ("Bob", 25), ("Charlie", 35)]
columns = ["Name", "Age"]
 
df = spark.createDataFrame(data, columns)
 
# Step 3 — Show the data
df.show()
 
# Output:
# +-------+---+
# |   Name|Age|
# +-------+---+
# |  Alice| 30|
# |    Bob| 25|
# |Charlie| 35|
# +-------+---+
```
 
> 💡 **Note:** `SparkSession` is your entry point to everything in Spark. Always start here.
 
---
 
## Interview Questions
 
**Q1: What is Apache Spark and why is it preferred over Hadoop MapReduce?**
 
> Apache Spark is an open-source distributed computing framework for processing large-scale data across a cluster of machines. It is preferred over Hadoop MapReduce because it processes data in-memory rather than writing intermediate results to disk, making it up to 100x faster. It also supports multiple workloads — batch, streaming, ML, and graph — in a single unified framework, whereas MapReduce only supports batch processing.
 
---
 
**Q2: What are RDDs and why are they important for fault tolerance?**
 
> RDD (Resilient Distributed Dataset) is the fundamental data structure of Spark. It is an immutable distributed collection of objects. RDDs maintain lineage information — a record of how each partition was derived from its parent. If any partition is lost due to node failure, Spark can recompute it using the lineage graph, making the system fault tolerant without needing to replicate data.
 
---
 
**Q3: Name the components of the Spark ecosystem.**
 
> The Spark ecosystem includes Spark Core (base engine), Spark SQL (structured data querying), Spark Streaming (real-time data processing), MLlib (machine learning), and GraphX (graph processing).
 
---
 
## ⏭️ Next Topic
 
**[02 — Spark Architecture: Driver, Executors and Cluster Manager →](./02_spark_architecture.md)**
 
---
 
*Found this helpful? Give the repo a ⭐ and share it with someone learning data engineering!*
 
---
> ✍️ Part of my Data Engineering learning journey | [GitHub](https://github.com/danitapatro/spark_learning)
