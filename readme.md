## Cloud Computing Concept
### Week 1
#### Mapreduce
Map take all separate line as input. After the map function finish, assign tasks to reduce server according to 
```
hash(map_task_id) % n_reduce_server
```
Each reduce calculation take a set of inputs, and might change information.
### Week 2
#### Gossip protocol
Each node send received messages to a number of other random node. Those nodes that already got messages are called infected node, while those not are called uninfected node.

Push means send out, pull means ask in.
#### Analysis
Suppose (n+1) nodes in the system. x means uninfected, y means infected. So x+y=n+1. Each round only a fraction of node will contact, says Beta (Actually it is b/n, b is the number each round a node send out). 
This is a continuos process, the 'turing into infected rate' is 
```
dx/dt = -Beta * x * y
``` 

Push take O(logn). After almost half of the nodes infected, pull becomes faster. Pull take O(loglogn) in the later half.
When consider topology structure, for example, a switch connect to two sub-network. Suppose there are n/2 nodes in a sub-network. The switch load is O(n) because each node send out with prob(b/n). To reduce the load, modify the send out probability that it has prob(1-1/n) to select nodes within the same sub-network.

Summary : 
1. Reliable
2. Fault tolerance
3. Scalable
4. Topology-aware
5. Fast

#### Membership Protocol
Failure detector. Once a process crash, we should detect it.
Consider some factors:
1. Completeness : each failure is detected
2. Accuracy : there is no mistaken detection
3. Speed : time till first detection of failure
4. Scale : equal load on each node (no bottleneck)

We can't get both. But we can gurantee completeness, and Partial/Probabilistic gurantee accuracy.

#### Centralized Hearbeating
Each process i send heart beat to one process j periodically. If nor received within a time, mark as fail.

#### All-to-all heartbeating
Every node send to every node. Gurantee completeness.

#### Gossip Style Heartbeating System
Each node send their member list to other node in gossip way. Once recieved, update local list according to the received list.Check the counter and decide to update or not. If update, change the local time to a new round.
If haven't get the heartbeat within a waiting time, say T_fail, wait another more T_clean to delete the process from list. Because other might have the process. If we delete immediately, we might treat it as a new process and add it back. This might be a ping pong between nodes.

#### SWIM (Scalable Weak-consistent Infection-style Membership)
Reference : https://www.brianstorti.com/swim/

#### Grid Applications
Compare OpenStack and Globus.

### Week 3
#### Peer-to-Peer system
##### All are DHT, distributed hash table.
1. Napster : 
    1. A centralized server groups, many peers. 
    2. Once a peer want to find a file, send the request to the server, the server will return a list of tuple (ip_addre, port) to indicate where can find the file. 

2. Gnutella : 
    1. Eliminate the server. TTL (Time To Live) prevent from the message circle around in the network. 
    2. When the RequestHit node is behind a firewall, althuogh the requester and responder has TCP connection, requestor can't connect to the responder through HTTP GET. Thus can utilize the overlap link, send a push request to transfer the file using the overlap path in the network. 
Ping, Pong messages to update the neighbor peer list.

3. Chord : Finger table, successor node. O(logN) for searching and insert, query.

4. Pastry : 
    1. Nodes have id, like Chord using consistant hashing. 
    2. Routing table based on prefix matching. 
    3. Early hops are short, later are long, because shorter prefix has closer distance (more candidate, good locality).  

5. Kelips :
    1. Affinity group, N^1/2 node in a group, each member of the group maintain tuples of (file, who has) pointers. 
    2. log(1) look up time. Memory cost log(N^1/2). Fits in today's laptop and work station. 
    3. Gossip membership.
    4. Time out for file metedata. Need to refresh periodically (heart beat).

##### Let's focus on Chord : 

Failure Tolerance : 
1. maintain 2log(N) successors suffices to maintain correctness with high probability.
2. Duplicate file on predecessor also, this also take care of load balance.

Stabilization protocol : When new node join, periodically talk to neighbor to update other nodes finger table.
Churn : Node constantly join, leave and fail. log(N) hops for looking up.

##### Trade Off
1. Memory cost
2. Look up cost
3. Background bandwidth (refresh look up table)

### MP1 Notes
#### C++
##### Copy Constructor:
A copy constructor is a member function which initializes an object using another object of the same class. A copy constructor has the following general function prototype:
`ClassName (const ClassName &old_obj);`

##### Constructor Overloading
We can have more than one constructor, each has different argument.

##### Constructot Initialization
`Member(): inited(false), inGroup(false), bFailed(false), nnb(0), heartbeat(0), pingCounter(0), timeOutCounter(0) {}`

##### Operator Overloading

##### Virtual Descructor
Base class's descructor is like this:
`virtual ~Base() {}`
If we have a derived class, and use a base pointer point to derived, like `Base *b = new Derived();` Once we delete b, it will get undefined behavior if the base class's destructor is not virtual.

##### enum
Usage :
`
enum Days {Saturday,Sunday,Tuesday,Wednesday,Thursday,Friday};
Days day = Saturday;
if(day == Saturday)
`

### Some Reference
#### SQL at Scale
https://towardsdatascience.com/sql-at-scale-with-apache-spark-sql-and-dataframes-concepts-architecture-and-examples-c567853a702f

