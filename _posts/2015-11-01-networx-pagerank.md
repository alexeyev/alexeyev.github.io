---
title: PageRank using NetworkX
---

```python
import networkx as nx

edges = [(0, 1), (1, 2), (2, 0)]
graph = nx.Graph()

for fromm, too in edges:
    graph.add_edge(fromm, too)

pr_values = {}

for vertex, pr_value in nx.pagerank_scipy(graph).iteritems():
    print vertex, "has PR", pr_value
```

```
0 has PR 0.333333333333
1 has PR 0.333333333333
2 has PR 0.333333333333
```
