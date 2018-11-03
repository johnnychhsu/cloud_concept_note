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

#### Some Reference
##### SQL at Scale
https://towardsdatascience.com/sql-at-scale-with-apache-spark-sql-and-dataframes-concepts-architecture-and-examples-c567853a702f
