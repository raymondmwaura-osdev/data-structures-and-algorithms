# Graph Data Structures

**This was written by deepseek.**

## 1. Introduction to Graphs

In computer science, a **graph** is an abstract data type that implements the graph theory concepts from mathematics. Graphs consist of vertices (also called nodes) connected by edges (also called links or arcs). This structure enables the representation of complex relationships across numerous domains, including social networks, transportation systems, communication networks, and dependency relationships.

The ubiquity of graphs in computational problem-solving makes understanding this data structure essential for any serious programmer or computer scientist.

---

## 2. Fundamental Terminology

Before examining implementation details, we must establish precise terminology:

### 2.1 Core Components

**Vertex (Node)**: A fundamental unit representing an entity. In social network analysis, vertices might represent individual users.

**Edge (Arc)**: A connection between two vertices, representing a relationship. In a transportation network, edges might represent routes between cities.

### 2.2 Graph Classifications

**Directed Graph (Digraph)**: A graph in which edges have orientation; edge (u, v) is distinct from (v, u). Directed edges imply unidirectional relationships, such as follower relationships on social media platforms.

**Undirected Graph**: A graph in which edges have no orientation; edge (u, v) is equivalent to (v, u). Undirected edges represent symmetric relationships, such as mutual friendships.

**Weighted Graph**: A graph in which each edge carries a numerical value (weight), representing cost, distance, capacity, or other quantitative attributes.

**Unweighted Graph**: A graph in which edges possess no associated numerical values; only connectivity matters.

**Cyclic Graph**: A graph containing at least one cycle; a path that begins and ends at the same vertex without traversing any edge twice.

**Acyclic Graph**: A graph containing no cycles. Directed acyclic graphs (DAGs) are particularly important in scheduling and dependency resolution.

**Connected Graph**: An undirected graph where a path exists between every pair of vertices.

**Disconnected Graph**: An undirected graph containing at least two vertices with no connecting path.

### 2.3 Mathematical Notation

Formally, a graph G is defined as an ordered pair G = (V, E), where:
- V is a set of vertices
- E is a set of edges, each edge being a 2-element subset of V (for undirected graphs) or an ordered pair (for directed graphs)

---

## 3. Graph Representations

The efficient representation of graphs in computer memory is crucial for algorithmic performance. Three primary representation methods dominate practice.

### 3.1 Adjacency Matrix

An adjacency matrix is a 2-dimensional array of size |V| × |V|, where |V| represents the number of vertices. For unweighted graphs, matrix element A[i][j] = 1 indicates an edge from vertex i to vertex j; for weighted graphs, A[i][j] stores the edge weight.

**Implementation Example (Python):**

```python
class AdjacencyMatrixGraph:
    def __init__(self, num_vertices, directed=False):
        self.num_vertices = num_vertices
        self.directed = directed
        # Initialize matrix with zeros
        self.matrix = [[0 for _ in range(num_vertices)] 
                       for _ in range(num_vertices)]
    
    def add_edge(self, u, v, weight=1):
        """Add an edge from vertex u to vertex v with optional weight."""
        self.matrix[u][v] = weight
        if not self.directed:
            self.matrix[v][u] = weight
    
    def remove_edge(self, u, v):
        """Remove the edge between vertices u and v."""
        self.matrix[u][v] = 0
        if not self.directed:
            self.matrix[v][u] = 0
    
    def has_edge(self, u, v):
        """Check if an edge exists between vertices u and v."""
        return self.matrix[u][v] != 0
    
    def get_neighbors(self, u):
        """Return all neighbors of vertex u."""
        neighbors = []
        for v in range(self.num_vertices):
            if self.matrix[u][v] != 0:
                neighbors.append(v)
        return neighbors
```

**Complexity Analysis:**
- Space Complexity: O(|V|²)
- Time Complexity (edge lookup): O(1)
- Time Complexity (listing neighbors): O(|V|)

**Advantages:**
- Simple implementation
- Constant-time edge existence checking
- Efficient for dense graphs

**Disadvantages:**
- Memory-intensive for large, sparse graphs
- Inefficient neighbor enumeration

### 3.2 Adjacency List

An adjacency list represents a graph as an array of lists. The array size equals |V|, with each entry containing a list of adjacent vertices (and optionally edge weights).

**Implementation Example (Python):**

