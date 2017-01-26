# [Resilient Distributed Datasets](http://www-bcf.usc.edu/~minlanyu/teach/csci599-fall12/papers/nsdi_spark.pdf)
A distributed memory abstraction for performing in memory computations, on a large cluster
- uses in memory operations, improving performace by orders of magnitude
- handles iterative algorithms better and provides interactive data mining
- RDDs provide a restricted form of shared memory (like RAM, for efficient transfer of data between processes)
- RDDs let scientists leverage distributed memory, useful for retaining data during intermediate operation
  - very important that the datasets provide reuse of data for iterative machine learning and graph algorithms ([PageRank](./PageRank.md), K-mean clustering, logistical regression)
  - no needing to setup an external storage system (which would need lots of disk writes, data serialization and data replication)
- RDDs provide a general abstraction for this, not specific to any particular algorithm, allowing for efficient data persistance 
- main challenge in designing RDDs is defining a storage interface for efficient fault tolerence
  - fault tolerence is always a space trade off (and making data-intensive workflows more expensive)
  - implemented with data replication or logging linage across servers
  - the RDD must be able to recompute lost partitions, derived from other RDDs
- Spark has an implementation of RDDs, providing a programmatic interface for querying large datasets

### RDD Abstraction
An RDD is a read-only partitioned collection of records
- only created using deterministic operations (transformations) on data in stable storage or on data in other RDDs
  - map, filter, join
- RDDs don't have to be materialized (for ex. in memory) at all times, as long as they can be deterministicly recomputed from other datasets, to recompute its partitions
- users have fine-grained control over how an RDD is persisted and partitioned (records stored in different parts of memory or different computers entirely)
  - for ex. data being joined from two different RDD's can have elements grouped together by key for hash-partitioning

### Spark Programming Interface
Spark allows programmers to create RDD objects from stable storage, then apply methods on the objects (transformations, returning another RDD, or actions, returning a value)
- Like DryadLINQ (API that it's inspired by), Spark computes RDDs lazily, waiting until it's used in an action so that it can pipeline transformations (apply transformations in parallel with stages)
- persist can be called on RDD objects to have them persist in RAM (and disk as needed)
  - priority can be specified for determining spillover into disk