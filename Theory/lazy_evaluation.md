## Table of Contents
1. [Lazy Evaluation](#lazy-evaluation)
2. [How It Works](#how-it-works)
3. [Lineage vs DAG](#lineage-vs-dag)
4. [Example in PySpark](#example-in-pyspark)
5. [Key Points](#key-points)
6. [Analogy](#analogy)

## Lazy Evaluation

**Lazy evaluation** is a key feature of Apache Spark that **delays the execution of transformations until an action is called**.  
This allows Spark to **optimize computations, improve performance, and efficiently manage resources**.

---

### How It Works

1. **Transformations are lazy**  
   - Operations like `map()`, `filter()`, `flatMap()`, `union()` **do not execute immediately**.  
   - Spark **records the sequence of transformations** for each RDD or DataFrame. This is called the **lineage**.

2. **Actions trigger execution**  
   - Operations like `collect()`, `count()`, `take()`, `saveAsTextFile()` **trigger Spark to execute all pending transformations**.  
   - Spark **builds a Directed Acyclic Graph (DAG)** from the lineage and executes it in **parallel across the cluster**.

---

### Lineage vs DAG

| Concept   | Meaning in Spark | Analogy |
|-----------|-----------------|---------|
| **Lineage** | Record of all transformations applied to an RDD/DataFrame. Enables **fault tolerance**. | The **recipe**: all steps needed to make a dish |
| **DAG**     | Optimized execution plan Spark builds from lineage when an action is called. Determines **stages, tasks, and parallel execution**. | The **cooking plan**: assigns steps to multiple chefs (executors) to finish faster |

**Key Difference:**  
- **Lineage = recorded steps (what to do)**  
- **DAG = optimized plan (how to do it in parallel efficiently)**  

---

### Example in PySpark

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("LazyExample").getOrCreate()
data = [1, 2, 3, 4, 5]

rdd = spark.sparkContext.parallelize(data)

# Transformation (lazy)
rdd2 = rdd.map(lambda x: x * 2)  # Not executed yet
rdd3 = rdd2.filter(lambda x: x > 5)  # Still lazy

# Action (triggers execution)
print(rdd3.collect())  # Executes all transformations in parallel
