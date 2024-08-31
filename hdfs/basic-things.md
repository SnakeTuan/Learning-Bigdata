# Understanding what is hdfs

## What is hdfs:
- HDFS, or Hadoop Distributed File System, is like a big, organized storage system for huge amounts of data. 

- Imagine a library where instead of keeping all the books in one room, the books are spread out across multiple rooms. 
Each room has a specific set of books, but all the rooms together make up the whole library. 
HDFS works in a similar way, but for data.

## How HDFS Works:

### Data is Split into Blocks:

- When you want to store a large file in HDFS, it splits the file into smaller pieces called blocks (usually 128MB or 256MB in size).

- These blocks are then stored across multiple computers (called nodes) in the cluster.

### Data Replication:

- To make sure your data is safe and available, HDFS doesn't just store one copy of each block. It creates multiple copies (depend on your configuration) and stores them on different nodes.

- So, if one node fails, you still have copies of the data on other nodes.

### Master-Slave Architecture:

- HDFS has a NameNode (the master) that keeps track of where all the blocks of your data are stored across the cluster.
The actual data blocks are stored on DataNodes (the slaves).

- When you want to read or write data, the NameNode tells you which DataNodes to interact with.

### Data Access:

- When you want to read a file, HDFS fetches the required blocks from the DataNodes and assembles them so you can access the complete file.

- Even if some DataNodes are down, HDFS can still provide the file from the other copies.

### Fault Tolerance:

- Since data is stored in multiple places, HDFS can handle hardware failures. If a node goes down, HDFS automatically starts using the other copies of the data without you even noticing.

## Why Many Companies Use HDFS?

- Scalability: You can store massive amounts of data by adding more nodes to the cluster.

- Reliability: With multiple copies of your data, the system can recover from failures without data loss.

- High Throughput: HDFS is optimized for reading large files, making it great for big data processing.