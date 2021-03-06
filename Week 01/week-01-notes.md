## Week 1: Undirected Graphs

> We define an undirected graph API and consider the adjacency-matrix and adjacency-lists representations. We introduce two classic algorithms for searching a graph—depth-first search and breadth-first search. We also consider the problem of computing connected components and conclude with related problems and applications.

### Introduction to Graphs

- _Graph_ - set of **vertices** connected pairwise by **edges**
- Why study graph algorithms?
  - Thousands of practical applications
  - Hundreds of graph algorithms known
  - Interesting and broadly useful abstraction
  - Challenging branch of computer science and discrete math
- _Path_ - sequence of vertices connected by edges
- _Cycle_ - path whose first and last vertices are the same
- Two vertices are **connected** if there is a path between them

### Graph API

- _Graph drawing_ - provides intuition about the structure of the graph (however, intuition can be misleading)
- _Vertex representation_:

  - In this lecture we will use integers between 0 and _V - 1_
  - In application, we convert between names and integers with a symbol table

- The typical graph API is as follows:

```java
public class Graph {
  // Create an empty graph with V vertices
  Graph(int V) {}

  // Create a graph from input stream
  Graph(In in) {}

  // Add an edge v-w
  void addEdge(int v, int w) {}

  // Vertices adjacent to v
  Iterable<Integer> adj(int v) {}

  // Number of vertices
  int V() {}

  // Number of edges
  int E() {}

  // String representation
  String toString() {}
}
```

- Adjacency-matrix graph representation requires a two-dimensional _V-by-V_ boolean array, where each edge _v-w_ in graph: `adj[v][w] == adj[w][v]` returns true
- The Java implementation for the adjacency-list graph representation is as follows:

```java
public class Graph {
  // Use bag data type
  private final int V;
  private final Bag<Integer>[] adj;

  // Create empty graph with V vertices
  public Graph(int V) {
    this.V = V;
    adj = (Bag<Integer>[]) new Bag[V];
    for (int v = 0; v < V; v++) {
      adj[v] = new Bag<Integer>();
    }
  }

  // Add edge v-w
  public void addEdge(int v, int w) {
    adj[v].add(w);
    adj[w].add(v);
  }

  // Iterator for vertices adjacent to v
  public Iterable<Integer> adj(int v) {
    return adj[v];
  }
}
```

- In practice, use adjacency-lists representation:
  - Algorithms based on iterating over vertices adjacent to _v_
  - Real-world graphs tend to be **sparse** (huge number of vertices, small average vertex degree)

### Depth-first Search

- Take a maze graph for example:
  - Each vertex is an intersection and each edge a passage
  - The goal is to explore every intersection in the maze
- Tremaux maze exploration:

  - Unroll a ball of string behind you
  - Mark each visited intersection and each visited passage
  - Retrace steps when no unvisited options

- DFS (Depth-first Search) is similar to this maze problem in that:
  - Goal is to systematically search through a graph
  - Find all vertices connected to a given source vertex
  - Find a path between two vertices
- The design pattern for graph processing is as follows:
  - Decouple graph data type from graph processing
  - Create a `Graph` object
  - Pass the `Graph` to a graph-processing routine
  - Query the graph-processing routine for information
- DFS then becomes the following:

  - Goal is to visit vertex _v_
  - Mark vertex _v_ as visited
  - Recursively visit all unmarked vertices adjacent to _v_

- DFS can be implemented in Java as follows:

```java
public class DepthFirstPaths {
  // marked[v] = true if v connected to s
  private boolean[] marked;
  // edgeTo[v] = previous vertex on path from s to v
  private int[] edgeTo;
  private int s;

  // Initialize data structures
  public DepthFirstPaths(Graph G, int s) {
    // ...
    // Find vertices connected to s
    dfs(G, s);
  }

  // Recursive DFS
  private void dfs(Graph G, int v) {
    marked[v] = true;
    for (int w : G.adj(v)) {
      if (!marked[w]) {
        dfs(G, w);
        edgeTo[w] = v;
      }
    }
  }
}
```

- DFS marks all vertices connected to _s_ in time proportional to the sum of their degrees
- Each vertex connect to _s_ is visited once
- After DFS, can find vertices connected to s in constant time and can find a path to _s_ (if one exists) in time proportional to its length

### Breadth-first Search

- BFS (Breadth-first Search) is similar to DFS but we use a queue and we repeat the following process until queue is empty:
  - Remove vertex _v_ from queue
  - Add to queue all unmarked vertices adjacent to _v_ and mark them
- _DFS_ - put unvisited vertices on a **stack**
- _BFS_ - put unvisited vertices on a **queue**
- _Shortest path_ - find path from _s_ to _t_ that uses **fewest number of edges**
- BFS computes shortest paths (fewest number of edges) from _s_ to all other vertices in a graph in time proportional to _E + V_
- Each vertex connected to _s_ is visited once

