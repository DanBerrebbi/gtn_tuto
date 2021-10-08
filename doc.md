

graph creation : <br/>
```g=gtn.Graph()```

add a node :  <br/>
```g.add_node(self, start: bool = False, accept: bool = False)``` 

* for a start node : g.add_node(start = True)
* for an internal node : g.add_node()
* for an acceptance node : g.add_node(start = False, accept = True)
* for a start and acceptance node : g.add_node(start = True, accept = True)


add an arc : <br/>
```add_arc(self, src_node: int, dst_node: int, ilabel: int, olabel: int, weight: float = 0.0)```

draw your graph : : <br/>
```draw(graph, file_name, isymbols={}, osymbols={})``` 