```python
class AdjacencyListGraph:
    def __init__(self, num_vertices, directed=False):
        self.num_vertices = num_vertices
        self.directed = directed
        # Initialize list of empty lists
        self.adj_list = [[] for _ in range(num_vertices)]
    
    def add_edge(self, u, v, weight=1):
        """Add an edge from vertex u to vertex v with optional weight."""
        # Store tuple (neighbor, weight)
        self.adj_list[u].append((v, weight))
        if not self.directed:
            self.adj_list[v].append((u, weight))
    
    def remove_edge(self, u, v):
        """Remove the edge between vertices u and v."""
        self.adj_list[u] = [(n, w) for (n, w) in self.adj_list[u] if n != v]
        if not self.directed:
            self.adj_list[v] = [(n, w) for (n, w) in self.adj_list[v] if n != u]
    
    def has_edge(self, u, v):
        """Check if an edge exists between vertices u and v."""
        return any(neighbor == v for neighbor, _ in self.adj_list[u])
    
    def get_neighbors(self, u):
        """Return all neighbors of vertex u."""
        return [neighbor for neighbor, _ in self.adj_list[u]]
```

**Complexity Analysis:**
- Space Complexity: O(|V| + |E|)
- Time Complexity (edge lookup): O(deg(u)), where deg(u) is the degree of vertex u
- Time Complexity (listing neighbors): O(deg(u))

**Advantages:**
- Memory-efficient for sparse graphs
- Efficient neighbor enumeration
- Common choice for most applications

**Disadvantages:**
- Slower edge existence checking (requires list traversal)
- Slightly more complex implementation

### 3.3 Edge List

An edge list represents a graph as a collection of edges, each stored as a tuple (u, v, weight). This representation is particularly useful when edge iteration is the primary operation.

**Implementation Example (Python):**

```python
class EdgeListGraph:
    def __init__(self, num_vertices, directed=False):
        self.num_vertices = num_vertices
        self.directed = directed
        self.edges = []
    
    def add_edge(self, u, v, weight=1):
        """Add an edge from vertex u to vertex v with optional weight."""
        self.edges.append((u, v, weight))
        if not self.directed and u != v:
            self.edges.append((v, u, weight))
    
    def remove_edge(self, u, v):
        """Remove the edge between vertices u and v."""
        self.edges = [(s, t, w) for (s, t, w) in self.edges 
                      if not (s == u and t == v)]
        if not self.directed:
            self.edges = [(s, t, w) for (s, t, w) in self.edges 
                          if not (s == v and t == u)]
    
    def get_all_edges(self):
        """Return all edges in the graph."""
        return self.edges
```

**Complexity Analysis:**
- Space Complexity: O(|E|)
- Time Complexity (edge lookup): O(|E|)
- Time Complexity (listing neighbors): O(|E|)

**Advantages:**
- Extremely space-efficient
- Simple representation
- Ideal for algorithms requiring edge iteration

**Disadvantages:**
- Inefficient for most graph operations
- Rarely used as primary representation

---

## 4. Graph Traversal Algorithms

Traversal algorithms systematically visit graph vertices, forming the foundation for more complex graph computations.

### 4.1 Breadth-First Search (BFS)

BFS explores a graph level by level, visiting all neighbors of a vertex before moving to the next level. This algorithm guarantees the shortest path in unweighted graphs.

**Algorithm Characteristics:**
- Uses a queue data structure
- Visits vertices in order of increasing distance from source
- Computes shortest paths in unweighted graphs
- Time Complexity: O(|V| + |E|)
- Space Complexity: O(|V|)

**Implementation Example:**

```python
from collections import deque

def breadth_first_search(graph, start_vertex):
    """
    Perform BFS traversal on a graph.
    
    Args:
        graph: An adjacency list graph implementation
        start_vertex: The vertex to begin traversal from
    
    Returns:
        A tuple containing:
        - visited_order: List of vertices in visitation order
        - distances: Dictionary mapping vertices to distance from start
    """
    visited = set()
    visited_order = []
    distances = {start_vertex: 0}
    
    # Initialize queue with start vertex
    queue = deque([start_vertex])
    visited.add(start_vertex)
    
    while queue:
        current_vertex = queue.popleft()
        visited_order.append(current_vertex)
        
        # Explore all neighbors
        for neighbor in graph.get_neighbors(current_vertex):
            if neighbor not in visited:
                visited.add(neighbor)
                distances[neighbor] = distances[current_vertex] + 1
                queue.append(neighbor)
    
    return visited_order, distances

# Example usage
graph = AdjacencyListGraph(6, directed=False)
edges = [(0, 1), (0, 2), (1, 3), (1, 4), (2, 4), (3, 5), (4, 5)]
for u, v in edges:
    graph.add_edge(u, v)

order, dist = breadth_first_search(graph, 0)
print(f"BFS traversal order: {order}")
print(f"Distances from vertex 0: {dist}")
```

### 4.2 Depth-First Search (DFS)

DFS explores a graph by traversing as far as possible along each branch before backtracking. This algorithm is fundamental for topological sorting, cycle detection, and connectivity analysis.

**Algorithm Characteristics:**
- Can be implemented recursively or iteratively (using a stack)
- Explores deep paths before wide exploration
- Useful for detecting cycles and connectivity
- Time Complexity: O(|V| + |E|)
- Space Complexity: O(|V|)

