### Understanding a Hadoop Cluster


There are three main types of machine involved:

* Client
* Master
* Worker / Slave

Note: Like pretty much every other cluster


## Clients

* Loads data into the system
* Retrieves the data from the system
* Manages the Input / Output of MapReduce


## Master - NameNode

* Tracks files and data
* Does not store data itself
* Single point of failure in Hadoop 1.x


## Master - SecondaryNameNode

* Helps the NameNode perform more computations
* Secondary is _very_ misleading.


## Master (ish) - StandByNameNode

* Introduced in Hadoop 2.0
* Provides failover capabilities
* Makes Hadoop truly high availability
* Cannot be used with SecondaryNameNode


## Master - ResourceManager

* Allocates the cluster resources


## Master - Application Master

* Responsible for the execution of a single application
* Contains application logic
* Framework specific


## Worker - DataNode

* Stores data


## Worker - NodeManager

* Communicates to the ResourceManager
* Describes what the machine can offer