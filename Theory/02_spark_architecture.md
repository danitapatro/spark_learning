# Apache Spark Architecture

## Table of Contents
- [Introduction](#introduction)
- [Spark Architecture Overview](#spark-architecture-overview)
- [Spark Components](#spark-components)
  - [Driver](#driver)
  - [Cluster Manager](#cluster-manager)
  - [Executors](#executors)
- [Spark Execution Flow](#spark-execution-flow)
- [Lineage and DAG](#lineage-and-dag)
  - [Lineage](#lineage)
  - [DAG (Directed Acyclic Graph)](#dag-directed-acyclic-graph)
  - [Difference Between Lineage and DAG](#difference-between-lineage-and-dag)
- [Lazy Evaluation](./lazy_evaluation.md)
- [Key Points to Remember](#key-points-to-remember)

---

## Introduction
- Apache Spark is a distributed computing engine designed for **big data processing**. 
- It provides high-level APIs in Java, Scala, Python, and R, and supports both batch and real-time data processing.
- Spark achieves **fault tolerance, scalability, and high performance** by distributing tasks across a cluster.
- Its architecture enables:
  * **Parallel computation**
  * **Efficient memory usage**
  * **Minimal disk I/O**

---

## Spark Architecture Overview:
- At a high level, Spark follows a master–slave architecture:
  * **Driver** → Master process (coordinates execution)
  * **Executors** → Worker processes (perform computation)
  * **Cluster Manager** → Manages resources between Driver & Executors

## Spark Components

### Driver
- The **Driver Program** is the main control process.
- It is responsible for:
  * Converting user code (transformations and actions) into a logical plan.
  * Creating the **DAG (Directed Acyclic Graph)**.
  * Requesting resources from the Cluster Manager.
  * Assigning tasks to Executors and collecting results.
**Analogy:**
- The Driver is like the **Project Manager** who prepares the plan and assigns tasks to the team.

### Cluster Manager
- The Cluster Manager is responsible for resource allocation (CPU, Memory).
**Spark supports different cluster managers:**
  * **Standalone Cluster Manager** (default) — Spark's built-in manager.
  * **YARN (Yet Another Resource Negotiator)** — Hadoop's resource manager.
  * **Mesos** — General-purpose manager.
  * **Kubernetes** — Container-based resource manager.
**Role:**
  * The Driver requests executors from the Cluster Manager.
  * The Cluster Manager provides executors based on available resources.
**Analogy:**
- The Cluster Manager is like the **Restaurant Manager** who assigns the right number of chefs (executors) depending on demand.

### Executors
- Executors are workers processes launched on each node.
**Responsibilities:**
  * Run assigned tasks
  * Store intermediate data in memory/disk
  * Cache data for reuse (RDD/DataFrame caching)
  * Continue running until the application finishes
**Analogy:**
- Executors are like **chefs** in a kitchen who actually cook based on the head chef’s (Driver’s) instructions.

---

## Spark Execution Flow
1. **User Code Submission** — code written using Spark APIs.
2. **Driver** — builds **Logical Plan → Physical Plan → DAG**.
3. **Cluster Manager** — allocates **executors**.
4. **Executors** — execute tasks in **parallel**.
5. **Driver** — collects results and returns them to the user.

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

## Key Points to Remember:
 * Spark uses a **Driver → Cluster Manager → Executors model**
 * **Driver**: Converts user code into a DAG & schedules tasks
 * **Cluster Manager**: Allocates resources (CPU, Memory)
 * Executors: Run tasks & store/cache data
 * Execution is **lazy** until an **Action** is called
 * Spark uses **lineage + DAG scheduling** for fault tolerance & optimization
