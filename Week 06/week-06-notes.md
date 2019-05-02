## Week 6: Reductions

> In this lecture our goal is to develop ways to classify problems according to their computational requirements. We introduce the concept of reduction as a technique for studying the relationship among problems. People use reductions to design algorithms, establish lower bounds, and classify problems in terms of their computational requirements.

### Introduction to Reductions
* **Desiderata** - classify **problems** according to computational requirements
* The problem is that a huge number of problem have defied classification
* **Reduction** - problem *X* **reduces to** problem *Y* if you can use an algorithm that solves *Y* to help solve *X*
* Cost of solving *X* = total cost of solving *Y* + cost of reduction

### Designing Algorithms
* **Design algorithm** - given algorithm for *Y*, you can also solve *X* (see lecture slide for list of examples)
* Convex hull reduces to sorting:
  * **Sorting** - given *N* distinct integers, rearrange them in ascending order
  * **Convex hull** - given N points in the plane, identify the extreme points of the convex hull (in counterclockwise order)
  * Solution: Graham scan algorithm
  * Run-time cost: *N * log(N) + N*, second term is reduction
* **Graham scan**:
  * Choose point p with smallest (or largest) y-coordinate
  * **Sort** points by polar angle with p to get simple polygon
  * Consider points in order, and discard those that would create a clockwise turn
* Shortest paths on edge-weighted graphs and digraphs
  * Undirected shortest paths (with non-negative weights) reduces to directed shortest path
  * Solution: replace each undirected edge by two directed edges
  * Run-time cost of undirected shortest paths: *E * log(V) + E*
* Shortest paths with negative weights:
  * Reduction is invalid for edge-weighted graphs with negative weights (even if no negative cycles)
  * Can still solve shortest-paths problem in undirected graphs (if no negative cycles), but need more sophisticated techniques
* Linear-time reductions involving familiar problems:
  * See lecture slides for diagram

### Establishing Lower Bounds
* Bird's-eye view:
  * **Goal** - prove that a problem requires a certain number of steps
* Linear-time reductions:
  * Problem *X* linear-time reduces to problem *Y* if *X* can be solved with:
    * Linear number of standard computational steps
    * Constant number of calls to *Y*
  * **Establish lower bound**:
    * If *X* takes *Ω(N * log(N))* steps, then so does *Y*
    * If *X* takes *Ω(N^2)* steps, then so does *Y*
  * **Mentality**:
    * If I could easily solve Y, then I could easily solve X
    * I can’t easily solve X
    * Therefore, I can’t easily solve Y
* Lower bound for convex hull:
  * In quadratic decision tree model, any algorithm for sorting *N* integers requires *Ω(N * log(N)))* steps
  * Sorting linear-time reduces to convex hull
  * **Implication** - any ccw-based convex hull algorithm requires *Ω(N * log(N))* operations
* Sorting linear-time reduces to convex hull:
  * Region *{ x : x2 ≥ x }* is convex ⇒ all points are on hull
  * Starting at point with most negative *x*, counterclockwise order of hull points yields integers in ascending order
* In summary, establishing lower bounds through reduction is an important tool in guiding algorithm design efforts
* How to convince yourself no linear-time convex hull algorithm exists?
  * Long futile search for a linear-time algorithm (hard way)
  * Linear-time reduction from sorting (easy way)

### Classifying Problems
* **Desiderata** - problem with algorithm that matches lower bound
* Example: sorting and convex hull have complexity *N * log(N)*
* Prove that two problems *X* and *Y* have the same complexity:
  * First, show that problem *X* linear-time reduces to *Y*
  * Second, show that *Y* linear-time reduces to *X*
  * Conclude that *X* and *Y* have the same complexity
* Integer arithmetic reductions:
  * **Integer multiplication** - given two N-bit integers compute their product
  * **Brute force** - *N^2* bit operations
