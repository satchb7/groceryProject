# groceryProject

Chinese Postman Problem
Purpose
	Recently there has been a proliferation of services that do your shopping for you, and bring you what you requested. This was very important during the Covid-19 pandemic and has continued to be used in the wake of it. These services are incentivized to be as efficient as possible in the stores as they can be. Usually the shoppers working for these companies have to fulfill a number of different orders at once, to the point where they functionally have to visit every aisle of the grocery store. Without an informed approach to this task, this can end up taking far longer than it needs to. We aim to solve this problem by modeling it as a Chinese Postman Problem (CPP). The CPP solves the most efficient way to traverse every edge in a graph at least once and return to the starting node.

The basic outline for solving the CPP is as follows

If there are all even degree nodes in the graph, generate an Eulerian Path and return it

If odd nodes exist, find all pairs shortest paths between nodes on the graph

Find the minimum cost pairwise matching of odd nodes

Augment the graph to include edges between these pairings with the relevant costs

Solve for the Eulerian Path on the augmented graph and return the path

** The following section will include an intuitive explanation of each algorithm and how it works, all algorithms were coded from scratch and can be found implemented in the python notebook
    1)
Step one (checking for odd degree nodes) can be done relatively trivially. In the example I had 26 of our 38 nodes as odd degree nodes. If there had been none we could have generated an Euler Path using Fleury’s Algorithm, which I will cover in detail later.

   2) 
   	In order to find all pairs shortest paths I opted to use the Floyd Warshall algorithm. This algorithm is a dynamic programming approach to finding all pairs shortest paths. Dynamic programming relies on saving the results of solved subproblems to then come together and generate a final solution, effectively reducing redundant computation. Floyd Warshall operates on the assumption that there can be negative edge weights, but no negative cost cycles in the graph.
The algorithm starts with an N by N graph where all edges i,j are included with their weights, using infinity for everything else. The algorithm then uses a triple for loop for each vertex. The main crux of the algorithm lies in the inequality which can be understood to mean if the existing cost from node i to node j, is greater than the cost from i to some intermediate node k added to the distance from that same k to j then we update the shortest path to be that distance. Once the algorithm finishes running, the resulting N by N matrix contains the shortest from i to j at each entry i,j.
	Traditionally, the Floyd Warshall algorithm gives the lowest cost from i to j but not the actual path. We added a second N by N matrix keeping track of previous nodes at all points, allowing for backtracking at a later point in time when reconstituting the eulerian path.
   
   3) 
	The minimum cost pairwise matching can be found using an algorithm called Blossom’s algorithm. Due to the complexity of the algorithm and lack of packages available implementing the functionality, as well as only having 26 odd nodes, I opted to model this as a linear programming problem in a fully connected graph, using the results of Floyd Warshall for the edge weights. I used the Gurobi optimization software to solve this minimization problem. The code can be viewed in the attached python notebook.

   4) 
	Once I generated the minimum cost pairwise matching I augmented the graph to include undirected edges between these nodes, with the associated shortest path costs from Floyd Warshall. After doing this we are left with a graph with all even degree nodes, which is guaranteed to have an Euler path.
   
   5)
	Once I had our even degree graph, we are able to use Fleury’s algorithm in order to generate an Euler Path. Fleury’s Algorithm is for undirected graphs, which we needed because we don't want to have to go up and down each aisle in the store, rather just pass along the aisle. Hierholzer’s algorithm can be used to generate an Euler Path for a directed graph. Fleury’s algorithm relies on “not burning bridges”. The basic logic is that you start at a node, And evaluate the edges for if they are valid. A valid edge is an edge that does not disconnect some part of the graph if deleted, which we are able to find using a DFS. The algorithm then traverses node to node using valid edges and deleting edges that have been used until it returns to the start, having generated an Euler Path

Using this method, I was able to generate the most efficient path for an instacart shopper to go through a grocery store resulting in a path only 18% longer than the sum of all edges in the graph.
