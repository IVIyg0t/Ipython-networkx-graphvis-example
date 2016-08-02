# Ipython-networkx-graphvis-example

# Install Libraries

Please install Homebrew if you're running on OSX.  You will need it to get pygraphviz to install correctly.

`pip install networkx numpy scipy matplotlib pyyaml`
`brew install graphviz`
`pip install --global-option=build_ext --global-option="-I/usr/local/Cellar/graphviz/2.38.0/include/"  --global-option="-L/usr/local/Cellar/graphviz/2.38.0/lib/" pygraphviz`

# Create a graph
```python
import networkx as nx
G = nx.Graph()
```
## Create a graph with an attribute
```python
G = nx.Graph(day="Friday")
G.graph
```

### Modify the attribute later
```python
G.graph['day'] = 'Monday'
G.graph
```

## Add nodes to the graph
```python
G.add_node(1)

# Alternatively add nodes using strings
G.add_node('House Security')
```

### Add a list of nodes to the graph
```python
G.add_nodes_from([2,3])
```

### Or add any 'nbunches' of nodes
```python
H = nx.path_graph(10)
G.add_nodes_from(H)
```

## Add edges to the graph
```python
G.add_edge(1,2)
e = (2,3)
G.add_edge(*e)
```

### Add a list of edges
```python
G.add_edges_from([
		(1,2),
		(1,3)
	])
```

## Display graph data
```python
print G.number_of_nodes()
print G.number_of_edges()
```

# Draw a graph
```python
import matplotlib.pyplot as plt
nx.draw(G)
plt.show()
```

# Directed graphs
```python
dg = nx.DiGraph()
nodes = ['House Security',
         'Garage Security',
         'Material',
         'Uniqueness of Opener',
         'Window Security',
         'Material',
         'Location and Height',
         'Latched',
         'Door Security',
         'Locked',
         'Spare Key',
         'Homeowner',
         'Location',
         'Fence Security',
         'Height',
         'Neighborhood',
         'Awareness of Neighbors',
         'Traffic on Street',
         'Number of Houses']
edges = [
    ('Garage Security','House Security'),
    ('Material','Garage Security'),
    ('Uniqueness of Opener','Garage Security'),
    ('Window Security','House Security'),
    ('Material','Window Security'),
    ('Location and Height','Window Security'),
    ('Latched','Window Security'),
    ('Door Security','House Security'),
    ('Locked','Door Security'),
    ('Spare Key','Door Security'),
    ('Homeowner','Spare Key'),
    ('Homeowner','Locked'),
    ('Homeowner','Latched'),
    ('Location','Door Security'),
    ('Material','Door Security'),
    ('Fence Security','House Security'),
    ('Material','Fence Security'),
    ('Height','Fence Security'),
    ('Neighborhood','House Security'),
    ('Awareness of Neighbors','Neighborhood'),
    ('Traffic on Street','Neighborhood'),
    ('Number of Houses','Neighborhood')]
dg.add_nodes_from(nodes)
dg.add_edges_from(edges)
```
## Convert Graph to a GraphViz object (for better visualization)

- **dot** : "heirerchican" or layed drawings of directed graphs. This is the default to use if edges have direction.
- **neato** : "spring model" layouts.  This is the default tool to use if the graph is not too large (about 100 nodes) and you don't know anything else about it.  Neato attempts to minimize a global energy function, which is equivalent to statistical multi-dimensional scaling.
- **fdp** : "spring model" layouts similart to those of neato, but does this by reducing forces rather than working with energy.
- **sfdp** : multiscale version of fdp for the layout of large graphs.
- **twopi** : radial layouts, after Graham Will 97.  Nodes are placed on concentric circles depending on their distance from a given root node.
- **circo** : circular layout, after Six and Tollis 99, Kauffman and Wiese 02.  This is suitable for certain diagrams 
of multiple cyclic structures such as certain telecommunications networks.

