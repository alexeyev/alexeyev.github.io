---
title: urllib2, matplotlib,Hyperlinks graph drawing code
description: Code preparing a graph of links found on a provided page, etc. The comments to the code are in Russian.
---
Код, отрисовывающий ссылочную структуру по данной странице и ссылкам на ней и т.д..

```python
# coding:utf-8
"""
 Код, отрисовывающий граф ссылок, начиная с данной
 PYTHON 2   
"""

import matplotlib.pyplot as plt
import urllib2
import networkx as nx
from BeautifulSoup import BeautifulSoup

request_queue = [(1, "", "START")]
edges = []
depth = 100

# BFS
while True:
    # выбиваем и берём из списка очередную ссылку для обхода
    depth, current_request, current_name = request_queue.pop(0)
    
    if depth <= 0:
        break
    
    # print request_queue
    page = urllib2.urlopen('http://vesna.nologin.ru' + current_request).read().decode("utf-8")
    soup = BeautifulSoup(page)
    links = soup.findAll('a')
    print len(links), current_name

    # для каждой ссылки на странице
    for link in links:
        edges.append((current_name, link.string))
        request_queue.append((depth - 1, link.get("href"), link.string))

# направленный граф
G = nx.DiGraph()
G.add_edges_from(edges)

# распечатываем "физично"
pos = nx.spring_layout(G)

# A = nx.nx_agraph.to_agraph(G)
# pos = A.layout('dot', args='-Nfontsize=10 -Nwidth=".2" -Nheight=".2" -Nmargin=0 -Gfontsize=8')
nx.draw(G, pos, font_size=10, with_labels=True)
plt.show()
```