- BFS is implemented in Java as follows:

```java
public class BreadthFirstPaths {
  private boolean[] marked;
  private int[] edgeTo;
  // …

  private void bfs(Graph G, int s) {
    Queue<Integer> q = new Queue<Integer>();
    q.enqueue(s);
    marked[s] = true;
    while (!q.isEmpty()) {
      int v = q.dequeue();
      for (int w : G.adj(v)) {
        if (!marked[w]) {
          q.enqueue(w);
          marked[w] = true;
          edgeTo[w] = v;
        }
      }
    }
  }
}
```

### Connected Components

- Connectivity queries:
  - Vertices _v_ and _w_ are **connected** if there is a path between them
  - Goal: preprocess graph to answer queries of the form: _is v connected to w?_ in **constant** time
  - Solution: use DFS
- _Connected components_ - a maximal set of connected values
- The _is-connected-to_ relation is an _equivalence relation_ if:
  - Reflexive: _v_ is connected to _v_
  - Symmetric: if _v_ is connected to _w_, then _w_ is connected to _v_
  - Transitive: if _v_ is connected to _w_ and _w_ connected to _x_, then _v_ connected to _x_
- Given connected components we can answer queries in constant time
- To visit a vertex _v_:
  - Mark vertex _v_ as visited
  - Recursively visit all unmarked vertices adjacent to _v_

### Challenges

- What are some challenges we face in graph-processing?
  - Is a graph bipartite? DFS-based solution
  - Find a cycle: DFS-based solution
  - Find a (general) cycle that uses every edge exactly twice: Eulerian tour
  - Find a cycle that visits every vertex exactly once: Hamiltonian cycle
  - Are two graphs identical except for vertex names? Graph isomorphism (no one knows)
  - Lay out a graph in the plane without crossing edges? Linear-time DFS-based planarity algorithm discovered by Tarjan in 1970s (too complicated for most practitioners)
- The Seven Bridges of Konigsberg: is there a (general) cycle that uses each edge exactly twice? a connected graph is Eulerian if and only if all vertices have **even** degree

## Week 1: Directed Graphs

> In this lecture we study directed graphs. We begin with depth-first search and breadth-first search in digraphs and describe applications ranging from garbage collection to web crawling. Next, we introduce a depth-first search based algorithm for computing the topological order of an acyclic digraph. Finally, we implement the Kosaraju-Sharir algorithm for computing the strong components of a digraph.

### Introduction to Digraphs

- _Digraph_ - set of vertices connected pairwise by **directed** edges
- Some digraph problems include:
  - _Path_ - is there a directed path from _s_ to _t_?
  - _Shortest path_ - what is the shortest directed path from _s_ to _t_?
  - _Topological sort_ - can you draw a digraph so that all edges point upwards?
  - _Strong connectivity_ - is there a directed path between all pairs of vertices?
  - _Transitive closure_ - for which vertices _v_ and _w_ is there a path from _v_ to _w_?
  - _PageRank_ - what is the importance of a web page?

### Diagraph API

- The diagraph API is as follows:

```java
public class Digraph {
  // Create an empty diagraph with V vertices
  Digraph(int V) {}

  // Create a diagraph from input stream
  Diagraph(In in) {}

  // Add a directed edge v -> w
  void addEdge(int v, int w) {}

  // Vertices pointing from v
  Iterable<Integer> adj (int v)

  // Number of vertices
  int V() {}

  // Number of edges
  int E() {}

  // Reverse of this diagraph
  Digraph reverse() {}

  // String representation
  String toString() {}
}
```

- The Java implementation for the adjacency-list digraph representation is very similar to the graph representation, the main difference is in `addEdge()`:

```java
public class Digraph {
  // Use bag data type
  private final int V;
  private final Bag<Integer>[] adj;

  // Create empty digraph with V vertices
  public Digraph(int V) {
    this.V = V;
    adj = (Bag<Integer>[]) new Bag[V];
    for (int v = 0; v < V; v++) {
      adj[v] = new Bag<Integer>();
    }
  }

  // Add edge v->w
  public void addEdge(int v, int w) {
    adj[v].add(w);
  }

  // Iterator for vertices pointing from v
  public Iterable<Integer> adj(int v) {
    return adj[v];
  }
}
```

- In practice use adjacency-lists representation:
  - Algorithms based on iterating over vertices pointing from _v_
  - Real-world digraphs tend to be sparse (huge number of vertices, small average vertex degree)

### Digraph Search

- Reachability - find all vertices reachable from _s_ along a directed path
- DFS in digraphs:

  - Same method as for undirected graphs
  - Every undirected graph is a digraph (with edges in both directions)
  - DFS is a **digraph** algorithm
  - Recall DFS, for digraphs is slightly different (one direction):
    - Marks vertex _v_ as visited
    - Recursively visits all unmarked vertices pointing from _v_

