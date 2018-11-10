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


### Some Reference
#### SQL at Scale
https://towardsdatascience.com/sql-at-scale-with-apache-spark-sql-and-dataframes-concepts-architecture-and-examples-c567853a702f

