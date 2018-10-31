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