**Implementation Example (Recursive):**

```python
def depth_first_search(graph, start_vertex):
    """
    Perform DFS traversal on a graph.
    
    Args:
        graph: An adjacency list graph implementation
        start_vertex: The vertex to begin traversal from
    
    Returns:
        List of vertices in visitation order
    """
    visited = set()
    visited_order = []
    
    def dfs_recursive(vertex):
        """Helper function for recursive DFS."""
        visited.add(vertex)
        visited_order.append(vertex)
        
        for neighbor in graph.get_neighbors(vertex):
            if neighbor not in visited:
                dfs_recursive(neighbor)
    
    dfs_recursive(start_vertex)
    return visited_order

# Iterative implementation using stack
def depth_first_search_iterative(graph, start_vertex):
    """
    Perform iterative DFS traversal using a stack.
    """
    visited = set()
    visited_order = []
    stack = [start_vertex]
    
    while stack:
        current_vertex = stack.pop()
        
        if current_vertex not in visited:
            visited.add(current_vertex)
            visited_order.append(current_vertex)
            
            # Add neighbors to stack (reverse order for original DFS order)
            neighbors = graph.get_neighbors(current_vertex)
            for neighbor in reversed(neighbors):
                if neighbor not in visited:
                    stack.append(neighbor)
    
    return visited_order
```

### 4.3 Comparative Analysis: BFS vs. DFS

| Criterion | Breadth-First Search | Depth-First Search |
|-----------|---------------------|--------------------|
| Data Structure | Queue | Stack (or recursion) |
| Path Finding | Finds shortest path (unweighted) | Does not guarantee shortest path |
| Memory Usage | O(width) - can be large for wide graphs | O(depth) - efficient for deep graphs |
| Complete | Yes (finds solution if exists) | Yes (finds solution if exists) |
| Optimal | Yes (for unweighted graphs) | No |
| Applications | Shortest path, web crawling | Topological sort, maze solving |

---

## 5. Practical Applications

Graph data structures enable solutions to numerous real-world problems:

### 5.1 Social Network Analysis
- Vertices: Users
- Edges: Friendships, follows, interactions
- Algorithms: Community detection, influence maximization, recommendation systems

### 5.2 Navigation and Routing
- Vertices: Locations, intersections
- Edges: Roads, paths with weights representing distance or time
- Algorithms: Shortest path (Dijkstra, A*), traveling salesman

### 5.3 Dependency Resolution
- Vertices: Tasks, packages, modules
- Directed edges: Dependency relationships
- Algorithms: Topological sorting, cycle detection

### 5.4 Network Flow
- Vertices: Network nodes
- Weighted directed edges: Capacity constraints
- Algorithms: Maximum flow, minimum cut

### 5.5 Recommendation Systems
- Vertices: Users and items
- Edges: Purchases, ratings, views
- Algorithms: Collaborative filtering, bipartite graph analysis

---

## 6. Advanced Topics for Further Study

This tutorial provides foundational knowledge; advanced study should include:

1. **Minimum Spanning Trees**: Prim's and Kruskal's algorithms
2. **Shortest Path Algorithms**: Dijkstra, Bellman-Ford, Floyd-Warshall
3. **Network Flow**: Ford-Fulkerson, Edmonds-Karp
4. **Graph Coloring**: Chromatic number, greedy coloring
5. **Strongly Connected Components**: Kosaraju's and Tarjan's algorithms
6. **Graph Neural Networks**: Deep learning on graph-structured data

---

## 7. Summary

Graphs constitute one of the most versatile and powerful data structures in computer science. This tutorial has established the fundamental concepts, representations, and traversal algorithms necessary for further exploration. Mastery of graphs enables programmers to model and solve complex relational problems across virtually every domain of computing.

The progression from theoretical understanding to practical implementation requires deliberate practice. Readers are encouraged to implement the provided examples, experiment with variations, and explore how graph algorithms apply to problems in their specific domains of interest.

---

## Exercises

1. Implement a graph class that supports both directed and undirected graphs, with methods for adding/removing vertices and edges.

2. Write a function to detect cycles in a directed graph using DFS.

3. Implement a function that determines whether an undirected graph is connected.

4. Create a program that finds the shortest path between two vertices in an unweighted graph using BFS.

5. Implement a simple recommendation system using a bipartite graph of users and movies, where edges represent ratings.

---

## References

1. Cormen, T. H., Leiserson, C. E., Rivest, R. L., & Stein, C. (2009). *Introduction to Algorithms* (3rd ed.). MIT Press.

2. Sedgewick, R., & Wayne, K. (2011). *Algorithms* (4th ed.). Addison-Wesley Professional.

3. Knuth, D. E. (1997). *The Art of Computer Programming, Volume 1: Fundamental Algorithms* (3rd ed.). Addison-Wesley.

---
