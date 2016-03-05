---
layout: post
title:  "Uninformed search algorithms in Python"
date:   2016-3-4
description:  The main uninformed search strategies are three. Depth-First Search or DFS Breadth-First Search or BFS Uniform Cost Search or UCS.
---

Overall, graph search can fall either under the uninformed or the informed category. The difference between the two is that the first one (uninformed) is naive or blind - meaning it has no knowledge of where the goal could be, while the second one (informed) uses heuristics to guide the search.

The main **uninformed search strategies** are three:

* Depth-First Search or DFS
* Breadth-First Search or BFS
* Uniform Cost Search or UCS

## Making graphs

These algorithms can be applied to traverse graphs or trees. To represent such data structures in Python, all we need to use is a dictionary where the vertices (or nodes) will be stored as keys and the adjacent vertices as values.

```python
small_graph = {
    'A': ['B', 'C'],
    'B': ['D', 'A'],
    'C': ['A'],
    'D': ['B']
}
```

Alternatively we can create a Node object with lots of attributes, but we'd have to instantiate each node separately, so let's keep things simple. We will use the plain dictionary representation for DFS and BFS and later on we'll implement a Graph class for the Uniform Cost Search.

Keep in mind that we can represent both directed and undirected graphs easily with a dictionary. In the case our small graph was directed it would maybe look something like this.

```python
small_graph = {'A': ['B', 'C'], 'B':['D'], 'C':[], 'D':[]}
```


## Depth-First Search

All of the search algorithms will take a graph and a starting point as input. Having a goal is optional.
Depth-First Search will visit the first adjacent vertex of the starting point and then repeat the same process until it reaches the very bottom of the branch and then it will finally start backtracking. DFS can be implemented using recursion, which is fine for small graphs, or a safer option is iteration.

A couple of things we need to do in this algorithm is keep track of which vertices we have visited, and also keep track of the *fringe*. The fringe (or frontier) is the collection of vertices that are available for expanding. We will store the fringe in a list commonly called "stack", referring to its ability to pop items from the tail.

```python
def dfs(graph, start, goal):
    visited = set()
    stack = [start]

    while stack:
        node = stack.pop()
        if node not in visited:
            visited.add(node)

            if node == goal:
                return
            for neighbor in graph[node]:
                if neighbor not in visited:
                    stack.append(neighbor)
```

## Breadth-First Search

It's totally possible to implement BFS with just changing one character from the DFS function above. Let's see why.

The BFS algorithm instead of following a branch down to the bottom, will visit all the vertices of the same depth before moving on deeper. So when choosing which vertex to expand next, it will choose the oldest item on the fringe, unlike DFS which chooses the newest. That's why DFS uses a stack and pops items from the tail, while BFS uses a queue and pops items from the front. So by modifying this line

```python
node = stack.pop(0)
```

we now execute a Breadth-First Search. We can make this more efficient though. To cut down on the cost of `pop(0)` we can use a double ended queue called deque. This allows us to append items to both ends.

```python
from collections import deque

def bfs(graph, start, goal):
    visited = set()
    queue = [start]

    while queue:
        node = queue.pop()
        if node not in visited:
            visited.add(node)

            if node == goal:
                return
            for neighbor in graph[node]:
                if neighbor not in visited:
                    queue.appendleft(neighbor)
```
That way, we're appending to the list in reverse order so the item in the tail is the oldest and not the newest.

## Uniform Cost Search

This search strategy is for weighted graphs. Each edge has a weight, and vertices are expanded according to that weight; specifically, cheapest node first. As we move deeper into the graph the cost accumulates. Check out [Artificial Intelligence - Uniform Cost Search](https://algorithmicthoughts.wordpress.com/2012/12/15/artificial-intelligence-uniform-cost-searchucs/) if you are not familiar with how UCS operates.

The algorithm needs to know the cost of moving from one vertex to another. On top of that, it needs to know the cumulative cost of the path so far. We'll use a Graph class for UCS, although not absolutely necessary, I want to cover this case and as a plus we keep things a little cleaner.

```python
class Graph:
    def __init__(self):
        self.edges = {}
        self.weights = {}

    def neighbors(self, node):
        return self.edges[node]

    def get_cost(self, from_node, to_node):
        return self.weights[(from_node + to_node)]
```
Now we can instantiate a graph by making a dictionary for the edges (just like the one before) and a dictionary for the edge weights.

The priority in which vertices are expanded is cheapest-first, so we need to turn our plain queue into a priority queue. For that we'll use Python's PriorityQueue. So we'll add this to the top.

```python
from queue import PriorityQueue
```

In a way, UCS is very similar to the Breadth-First algorithm; in fact BFS is UCS when all the edge weights are equal. So the implementation will be similar to the previous two. We still use the visited set, while the queue becomes a PriorityQueue that takes tuples in the form of (cost, vertex), which describes the cost of moving to the next vertex.

```python
def ucs(graph, start, goal):
    visited = set()
    queue = PriorityQueue()
    queue.put((0, start))

    while queue:
        cost, node = queue.get()
        if node not in visited:
            visited.add(node)

            if node == goal:
                return
            for i in graph.neighbors(node):
                if i not in visited:
                    total_cost = cost + graph.get_cost(node, i)
                    queue.put((total_cost, i))
```

At the start of our main loop we also have a cost variable, which will be the cumulative cost for each node, the one we compute right before appending a neighboring node to the fringe at the very end of the algorithm.

## Shortest and cheapest paths

Uniform Cost Search will reach the goal in the cheapest way possible. Breadth-First Search will reach the goal in the shortest way possible. Depth-First Search is not optimal and is not guaranteed to reach the goal cheaply or shortly.

In order to modify our two optimal algorithms to return the best path, we have to replace our visited set with a `came-from` dictionary. We can then reconstruct the best path and return it.

<br>

I highly recommend reading these two articles:

* [Introduction to A*](http://www.redblobgames.com/pathfinding/a-star/introduction.html)
* [Implementation of A*](http://www.redblobgames.com/pathfinding/a-star/implementation.html)

They build up to A* search (which uses heuristics) by giving lots and lots of awesome info about BFS and UCS (as Dijkstra's algorithm).

I also recommend checking out the [simpleai](https://github.com/simpleai-team/simpleai) Python library.
