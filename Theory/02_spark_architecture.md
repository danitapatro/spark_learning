Apache Spark Architecture
Table of Contents

Introduction

Spark Architecture Overview

Driver

Cluster Manager

Executors

Spark Execution Flow

Example Walkthrough

Key Points to Remember

Introduction

Apache Spark is a distributed data processing framework designed to handle large-scale data across multiple machines.
Its architecture enables:

Parallel computation

Efficient memory usage

Minimal disk I/O

Spark Architecture Overview

At a high level, Spark follows a master–slave architecture:

Driver → Master process (coordinates execution)

Executors → Worker processes (perform computation)

Cluster Manager → Manages resources between Driver & Executors

Driver

The Driver Program runs the main application.

Responsibilities:

Converts user code (RDD/DataFrame/SQL) into a logical plan

Optimizes it into a physical plan (using Catalyst Optimizer for SQL/DataFrames)

Divides jobs into stages and tasks

Schedules tasks on Executors and monitors progress

Analogy: The Driver is like the Project Manager who prepares the plan and assigns tasks to the team.

Cluster Manager

The Cluster Manager is responsible for resource allocation (CPU, Memory).

Types of Cluster Managers:

Standalone → Spark’s built-in manager

YARN → Hadoop’s resource manager

Mesos → General-purpose manager

Kubernetes → Container-based resource manager

Role:

The Driver requests executors from the Cluster Manager

The Cluster Manager provides executors based on available resources

Analogy: The Cluster Manager is like the Restaurant Manager who assigns the right number of chefs (executors) depending on demand.

Executors

Executors are worker processes launched on cluster nodes.

Responsibilities:

Run assigned tasks

Store intermediate data in memory/disk

Cache data for reuse (RDD/DataFrame caching)

Continue running until the application finishes

Analogy: Executors are like chefs in a kitchen who actually cook based on the head chef’s (Driver’s) instructions.

Spark Execution Flow

Step-by-step process of Spark execution:

User Code Submission → Code written using Spark APIs

Driver → Builds Logical Plan → Physical Plan → DAG

Cluster Manager → Allocates Executors

Executors → Execute tasks in parallel

Driver → Collects results and returns them to the user

Example Walkthrough
# Load data
rdd = sc.textFile("data.txt")

# Transformations (lazy)
words = rdd.flatMap(lambda line: line.split(" "))
filtered = words.filter(lambda w: len(w) > 3)

# More transformation
counts = filtered.map(lambda w: (w, 1)).reduceByKey(lambda a, b: a + b)

# Action (triggers execution)
counts.collect()


Step-by-step:

textFile → Loads data into RDD

flatMap, filter, map → Transformations (lazy, recorded as lineage)

reduceByKey → Groups data, creating a DAG of stages

collect() → Action triggers execution → Tasks sent to Executors

Executors compute results and return them to Driver

Key Points to Remember

Spark uses a Driver → Cluster Manager → Executors model

Driver: Converts user code into a DAG & schedules tasks

Cluster Manager: Allocates resources (CPU, Memory)

Executors: Run tasks & store/cache data

Execution is lazy until an Action is called

Spark uses lineage + DAG scheduling for fault tolerance & optimization