- DFS implementation for digraphs is identical to DFS implementation for graphs:

```java
public class DirectedDFS {
  // True if path from s
  private boolean[] marked;

  // Constructor marks vertices reachable from s
  public DirectedDFS(Digraph G, int s) {
    marked = new boolean[G.V()];
    dfs(G, s);
  }

  // Recursive DFS does the work
  private void dfs(Digraph G, int v) {
    marked[v] = true;
    for (int w: G.adj(v)) {
      if (!marked[w]) {
        dfs(G, w);
      }
    }
  }

  // Client can ask whether any vertex is reachable from s
  public boolean visited(int v) {
    return marked[v];
  }
}
```

- DFS enables direct solution of simple digraph problems:
  - Reachability
  - Path finding
  - Topological sort
  - Directed cycle detection
- DFS forms the basis for solving difficult digraph problems:

  - Two-satisfiability
  - Directed Euler path
  - Strongly-connected components

- Similarly, BFS is same method as for undirected graphs:
  - Every undirected graph is a digraph (with edges in both directions)
  - BFS is a **digraph** algorithm
- BFS computes shortest paths (fewest number of edges) from _s_ to all other vertices in a digraph in time proportional to _E + V_
- BFS works as follows for directed graphs:
  - Repeat until queue is empty
  - Remove vertex _v_ from queue
  - Add to queue all unmarked vertices point from _v_ and mark them
- For multiple-source shortest paths:
  - Given a digraph and a **set** of source vertices
  - Find shortest path from any vertex in the set to each other vertex

### Topological Sort

- _DAG_ - directed acyclic graph
- _Topological sort_ - redraw DAG so all edges point upwards (use DFS)
- Topological sort works as follows:
  - Run DFS
  - Return vertices in reverse postorder
- Some observations:
  - Reverse DFS postorder of a DAG is a topological order
  - A digraph has a topological order if and only if no directed cycle

### Strong Components

- Vertices _v_ and _w_ are **strongly connected** if there is both a directed path from _v_ to _w_ **and** a directed path from _w_ to _v_
- Strong connectivity is an **equivalence relation**:
  - _v_ is strongly connected to _v_
  - If _v_ is strongly connected to _w_, then _w_ is strongly connected to _v_
  - If _v_ is strongly connected to _w_ and _w_ to _x_, then _v_ is strongly connected to _x_
- A **strong component** is a maximal subset of strongly-connected vertices

- Kosaraju-Sharir algorithm:
  - _Reverse graph_ - strong components in _G_ are same as in _G^R_
  - _Kernel DAG_ - contract each strong component into a single vertex
  - _Idea_:
    - Compute topological order (reverse postorder) in kernel DAG
    - Run DFS, considering vertices in reverse topological order
  - _Phase 1_ - compute reverse postorder in _G^R_
  - _Phase 2_ - run DFS in _G_, visiting unmarked vertices in reverse postorder of _G^R_
- Another way to implement the Kosaraju-Sharir algorithm:
  - _Phase 1_ - run DFS on _G^R_ to compute reverse postorder
  - _Phase 2_ - run DFS on _G_, considering vertices in order given by first DFS
- An observation of the Kosaraju-Sharir algorithm is that it computers the strong components of a digraph in time proportional to _E + V_

- Recall DFS implementation for connected components in an undirected graph:

```java
public class CC {
  private boolean marked[];
  private int[] id;
  private int count;

  public CC(Graph G) {
    marked = new boolean[G.V()];
    id = new int[G.V()];
    for (int v = 0; v < G.V(); v++) {
      if (!marked[v]) {
        dfs(G, v);
        count++;
      }
    }
  }

  private void dfs(Graph G, int v) {
    marked[v] = true;
    id[v] = count;
    for (int w: G.adj(v)) {
      if (!marked[w]) {
        dfs(G, w);
      }
    }
  }

  public boolean connected(int v, int w) {
    return id[v] == id[w];
  }
}
```

- For strong components in a digraph we could use two DFS:

```java
public class KosarajuSharirSCC {
  private boolean marked[];
  private int[] id;
  private int count;

  public KosarajuSharirSCC(Digraph G) {
    marked = new boolean[G.V()];
    id = new int[G.V()];
    DepthFirstOrder dfs = new DepthFirstOrder(G.reverse());
    for (int v: dfs.reversePost()) {
      if (!marked[v]) {
        dfs(G, v);
        count++;
      }
    }
  }

  private void dfs(Digraph G, int v) {
    marked[v] = true;
    id[v] = count;
    for (int w: G.adj(v)) {
      if (!marked[w]) {
        dfs(G, w);
      }
    }
  }

  public boolean stronglyConnected(int v, int w) {
    return id[v] == id[w];
  }
}
```
