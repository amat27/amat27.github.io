WUE (Water Usage Efficiency) = Annual Water Usage / IT Equipment Energy (L/kWh)
PUE (Power Usage Efficiency) = Total Facility Power / IT Equipment Power

Consistency Spectrum
Weak Consistency:

- Eventual Consistency (Cassandra, Dynamo, Voldemort)
- Per-key Sequential
- CRDTs (Commutative Replicated Data Types)
- Red-blue Consistency
- Causal Consistency

Strong Consistency:

- Sequential
- Linearizability
- Transaction ACID (Hyperdex, Spanner, Transaction Chains)

Consistent Hashing

- Using SHA-1 to has peer address to 160 bit array
- Truncated to m bits, treated as peer id

Graph
- Two Important Graph Properties
    - Clustering Coefficient: the probability if A and B are neighbors if A and C are neighbors and B and C are also neighbors
    - Path Length: the average path length of a graph is the average of path length between each pair of vertices
- Graph Type
    - Extended Ring Graph: High CC, Long Paths
    - Random Graph: Low CC, Short Path
    - Small World Networks: High CC, Short Paths
- Degree distribution: the probability of a given node having k edges
    - Regular graph: all nodes same degree
    - Gaussian
    - Random graph: Exponential -e^(-k.c)
    - Power law: k^-a