* Linear algebra reductions:
  * **Matrix multiplication** - given two N-by-N matrices, compute their product
  * **Brute force** -  *N^3* flops
* Bird's-eye review:
  * **Desiderata** - classify problems according to computational requirements:
    * Bad news: huge number of problems have defied classification
    * Good news: can put many problems into equivalence classes
* Complexity zoo:
  * **Complexity class** - set of problems sharing some computational property
  * **Bad news** - lots of complexity classes
* In summary:
  * **Reductions are important in theory to**:
    * Design algorithms
    * Establish lower bounds
    * Classify problems according to their computational requirements
  * **Reductions are important in practice to**:
    * Design algorithms
    * Design reusable software modules
      * Stacks, queues, priority queues, symbol tables, sets, graphs
      * sorting, regular expressions, Delaunay triangulation
      * MST, shortest path, max-flow, linear programming
    * Determine difficulty of your problem and choose the right tool

## Week 6: Linear Programming

> The quintessential problem-solving model is known as linear programming, and the simplex method for solving it is one of the most widely used algorithms. In this lecture, we given an overview of this central topic in operations research and describe its relationship to algorithms that we have considered.

### Linear Programming
* Linear programming is a problem-solving model for optimal allocation of scarce resources, among a number of competing activities that encompasses:
  * Shortest paths, maxflow, MST, matching, assignment, ...
  * *A * x = b*, 2-person zero-sum games, ...
* Significance of linear programming (see lecture slides for applications):
  * Fast commercial solvers available
  * Widely applicable problem-solving model
  * Key subroutine for integer programming solvers

### Brewer's Problem
* **Scenario** - small brewery produces ale and beer:
  * Production limited by scare resources: corn, hops, barley malt
  * Recipes for ale and beer require different proportions of resources
* **Goal** - choose product mix to maximize profits
* **Linear programming formulation**:
  * Let *A* be the number of barrels of ale
  * Let *B* be the number of barrels of beer
* Inequalities define **halfplanes**; feasible region is a **convex polygon** (see lecture slides)
* Optimal solution occurs at an **extreme point**
* Standard form linear program:
  * **Goal** - Maximize linear objective function of *n* non-negative variables, subject to *m* linear equations:
    * Input: real numbers *a_ij, c_j, b_i*
    * Output: real numbers *x_j*
* Converting the brewer's problems to the standard form:
  * Add variable *Z* and equation corresponding to objective function
  * Add **slack** variable to convert each inequality to an equality
  * Now a 6-dimensional problem
* Geometry:
  * Inequalities define **halfspaces**; feasible region is a **convex polyhedron**
  * A set is **convex** if for any two points *a* and *b* in the set, so is *(1/2) * (a + b)*
  * An extreme point of a set is a point in the set that can't be written as *(1/2) * (a + b)*, where *a* and *b* are two distinct points in the set
* **Extreme point property** - if there exists an optimal solution to (P), then there exists one that is an extreme point:
  * Good news: number of extreme points to consider is **finite**
  * Bad news : number of extreme points can be **exponential!**
* **Greed property** - extreme point optimal if and only if no better adjacent extreme point

### Simplex Algorithm
* **Simplex algorithm**:
  * Developed shortly after WWII in response to logistical problems, including Berlin airlift
  * Ranked as one of top 10 scientific algorithms of 20th century
* **Generic algorithm**:
  * Start at some extreme point
  * **Pivot** from one extreme point to an adjacent one
  * Repeat until optimal
* How do we implement either algorithm?
  * Linear algebra
* A **basis** is a subset of *m* of the *n* variables
* **Basic feasible solution (BFS)**:
  * Set *n – m* nonbasic variables to 0, solve for remaining *m* variables
  * Solve *m* equations in *m* unknowns
  * If unique and feasible ⇒ BFS
  * BFS ⇔ extreme point
* When to stop pivoting?
  * When no objective function coefficient is positive
* Why is resulting solution optimal?
  * Any feasible solution satisfies current system of equations