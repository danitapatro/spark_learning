# Apache Spark Architecture

## Table of Contents
- [Introduction](#introduction)
- [Spark Components](#spark-components)
  - [Driver](#driver)
  - [Cluster Manager](#cluster-manager)
  - [Executors](#executors)
- [Execution Flow](#execution-flow)
- [Lineage and DAG](#lineage-and-dag)
  - [Lineage](#lineage)
  - [DAG (Directed Acyclic Graph)](#dag-directed-acyclic-graph)
  - [Difference Between Lineage and DAG](#difference-between-lineage-and-dag)
- [Lazy Evaluation](#lazy-evaluation)
- [Conclusion](#conclusion)

---

## Introduction
Apache Spark is a distributed computing engine designed for **big data processing**. It provides high-level APIs in Java, Scala, Python, and R, and supports both batch and real-time data processing. Spark achieves **fault tolerance, scalability, and high performance** by distributing tasks across a cluster.

---

## Spark Components

### Driver
- The **Driver Program** is the main control process.
- It is responsible for:
  * Converting user code (transformations and actions) into a logical plan.
  * Creating the **DAG (Directed Acyclic Graph)**.
  * Requesting resources from the Cluster Manager.
  * Assigning tasks to Executors and collecting results.

### Cluster Manager
- Manages resources across the cluster.
- Spark supports different cluster managers:
  * **Standalone Cluster Manager** (default).
  * **YARN (Yet Another Resource Negotiator)**.
  * **Mesos**.
  * **Kubernetes**.

### Executors
- Worker processes launched on each node.
- Responsibilities:
  * Execute tasks assigned by the driver.
  * Store data in memory or disk (for caching and shuffling).
  * Return results to the driver.

---

## Execution Flow
1. User code is submitted to the **Driver**.
2. Driver builds a **logical execution plan**.
3. The plan is converted into a **DAG** of stages.
4. Tasks are distributed to **Executors** by the Cluster Manager.
5. Executors process tasks in parallel and return results to the Driver.

---

## Lineage and DAG

### Lineage
- Represents the sequence of transformations (like `map`, `filter`, `reduceByKey`) that created an RDD.
- Provides **fault tolerance**:
  * If a partition of data is lost, Spark can recompute it from its lineage.
- Example:  
  ```python
  rdd1 = sc.textFile("data.txt")
  rdd2 = rdd1.map(lambda x: x.split(","))
  rdd3 = rdd2.filter(lambda x: x[1] == "NY")
