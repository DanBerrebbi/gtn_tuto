# Concise tutorial on finite automaton and GTN librairy usage

[GTN](https://github.com/facebookresearch/gtn) is a C++ coded librairy with python bindings which provides easy tools to build finite automaton (FSA, WFSA, FST, WFST) and to perform operations on them. 
<br/> The advantage of finite automaton is that we can build complex models (build a CTC loss, an ASG criterion ...) from very basic graphs by doing operations on those simple graphs.
<br/> GTN provides an straightforward class that encompass finite automata operations, and most importantly, that enables automatic differentiation of those weighted automaton. With automatic differentiation enabled and a pytorch compatibility, we can integrate automaton and in particular WFST in our traditionnal models which leads to easier design of existing model and easier conception of new models or new losses.  <br/>


## List of useful links 
* [gtn official doc](https://gtn.readthedocs.io/en/latest/index.html) including installing steps. The installing procedure described [here](https://gtn.readthedocs.io/en/latest/install.html) worked well for psc.
* [The bible to learn on automatas and gtn](https://awnihannun.com/writing/automata_ml/automata_in_machine_learning.pdf). This artcile is very clear and precise, however it is quite long (70 pages) so I summarized some important parts of it here and added comments and examples for ASR.
* [gtn github](https://github.com/facebookresearch/gtn)

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
* for a start and acceptance node : g.add_node(start = True, accept = True) <br/>
Returns the id of the node.

__add an arc__ <br/>
```add_arc(self, src_node: int, dst_node: int, ilabel: int, olabel: int, weight: float = 0.0)``` <br/>
Returns the id of the arc.<br/>
NOTE that the labels should be integers so you should maintain a correspondance id to label somewhere 

__draw your graph__ <br/>
```draw(graph, file_name, isymbols={}, osymbols={})``` 
This function is very convenient to visualize the automata you are building. I recommend frequently saving graph as a .png file and check its shape.

## Operations on Finite automaton

### 1) Closure (Kleene)

* __Theory (for FSA) :__ <br/>

Let <a href="https://www.codecogs.com/eqnedit.php?latex=\mathcal{A}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\mathcal{A}" title="\mathcal{A}" /></a> be an acceptor and <a href="https://www.codecogs.com/eqnedit.php?latex=\mathcal{L(A)}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\mathcal{L(A)}" title="\mathcal{L(A)}" /></a> be the sequences accepted by A. <br/>

We denote <a href="https://www.codecogs.com/eqnedit.php?latex=\mathcal{A^*}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\mathcal{A^*}" title="\mathcal{A^*}" /></a> the closure of <a href="https://www.codecogs.com/eqnedit.php?latex=\mathcal{A}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\mathcal{A}" title="\mathcal{A}" /></a>. If a sequence x is accepted by A, then zero or more copies of x are accepted by the closure of A. Formally, we have <a href="https://www.codecogs.com/eqnedit.php?latex=\mathcal{L(A)}=\{&space;x^n&space;\;|\;&space;x\in\mathcal&space;L(A),&space;\;&space;\forall&space;n\in&space;\mathbb{N}\}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\mathcal{L(A)}=\{&space;x^n&space;\;|\;&space;x\in\mathcal&space;L(A),&space;\;&space;\forall&space;n\in&space;\mathbb{N}\}" title="\mathcal{L(A)}=\{ x^n \;|\; x\in\mathcal L(A), \; \forall n\in \mathbb{N}\}" /></a> where <a href="https://www.codecogs.com/eqnedit.php?latex=x^n" target="_blank"><img src="https://latex.codecogs.com/gif.latex?x^n" title="x^n" /></a> denotes the concatenation of n sequences x.

* __gtn for closure__   : ```closure(g)```  <br/>
Takes a graph (automata) as input and outputs its closure.  <br/>

* __Why closures ? Example of usage__  <br/>
If you need to align frames to letters while training your asr system, several consecutive frames may be aligned with the letter "a". So if you are using a FSA for accepting the letter "a", you may consider using its closure in order to allow several frames to align to the same letter. Building a simple graph and computing its closure through gtn avoid building a closure graph by yourself which can be more painful.


### 2) Union 

* __Theory :__ 

Let <a href="https://www.codecogs.com/eqnedit.php?latex={A_i},&space;for&space;\;&space;i\in&space;[1,n]&space;\cap&space;\mathbb{N}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?{A_i},&space;for&space;\;&space;i\in&space;[1,n]&space;\cap&space;\mathbb{N}" title="{A_i}, for \; i\in [1,n] \cap \mathbb{N}" /></a> be n finite state acceptors. We denote <a href="https://www.codecogs.com/eqnedit.php?latex=\mathcal{A}_1&space;&plus;&space;\mathcal{A}_2&space;&plus;&space;...&space;&plus;&space;\mathcal{A}_n" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\mathcal{A}_1&space;&plus;&space;\mathcal{A}_2&space;&plus;&space;...&space;&plus;&space;\mathcal{A}_n" title="\mathcal{A}_1 + \mathcal{A}_2 + ... + \mathcal{A}_n" /></a> their union.   <br/>

The vocabulary of the union of the acceptors is the union of the acceptors' vocabularies. Formally, <a href="https://www.codecogs.com/eqnedit.php?latex=\mathcal{L}(\mathcal{A}_1&space;&plus;&space;\mathcal{A}_2&space;&plus;&space;...&space;&plus;&space;\mathcal{A}_n)&space;=&space;\{x\;&space;|&space;\;&space;\exists&space;i&space;\;&space;s.t.&space;\;&space;x\in&space;\mathcal{A}_i&space;\}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\mathcal{L}(\mathcal{A}_1&space;&plus;&space;\mathcal{A}_2&space;&plus;&space;...&space;&plus;&space;\mathcal{A}_n)&space;=&space;\{x\;&space;|&space;\;&space;\exists&space;i&space;\;&space;s.t.&space;\;&space;x\in&space;\mathcal{A}_i&space;\}" title="\mathcal{L}(\mathcal{A}_1 + \mathcal{A}_2 + ... + \mathcal{A}_n) = \{x\; | \; \exists i \; s.t. \; x\in \mathcal{A}_i \}" /></a>. 


* __gtn for union__   : ```union(graphs)``` <br/>
Takes a list of graphs as input and returns the union of those graphs.

* __Why union ? Example of usage__  <br/>
If you want an acceptor which vocabulary is a sequence of a and b. Then you can just build an acceptor that accepts a, one that accepts b, compute their union and then compute the closure of that union so that you can accept any sequence composed of a and b.

* __BE CAREFUL__  The closure of the union is not equal to the union of the closure.

### 3) Concatenation 

* __Theory :__ 

The vocabulary of a concatenation of acceptors is the set of strings that can be formed by any concatenation of strings from the individual acceptors. 
We denote <a href="https://www.codecogs.com/eqnedit.php?latex=\mathcal{A}_1\mathcal{A}_2&space;...&space;\mathcal{A}_n" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\mathcal{A}_1\mathcal{A}_2&space;...&space;\mathcal{A}_n" title="\mathcal{A}_1\mathcal{A}_2 ... \mathcal{A}_n" /></a> the conctenation of the acceptors. Formally, we have <a href="https://www.codecogs.com/eqnedit.php?latex=\mathcal{L}(\mathcal{A}_1\mathcal{A}_2&space;...&space;\mathcal{A}_n)&space;=&space;\{x_1x_2...x_n\;&space;|&space;\;&space;\forall&space;i&space;\;&space;x_i\in&space;\mathcal{A}_i&space;\}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\mathcal{L}(\mathcal{A}_1\mathcal{A}_2&space;...&space;\mathcal{A}_n)&space;=&space;\{x_1x_2...x_n\;&space;|&space;\;&space;\forall&space;i&space;\;&space;x_i\in&space;\mathcal{A}_i&space;\}" title="\mathcal{L}(\mathcal{A}_1\mathcal{A}_2 ... \mathcal{A}_n) = \{x_1x_2...x_n\; | \; \forall i \; x_i\in \mathcal{A}_i \}" /></a>

* __gtn for concatenation__   : ```concat(graphs)``` <br/>
Takes a list of graphs as input and returns the union of those graphs.

* __Why concatenation ? Example of usage__  <br/>

In the ASR training setting, we need to recognize the word "yes". The accepted alignements would be any sequence of repeated "y", followed by a sequence of "e", followed by a sequence of "s". This (infinite) set of sequences can be easilly built by taking the closure of the 3 acceptors of the 3 letters and then compute the concatenation of these closures.

* __BE CAREFUL__  The closure of the concatenation is not equal to the concatenation of the closure.

* __BE CAREFUL__  The order of the acceptors matters in the concatenation operation.


### 4) Intersection

* __Theory :__ 
The intersection of two acceptors is the set of strings which is acepted by both of them.
More formaly, <a href="https://www.codecogs.com/eqnedit.php?latex=\mathcal{L}(\mathcal{A}_1&space;\circ&space;\mathcal{A}_2)&space;=&space;\{x\;|\;x\in\mathcal{A}_1\;&space;and&space;\;&space;x\in&space;\mathcal{A}_2&space;\}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\mathcal{L}(\mathcal{A}_1&space;\circ&space;\mathcal{A}_2)&space;=&space;\{x\;|\;x\in\mathcal{A}_1\;&space;and&space;\;&space;x\in&space;\mathcal{A}_2&space;\}" title="\mathcal{L}(\mathcal{A}_1 \circ \mathcal{A}_2) = \{x\;|\;x\in\mathcal{A}_1\; and \; x\in \mathcal{A}_2 \}" /></a>.
We use <a href="https://www.codecogs.com/eqnedit.php?latex=\mathcal{A}_1&space;\circ&space;\mathcal{A}_2" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\mathcal{A}_1&space;\circ&space;\mathcal{A}_2" title="\mathcal{A}_1 \circ \mathcal{A}_2" /></a> to denote the intersection of the two acceptors. (We will use the same notation for composition as it is the generalization of intersection but for transducers).

* __gtn for intersection__   :```intersect(g1, g2)``` <br/>

* __Why intersection ? Example of usage__  <br/>
Build a shared vocabulary.


### 5) Composition

* __Theory :__
Let ```FST1``` and ```FTS2``` be two transducers. Assume that ```FST1``` transduces the sequence x to the sequence y and ```FST2``` transduces the sequence y to the sequence z. Then the composition of  ```FST1``` and ```FST2``` (order matters contrairy to intersection) tranduces x to z. 


* __gtn for intersection__   : ```compose(g1, g2)``` <br/>


* __Why composition ? Example of usage__  <br/>

Put together the different components of your model (I'll give more detailled example a bit later with CTC loss)


## Advanced usage of automaton

The operations described in the previous section enable to build very complex automaton from tiny graphs through easy operations. This is the strength of finite state automaton. We will now describe more advanced operations and usage you can do with those graphs. 

* compute the forward score of a graph (sum of the weight of all possible path from start state to end state) ```forward_score(g)```
* compute the Viterbi score and Path  ```viterbi_score(g)``` , ```viterbi_path(g)```
* enable gradient computation to backpropagate a loss and train the weights for graphs (train the emission weights for instance...)



## More useful functions in gtn 

* ```linear_graph(M, N, calc_grad=True)``` Create a linear chain graph with M + 1 nodes and N edges between each node.
* ```equal(g1, g2)``` Checks if two graphs are exactly equal (not isomorphic) (```isomorphic(g1, g2)```).
* ... more on the official doc


<br/>
<br/>

This small summary will be completed soon with more examples, in particular building useful graphs for ASR. <br/>
