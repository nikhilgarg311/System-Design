### How to design a Distributed Database

## Characteristics
    Durability- If we loose custome data we cant be in database business, so durability is important
    Availability over Performance- Its important that our database return data either slow or fast but it should return
    Strong Consistency
    ACID not folowed

# Assumption: Client will handle security

## Operations

    Create Table
    Put (table, key, value)
    Get (table,key) - value
    List (keys in table is sorted order)
    Delete (table,key)
    Delete Table (optional)
    
# We will use sequencers
    a sequencer is a combination of 
        timestamp(ns) + Unique per node + Unique node id
        8bytes              4Bytes          4Bytes             = 16 Bytes

# Lets perform some operations
    Create Stocks
    Put(Stocks,B,100)
    Put(Stocks,A,120)
    Put(Stocks,C,135)
    

    Stocks Table will look like
    
    Stock | Price | Sequence
    A     | 120   | Seq2
    B     | 100   | Seq1
    C     | 135   | Seq3


    Get(Stocks,B) ----> 100
    List(Stocks)-------> A-120,B-100,C-135

    Now lets suppose a client performs two put operations at same time( assuming we dont have read/write locks)
    Put(Stocks,D,140)
    Put(Stocks,D,160)

    Both the operations will happen, and somewhere stored in DB, but finally when reconcillation happens then we will use that sequencer to find which was the latest write.

## High Level Architecture of Distribute Database System

                                Load Balancer

            Request Manager     Request Manager     Request Manager

Controller                                                                 Metadata Manager

            Replication Group    Replication Group     Replication Group


So the load balancer recieves request and sends it to Request Manager(RM).
RM consults with the metadata manager(MM) because MM stores the info of where a table/data is present.
After consulation with MM, RM sends request to Replication Group
Controller looks after the replication groups and starts distributing data across differnt groups when one table starts overloading.


## Metadata Manager (MM)
    responsible for leader election in replication group
    mapping of table part with replication group eg: stock[A-C] ->repgroup1 , stock[D-L]->repgroup2, stock[M-Z]->repgroup3
    examples: zookeeper, etcd, redis

    we try to keep all the data of MM in RM so that RM does not need to talk to MM again and again.
    since MM stores such crucial data we therefore need to backup it as frequently as possible


## Replication Group (RG)

group of data nodes, where one is the leader.
every request to a RG goes via its leader rest are followers, so it will be consistent.


## Control Plane/Controller

#### Leader management
Whenever a new RG comes into picture, all the nodes withing it try be become the leader, MM choses one, and MM keeps in touch with all nodes using hearbeats so that it knows when the node is down. The moment MM gets to know that leader node is down it appoints other node which was in sync with the leader as new leader

#### Follower node following behind
When a follower node is not able to stay in sync with the leader and is falling behind, controller selects a new node from pool of available nodes and replaces it, updates the metadata manager, and flag the defective node for further inspection.

#### Split hot(getting bigger is size) tables

## Error/Race Conditions
#### Split Brains( Two leaders)
#### No Leader
#### Network split at metadata manager
#### Outdated table metadata
#### Node goes down before updating B+tree or LSM tree
#### Bad Request Manger
#### Bad Replication Group

## watch this for in detail vedeo https://www.youtube.com/watch?v=rnZmdmlR-2M