### Week 4
#### Intro
1. Key-Value store
Like Distributed hash table.  Example :
    1. Tweeter ID -> info about tweet
2. NoSQL
    1. Not Only SQL. 
    2. Column-oriented sotre : Fast, don't need to fetch the whole table.

**Why need this?**
1. Speed
2. Avoid single point of failure
3. Low total cost of operation
4. Scale out

#### Cassandra
When data size is large, way more faster than MySQL.
##### Data placement strategies
1. Simple strategy
    1. Random partitioner 
    2. Byte ordered partitioner : Good for range search
2. Network Topology strategy

##### Write
1. Always writable (Hinted Handoff mechanism)
    1. If any repliais down, the coordinator write to other replicas 
    2. If all down, the coordinator buffer the write
2. Eventually consistency (Weak consistency)

##### Delete
Don't delete immediately. Add a tombstone into log.  
Eventually, when compaction encointers a tombstone, it delete the item.

##### Bloom filter
A Bloom filter is a space-efficient probabilistic data structure. It is used to test whether an element is a member of a set.
  Usually we consider trade off between time and space. If we can't trade one of them, then we need to consider accuracy.

Suppose m bits, k hash function. Each time insert one item, calculate the k hash function, then set the corresponding bit of m bits.
`
bit_map = list() # length m
for i in k:
    v = hash_i(item)
    v = v % 10
    m[v] = 1
`
May cause false positive.
**Ref** : https://www.jianshu.com/p/8193d7dc8348

##### Suspicious mechanism

##### Consistenct level
1. Any
2. All
3. One
4. Quorums

#### CAT Theorem
1. Consistency
2. Availability
3. Partion-tolerance

#### Other Consistency Model
1. Per-Key sequential
2. CRDTa (Commutative Replicated Data Types)
3. Red-blue consistency

**Strong Consistency**
1. Linearizability
2. Sequential Consistency

#### HBase
Prefer consistancy than availability compared to Casandra.

#### Summary
1. Traditional DB works with strong consistency.
2. Modern systems workload don't need, but need fast (availability).

#### Time and Ordering
Asynchronous distributed system consists of number of process. Each has its own clock. 
1. Clock Skew : Relative difference of two clock value
2. Clock Drift : Relative difference of two clock frequency

**Maximum drift rate**
Given a max acceptable skew M between any pair of two clocks, need to synchronize at least once per : M / (2\*MDR)

##### Clock Syne Algorithm
1. Cristian'a Algorithm
2. NTP (Nerwork Time Protocol)
    1. Organized in a tree
    2. Each client is leaf
    3. Each node sync to its parent
 
**Lamport Timestamp** 
Do we necessary to sync time? We only need to maintain the causality (Lamport Ordering).
1. Each process use a counter, init 0.
2. Each process increase its counter when send or an instruction happens, assign to its timestamp.
3. A send carry its timestamp.
4. Once received a send, assign its timestamp to `max(local time, message timestamp)` + 1. 
5. May exist concurrency.

##### Vector Clock
1. Process maintain a vector which has length equals number of processes. Record each process's timestamp.
2. When send message, send its vector also.
  
By using this vector clock, we can tell the difference between causality and concurrency.

**Gotchas**
1. Allowed to increase clock value, but never decrease.
2. Allowed to increase or decrease speed of clock.
3. If error too high, take multiple readings and average them.


### Week 5
#### Global snapshot
It is global state.
1. Process state
2. Channel state  
Don't need to sync all, causality is enough.

##### System model
Record process state and channel state.
1. N process in system
2. Two uni-directional channels between each ordered process
3. Communication channel are FIFO
4. No failure
5. All messages arrive intact, no duplicate

Should not interfere normal application.  
Global state is collected in a distributed manner.  
All process may initiate a new global state.  

##### Chandy-Lamport algorithm
1. Cut : time frontier at each process and each channel  
    1. Consistent cut
2. Use this concept to prove the algorithm obey causality.

##### Lives and Safty
1. Liveness : something good will happen eventually
2. Safty : something bad will never happen

Chandy-Lamport can be used to detect global properties that are stable, where stable means :  
once true, stay true forever.


#### Multicast
Send to a group of nodes.
1. FIFO ordering
2. Causal ordering
3. Total ordering

##### Implementation
1. FIFO : Use a vector to record all message order sent by process. If receive number not equals to current order + 1, buffer it.  
2. Total ordering : A global sequencer. After receive the message from sequencer, deliver.  
3. Causal ordering : Similar to FIFO, but different updating rule. 

#### Reliable
Every correct process in the group receive the same set of multicast.

#### Virtual Synchrony
1. Views : Each process maintain a membershiplist called view. An update to the view is called view change.
2. The set of multicast delivered in a given view is the same set at all correct process that were in the view.
3. If not, then the process will be forcibly removed from v at other process.
4. What happen in a view, stay in a view.

#### Consensus Problem
A group of servers attampting to : 
1. Reliable multicast
2. Membership/Failure detection
3. Leader Election
4. Mutual Exclusion 
  
Many problems in distributed system is equivalent to consensus.

##### Synchronous System
1. Message delay
2. Upper bound on clock drift rate
3. Max time for each process step

##### Asynchronous System
**Paxos**
1. Consensus is impossible to solve in this async system where delays are unbounded.  
2. But we can do safty and eventual liveness.



 










