# Concise tutorial on finite automaton and GTN librairy usage

[GTN](https://github.com/facebookresearch/gtn) is a C++ coded librairy with python bindings which provides easy tools to build finite automaton (FSA, WFSA, FST, WFST) and to perform operations on them. 
<br/> The advantage of finite automaton is that we can build complex models (build a CTC loss, an ASG criterion ...) from very basic graphs by doing operations on those simple graphs.
<br/> GTN provides an straightforward class that encompass finite automata operations, and most importantly, that enables automatic differentiation of those weighted automaton. With automatic differentiation enabled and a pytorch compatibility, we can integrate automaton and in particular WFST in our traditionnal models which leads to easier design of existing model and easier conception of new models or new losses.  <br/>


## Finite automaton

We assume that the reader knows the basics of Finite State Acceptors (FSA), Finite State Trasducer (FST) and their weighted versions (WFSA and WFST).

We list the gtn functions to build those finite automaton. <br/>

__graph creation__  <br/>
```g=gtn.Graph(calc_grad=True)```

__add a node__  <br/>
```g.add_node(self, start: bool = False, accept: bool = False)``` 

* for a start node : g.add_node(start = True)
* for an internal node : g.add_node()
* for an acceptance node : g.add_node(start = False, accept = True)
* for a start and acceptance node : g.add_node(start = True, accept = True)


__add an arc__ <br/>
```add_arc(self, src_node: int, dst_node: int, ilabel: int, olabel: int, weight: float = 0.0)``` <br/>

NOTE that the labels should be integers so you should maintain a correspondance id to label somewhere 

__draw your graph__ <br/>
```draw(graph, file_name, isymbols={}, osymbols={})``` 
This function is very convenient to visualize the automata you are building. I recommend frequently saving graph as a .png file and check its shape.

## Operations on Finite automaton

Notation : Let <a href="https://www.codecogs.com/eqnedit.php?latex=\mathcal{A}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\mathcal{A}" title="\mathcal{A}" /></a> be an acceptor and <a href="https://www.codecogs.com/eqnedit.php?latex=\mathcal{L(A)}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\mathcal{L(A)}" title="\mathcal{L(A)}" /></a> be the sequences accepted by A.

### Closure (Kleene)

Theoory (for FSA) : We denote <a href="https://www.codecogs.com/eqnedit.php?latex=\mathcal{A^*}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\mathcal{A^*}" title="\mathcal{A^*}" /></a> the closure of <a href="https://www.codecogs.com/eqnedit.php?latex=\mathcal{A}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\mathcal{A}" title="\mathcal{A}" /></a>. If a sequence x is accepted by A, then zero or more copies of x are accepted by the closure of A. Formally, we have <a href="https://www.codecogs.com/eqnedit.php?latex=\mathcal{L(A)}=\{&space;x^n&space;\;|\;&space;x\in\mathcal&space;L(A),&space;\;&space;\forall&space;n\in&space;\mathbb{N}\}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\mathcal{L(A)}=\{&space;x^n&space;\;|\;&space;x\in\mathcal&space;L(A),&space;\;&space;\forall&space;n\in&space;\mathbb{N}\}" title="\mathcal{L(A)}=\{ x^n \;|\; x\in\mathcal L(A), \; \forall n\in \mathbb{N}\}" /></a> where <a href="https://www.codecogs.com/eqnedit.php?latex=x^n" target="_blank"><img src="https://latex.codecogs.com/gif.latex?x^n" title="x^n" /></a> denotes the concatenation of n sequences x.

__gtn for closure__   : ```closure(g)```
 <br/>
 It takes a graph (automata) as input and outputs its closure. 
