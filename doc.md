

graph creation : 
```g=gtn.Graph()```

add a node : 
```g.add_node(self, start: bool = False, accept: bool = False)``` 
params : 
* for a start node : g.add_node(start = True)
* for an internal node : g.add_node()
* for an acceptance node : g.add_node(start = False, accept = True)
* for a start and acceptance node : g.add_node(start = True, accept = True)
