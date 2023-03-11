# BMI3-project
I shared Louvain algorithm (clustering) implementation with self-optimization 

# Our project is TCRcluster.

TCRs can mediate the recognition of antigens through the binding with MHC. The change in TCR repertoire can show the individual immune response status and TCR clustering is one of the relevant analyses that has aroused interest in the potential inference of the shared antigen. Based on conserved sequences in TCRs that recognize the same epitope, TCRcluster, a mini software, was developed to combine TCR distance calculation, clustering, visualization, and verification functions together. It only requires TCR sequences as input and can optionally provide consensus antigen sequences if other information is given. It contributes to the identification of the key conserved sequences for specific antigen recognition and can be applied in the diagnosis of diseases. Our group was devoted to this project, and TCRcluster shows some advantages but also limitations. It was a challenging and meaningful attempt to work together and learn how to develop useful tools that can contribute to the progress of the field of immunity.

# I responsible for the Clustering part
The general idea of Louvain clustering algorithm is shown below.

I developed a graph-based greedy algorithm referring to the Louvain algorithm (Blondel et al., 2008). The key to the algorithm is to continually optimize modularity which can represent the quality of how the network is segmented by communities (clusters). The whole stage primarily contains two critical steps. In the first step, every node in the network attempts to merge into its neighbor’s community which results in a change in the modularity marked as ∆Q. For each node, adopt the change with the maximal positive ∆Q an move the node into the corresponding community. If no ∆Q is positive, the node will not change. Iterate this step constantly until no node moves to a new community. In the second step, the nodes partitioned into the same community in the first step will be aggregated into a new super-node. Edges in the same community contribute to a self-loop of the new node while the edges linking different communities serve as connections between the super-nodes. A new network is constructed for another run of the entire cycle. Finally, the whole process stops until there is no change in the first step.

# Highlights of my code
Compared to the Louvain method in the ”networkx” package (Hagberg et al., 2008), my codes can attain the function much more quickly. The probable reason accounting for the performance is that I used the derived formula which only considers a small part of the network and greatly reduces the computations rather than the global modularity.

The complete codes for implementation are shown in the repository.