```python
graphFile = 'test.png'
A = nx.drawing.nx_agraph.to_agraph(dg)
A.layout(prog='dot')
A.draw(graphFile)
```

## Open the image in IPython
```python
from IPython.display import Image, display
display(Image(graphFile)
```

## Output a graph to YAML structure
```python
nx.write_yaml(dg,'test.yaml')
```

# Graph Measurements

## Katz Centrality

Katz centrality[30] is a generalization of degree centrality. Degree centrality measures the number of direct neighbors, and Katz centrality measures the number of all nodes that can be connected through a path, while the contributions of distant nodes are penalized.  Katz centrality can be viewed as a variant of eigenvector centrality.

<a href="https://en.wikipedia.org/wiki/Centrality">Source</a>

```python
import math
phi = (1 + math.sqrt(5))/2.0 # largest eigenvalue of adj matrix
centrality = nx.katz_centrality(dg,1/phi-0.01)
for n,c in sorted(centrality.items()):
    print("%s %0.2f"%(n,c))
```

## Degree Centrality

Historically first and conceptually simplest is degree centrality, which is defined as the number of links incident upon a node (i.e., the number of ties that a node has). The degree can be interpreted in terms of the immediate risk of a node for catching whatever is flowing through the network (such as a virus, or some information). In the case of a directed network (where ties have direction), we usually define two separate measures of degree centrality, namely indegree and outdegree. Accordingly, indegree is a count of the number of ties directed to the node and outdegree is the number of ties that the node directs to others. When ties are associated to some positive aspects such as friendship or collaboration, indegree is often interpreted as a form of popularity, and outdegree as gregariousness.
<a href="https://en.wikipedia.org/wiki/Centrality">Source</a>

```python
centrality = nx.degree_centrality(dg)
for n,c in sorted(centrality.items()):
    print("%s %0.2f"%(n,c))
```

## In-Degree Centrality
```python
centrality = nx.in_degree_centrality(dg)
for n,c in sorted(centrality.items()):
    print("%s %0.2f"%(n,c))
```

## Out-Degree Centrality
```python
centrality = nx.out_degree_centrality(dg)
for n,c in sorted(centrality.items()):
    print("%s %0.2f"%(n,c))
```

## Closeness Centrality

In the classic definition of the closeness centrality, the spread of information is modeled by the use of shortest paths. This model might not be the most realistic for all types of communication scenarios. Thus, related definitions have been discussed to measure closeness, like the random walk closeness centrality introduced by Noh and Rieger (2004). It measures the speed with which randomly walking messages reach a vertex from elsewhere in the graph—a sort of random-walk version of closeness centrality.[24] Hierarchical closeness of Tran and Kwon (2014)[25] is an extended closeness centrality to deal still in another way with the limitation of closeness in graphs that are not strongly connected. The hierarchical closeness explicitly includes information about the range of other nodes that can be affected by the given node. <a href="https://en.wikipedia.org/wiki/Centrality">Source</a>

```python 
centrality = nx.closeness_centrality(dg)
for n,c in sorted(centrality.items()):
    print("%s %0.2f"%(n,c))
 ```
 
 ## Betweenness Centrality

Betweenness centrality quantifies the number of times a node acts as a bridge along the shortest path between two other nodes. It was introduced as a measure for quantifying the control of a human on the communication between other humans in a social network by Linton Freeman[26] In his conception, vertices that have a high probability to occur on a randomly chosen shortest path between two randomly chosen vertices have a high betweenness. <a href="https://en.wikipedia.org/wiki/Centrality">Source</a>

```python
centrality = nx.betweenness_centrality(dg)
for n,c in sorted(centrality.items()):
    print("%s %0.2f"%(n,c))
```

## Edge Betweenness Centrality
```python
centrality = nx.edge_betweenness_centrality(dg)
for n,c in sorted(centrality.items()):
    print("%s %0.2f"%(n,c))
```