# 02 — Apache Spark Architecture
 
> 📚 **Series:** PySpark for Data Engineering | **Topic:** 2
> ✍️ **Level:** Beginner | **Read time:** 10 minutes
 
---
 
## Table of Contents
 
1. [Introduction](#introduction)
2. [Spark Architecture Overview](#spark-architecture-overview)
3. [Spark Components](#spark-components)
   - [Driver](#driver)
   - [Cluster Manager](#cluster-manager)
   - [Executors](#executors)
4. [Spark Execution Flow](#spark-execution-flow)
5. [Lineage and DAG](#lineage-and-dag)
   - [Lineage](#lineage)
   - [DAG (Directed Acyclic Graph)](#dag-directed-acyclic-graph)
   - [Difference Between Lineage and DAG](#difference-between-lineage-and-dag)
6. [Key Points to Remember](#key-points-to-remember)
7. [Interview Questions](#interview-questions)
---
 
## Introduction
 
- Apache Spark is a distributed computing engine designed for **big data processing**.
- It provides high-level APIs in Java, Scala, Python, and R, and supports both batch and real-time data processing.
- Spark achieves **fault tolerance, scalability, and high performance** by distributing tasks across a cluster.
- Its architecture enables:
  - **Parallel computation** — multiple tasks run simultaneously across nodes
  - **Efficient memory usage** — data stays in RAM instead of disk
  - **Minimal disk I/O** — intermediate results are not written to disk
---
 
## Spark Architecture Overview
 
At a high level, Spark follows a **Master–Slave architecture**:
 
```
┌─────────────────────────────────────────────────────┐
│                   SPARK APPLICATION                  │
│                                                      │
│   ┌──────────────┐       ┌─────────────────────┐    │
│   │    DRIVER    │◄─────►│   CLUSTER MANAGER   │    │
│   │  (Master)    │       │  (Resource Allocator)│    │
│   └──────┬───────┘       └─────────────────────┘    │
│          │                                           │
│    ┌─────┼─────┐                                     │
│    ▼     ▼     ▼                                     │
│  [Exec] [Exec] [Exec]   ← Worker Nodes               │
│   (Slave processes)                                  │
└─────────────────────────────────────────────────────┘
```
 
| Component | Role |
|-----------|------|
| **Driver** | Master process — coordinates everything |
| **Cluster Manager** | Allocates CPU and memory resources |
| **Executors** | Worker processes — do the actual computation |
 
---
 
## Spark Components
 
### Driver
 
The **Driver Program** is the brain of every Spark application.
 
**Responsibilities:**
- Converts user code (transformations and actions) into a logical plan
- Creates the **DAG (Directed Acyclic Graph)**
- Requests resources from the Cluster Manager
- Assigns tasks to Executors and collects results
**Key objects inside the Driver:**
- `SparkContext` — original entry point (Spark 1.x)
- `SparkSession` — modern unified entry point (Spark 2.x+)
> 💡 **Analogy:** The Driver is like a **Project Manager** who prepares the full plan and assigns tasks to the team — but does not do the actual work themselves.
 
---
 
### Cluster Manager
 
The Cluster Manager handles **resource allocation** (CPU, Memory) across the cluster.
 
**Spark supports four cluster managers:**
 
| Cluster Manager | Description |
|----------------|-------------|
| **Standalone** | Spark's own built-in manager — simplest to set up |
| **YARN** | Hadoop's resource manager — most common in enterprise |
| **Mesos** | General-purpose cluster manager |
| **Kubernetes** | Container-based resource manager — growing in popularity |
 
**How it works:**
1. Driver requests executors from the Cluster Manager
2. Cluster Manager checks available resources
3. Cluster Manager launches executors on worker nodes
> 💡 **Analogy:** The Cluster Manager is like a **Restaurant Manager** who decides how many chefs (executors) to assign based on how many orders (tasks) are coming in.
 
---
 
### Executors
 
Executors are **worker processes** launched on each node in the cluster.
 
**Responsibilities:**
- Run the tasks assigned by the Driver
- Store intermediate data in memory or disk
- Cache data for reuse (RDD/DataFrame caching)
- Send results back to the Driver
- Keep running for the entire lifetime of the application
> 💡 **Analogy:** Executors are like **chefs in a kitchen** — they receive instructions from the head chef (Driver) and actually do the cooking (computation).
 
---
 
## Spark Execution Flow
 
Here is exactly what happens step by step when you run a Spark job:
 
```
Step 1 → User submits Spark code
Step 2 → Driver converts code into Logical Plan
Step 3 → Driver optimizes into Physical Plan + DAG
Step 4 → Driver requests executors from Cluster Manager
Step 5 → Cluster Manager allocates resources and launches Executors
Step 6 → Driver sends Tasks to Executors
Step 7 → Executors run Tasks in parallel
Step 8 → Results returned to Driver
Step 9 → Driver returns final output to user
```
 
**In code — what triggers this entire flow:**
 
```python
from pyspark.sql import SparkSession
 
spark = SparkSession.builder.appName("ArchitectureDemo").getOrCreate()
 
# These are TRANSFORMATIONS — nothing executes yet (lazy evaluation)
df = spark.read.csv("sales_data.csv", header=True)
df_filtered = df.filter(df["region"] == "India")
df_grouped = df_filtered.groupBy("product").sum("revenue")
 
# This ACTION triggers the entire execution flow above
df_grouped.show()  # ← DAG is built and tasks are sent to executors HERE
```
 
> 💡 **Key insight:** Spark does nothing until an **action** is called. All transformations before it are just building a plan. This is called **Lazy Evaluation**.
 
---
 
## Lineage and DAG
 
### Lineage
 
**Lineage** is the complete record of all transformations applied to create an RDD or DataFrame.
 
- It is Spark's primary mechanism for **fault tolerance**
- If any partition of data is lost, Spark uses the lineage to **recompute only that lost partition** from scratch
- No need to replicate data like traditional systems
**Example:**
 
```python
rdd1 = sc.textFile("data.txt")           # Step 1 — Read file
rdd2 = rdd1.map(lambda x: x.split(",")) # Step 2 — Split each line
rdd3 = rdd2.filter(lambda x: x[1] == "NY") # Step 3 — Filter by city
 
# Lineage of rdd3:
# data.txt → map(split) → filter(NY) → rdd3
# If rdd3's partition 2 is lost → Spark recomputes from data.txt automatically
```
 
---
 
### DAG (Directed Acyclic Graph)
 
**DAG** is a visual and logical representation of the computation plan Spark creates before executing.
 
- **Directed** — each step flows in one direction (no going back)
- **Acyclic** — no circular loops
- **Graph** — a connected set of nodes (operations) and edges (data flow)
```
textFile("data.txt")
       ↓
   map(split)
       ↓
  filter(NY)
       ↓
  groupByKey()
       ↓
    count()      ← Action triggers DAG execution
```
 
The **DAG Scheduler** inside the Driver converts this graph into **Stages** and then into **Tasks** that are sent to Executors.
 
---
 
### Difference Between Lineage and DAG
 
| Feature | Lineage | DAG |
|---------|---------|-----|
| **Purpose** | Fault recovery | Execution planning |
| **Scope** | Per RDD/partition | Entire job |
| **Used by** | Spark runtime for recovery | DAG Scheduler for optimization |
| **Visualized in** | Not directly visible | Spark UI → Stages tab |
 
> 💡 **Simple way to remember:** Lineage is the *history* of how data was made. DAG is the *plan* of how to execute the job.
 
---
 
## Key Points to Remember
 
- Spark uses a **Driver → Cluster Manager → Executors** model
- **Driver** — converts user code into a DAG and schedules tasks
- **Cluster Manager** — allocates resources (CPU, Memory)
- **Executors** — run tasks and store/cache data
- Execution is **lazy** — nothing runs until an **action** is called
- Spark uses **lineage + DAG scheduling** for fault tolerance and optimization
- The Driver communicates with Cluster Manager to get Executors, then communicates directly with Executors for task execution
---
 
## Interview Questions
 
**Q1: Explain the Spark architecture and the role of each component.**
 
> Spark follows a master-slave architecture with three main components. The Driver is the master process that converts user code into a DAG, schedules tasks, and collects results. The Cluster Manager allocates CPU and memory resources across the cluster — Spark supports Standalone, YARN, Mesos, and Kubernetes. Executors are worker processes on each node that run the actual tasks and return results to the Driver.
 
---
 
**Q2: What is the difference between Lineage and DAG in Spark?**
 
> Lineage is the record of all transformations applied to create an RDD, used for fault recovery — if a partition is lost, Spark recomputes it using the lineage chain. DAG (Directed Acyclic Graph) is the execution plan created by the Driver that represents all operations as a graph of stages and tasks. Lineage is about recovery while DAG is about execution planning.
 
---
 
**Q3: What happens when a Spark executor fails mid-job?**
 
> When an executor fails, the Driver detects the failure. It uses the RDD lineage to identify which partitions were lost and reschedules those specific tasks on other available executors. The entire job does not need to restart — only the lost partitions are recomputed. This makes Spark fault tolerant without needing data replication.
 
---
 
**Q4: What cluster managers does Spark support and which is most common in production?**
 
> Spark supports four cluster managers: Standalone (built-in, good for development), YARN (Hadoop's resource manager, most common in enterprise environments), Mesos (general-purpose), and Kubernetes (container-based, growing rapidly). In production, YARN is the most widely used, but Kubernetes adoption is increasing significantly.
 
---
 
## ⏭️ Next Topic
 
**[03 — SparkContext vs SparkSession →](./03_sparksession_vs_sparkcontext.md)**
 
---
 
*Found this helpful? Give the repo a ⭐ and share it with someone learning data engineering!*
 
---
> ✍️ Part of my Data Engineering learning journey | [GitHub](https://github.com/danitapatro/spark_learning)
