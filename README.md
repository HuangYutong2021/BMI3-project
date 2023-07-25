# BMI3-PROJECT: **TCRcluster**
**Group members: Yutong Huang, Jiayi Tang, Zihao Hu, Jianzhang Lu, Yule Zhu**

TCRs can mediate the recognition of antigens through the binding with MHC. The change in TCR repertoire can show the individual immune response status and TCR clustering is one of the relevant analyses that has aroused interest in the potential inference of the shared antigen. Based on conserved sequences in TCRs that recognize the same epitope, **TCRcluster**, a mini software, was developed to combine **TCR distance calculation**, **clustering**, **visualization**, and **verification** functions together. It only requires TCR sequences as input and can optionally provide consensus antigen sequences if other information is given. It contributes to the identification of the key conserved sequences for specific antigen recognition and can be applied in the diagnosis of diseases. Our group was devoted to this project, and TCRcluster shows some advantages but also limitations. It was a challenging and meaningful attempt to work together and learn how to develop useful tools that can contribute to the progress of the field of immunity.
![image](https://github.com/HuangYutong2021/BMI3-project/assets/79962064/490a2052-6c96-44ad-97d3-85b6fd41368d)

# Core Algorithm Design
**1 Distance calculation**

The key algorithm is dynamic programming. We calculate the similarity score (distance) of every two CDR3 sequences at a time. Mismatch, gap open, and gap extension are the three types of penalty. BLOSUM-62 or BLOSUM-80 matrix is officially used to assign scores for mismatched or matched amino acids. Also, gap open and extension will be considered different penalty scores to better differentiate these sequences. With CDR3α and CDR3β sequences given, we sum them up with different weights alternatively given by users. To better classify the sequences without excessive complication or insufficient connections, for each sequence considered as a node, the highest k (user can define the percentage of the total nodes) neighbors will be reserved as the output data to be processed next.

**2 Clustering (My work)**

We developed a graph-based greedy algorithm referring to the Louvain algorithm (Blondel et al., 2008). The key to the algorithm is to continually optimize modularity which can represent the quality of how the network is segmented by communities (clusters). The whole stage primarily contains two critical steps. In the first step, every node in the network attempts to merge into its neighbor’s community which results in a change in the modularity marked as ∆Q. For each node, adopt the change with the maximal positive ∆Q an move the node into the corresponding community. If no ∆Q is positive, the node will not change. Iterate this step constantly until no node moves to a new community. In the second step, the nodes partitioned into the same community in the first step will be aggregated into a new super-node. Edges in the same community contribute to a self-loop of the new node while the edges linking different communities serve as connections between the super-nodes. A new network is constructed for another run of the entire cycle. Finally, the whole process stops until there is no change in the first step.

**3 Visualization**

An undirected graph with CDRH3 sequences (nodes) and distances (edges) will be presented in the visualization. The actual position of the nodes in the two axes can be calculated through the Fruchterman-Reingold algorithm (Fruchterman and Reingold, 1991) and every node will be regarded as an electron. In that case, two types of forces play roles on every node: The coulomb force to avoid overlap of nodes and the tensile force to push nodes closer according to their TCR distances. Iterate it several times until the stabilization. After identifying the coordinates of each node, they are separately colored based on which cluster they belong to.

# Parameter Explanation
Thanks to the major writer: **Jianzhang Lu**

**1 Input format**

A .csv file must contain at least one column with the fixed name: CDR3b, which represents CDR3β sequences. Notably, the input file must also contain the CDR3a column representing CDR3α sequences if you want to consider the influence of CDR3α (Use -w/–weight, see below for more information). Additionally, peptide column representing the sequences of corresponding epitope amino acid must be contained if you want to verify the result of clustering (Use -ver/–verification, see below for more information). The software will raise information if the input format is wrong.

**2 Parameter explanation**

General usage: TCRcluster.py -i [-t] [-B] [-e] [-o] [-w] [-s] [-ver] -out

Arguments:

-h, –help: Show the quick and brief help message.

-i, –input: The path of the input .csv file.

-t, –trim: If this parameter is added, the conserved sequences of CDR3 (2 amino acids on the N-terminal side and the C-terminal side) will be trimmed and not used when calculating distances. If your input file has already contained trimmed sequences, please do not set this parameter.

-B, –BLOSUM: Select the BLOSUM matrix used in global alignment to assign points for matched or mis￾matched amino acids. We currently provide two matrixes: BLOSUM-62 and BLOSUM-80. You can choose either 62 or 80 for this value. If your provided sequences seem to have higher similarities, we suggest using -B 80 or –BLOSUM 80. Additionally, if you have no idea about the degree of similarities of your sequences, just use our default value: -B 62 or –BLOSUM 62.

-e, –extend: The penalty points for a gap extend (default -1).

-o, –open: The penalty points for a gap open (default -5).

-w, –weight: The percentage of CDR3b weight when calculating the distances between each TCR sequence. This value must be integers from 0 to 100 (default 100). For example, “100” means that only the CDR3b sequence will be considered, while “90” means that the final distances between each TCR sequence are equal to 90% of the CDR3b sequence distances plus 10% of the CDR3a sequence distances. We highly recommend setting more than 90 for this parameter.

-s, –select: Only the top percent of distances from each TCR sequence to others will be considered when clustering. This value must be integers from 1 to 50 (default 10). For instance, “10” means that for each CDR3 sequence, only the top 10% distances to others will be preserved for clustering. A bigger percentage may conclude a more convincing conclusion but with a much longer running time (we highly recommend using the default value for this parameter).

-ver, –verification: Whether to verify the result of TCR clustering using the corresponding epitope sequences. If this parameter is added, a .csv file containing the consensus motif of each cluster will be output. Remember to add the peptide column in your input file if this parameter is set.

-out, –output: The path and the title name for output files. We have more than one output file, therefore you just need to provide the title name, not including the format of the output files. For example, use -out test instead of -out test.txt or test.png.

**3 Output format**

The software will deliver two output files if you do not set -ver, while three files if you set it. The first is a .txt file showing the result of clustering. Numbers in each community represent the original rows (starting from 0) in your input file, meaning that the TCRs in these rows are clustered into the same community. The second is a .png file showing a clustering plot for visualization. The third is a .csv file (only when -ver is set) containing the consensus motif of epitope antigens in each cluster, together with the information of epitope antigen and CDR3b sequences for each TCR. You can see the below examples for a more intuitive sense.

**4 Additional information**

The software will show the step it is currently running and you can choose to stop running it during visualization since this step costs much time. In this situation, you will not get the .png output file. The row number (number of TCR sequences) is suggested to be more than 100 since fewer data will lead to a relatively bad clustering result and visualization. Due to the randomization of clustering and visualization steps, you can run this software with the same parameters and input file more than one time to get a satisfactory result.


# Highlights of My work - Clustering

Compared to the Louvain method in the ”networkx” package (Hagberg et al., 2008), my codes can attain the function much more quickly. The probable reason accounting for the performance is that I used the derived formula which only considers a small part of the network and greatly reduces the computations rather than the global modularity.
![image](https://github.com/HuangYutong2021/BMI3-project/assets/79962064/0d5dc266-783e-4c1f-b96a-37199ccba0f6)


All the code for the project is shown in the repository.
