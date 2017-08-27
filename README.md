# Drais
A chess engine written in Haskell

A basic chess engine written in Haskell, using few external libraries or fancy features. As far as I know, Drais is the most complete chess engine written in Haskell thus far. Drais enjoys a blitz rating of about 1500-1600 on the FICS.

Drais employs an iterative-deepening approach, repeatedly calling an internal search routine with successively higher levels of depth, and retaining after each iteration a principal variation. The internal search routine itself performs the "NegaScout" or "PVS" variant of Alpha-Beta pruning upon each call, using move orderings informed by the most recently retained principal variation as well as by heuristic considerations.

The search depth parameter passed in each case into the main search routine represents the maximum search depth of its Floyd-style quiescence search (see Knuth and Moore, An Analysis of Alpha-Beta Pruning, p. 302), in which "likely" (i.e., capture) moves decrement the remaining search depth less than do unlikely moves.

Drais uses a heuristic evaluation function incorporating material, castling, advanced pawns, central rooks, etc. When compiled using GHC's -O2 optimization package, Drais achieves a (_maximal_) search depth of 10 half-moves or higher in just a few seconds.

For testing purposes during the earlier stages of the engine's development, I wrote a JavaScript GUI. Drais can be compiled into JavaScript using Haste; unfortunately, substantial slowdowns result. A live version of the applet can be found at http://www.math.jhu.edu/~bdiamond/chess/chess.html.

As it's currently written, the first argument of the main routine is the FEN-notation string of some game; the second is the search depth. Given these, Drais will compute and print a principal variation. Here is a sample input and output:
```
ghc --make -O2 Main.hs
./Main "1r5k/1b2K2p/7p/2pp3P/7B/5pp1/4p3/5r2 w - - 0 3" 12
_ r _ _ _ _ _ k
_ b _ _ K _ _ p
_ _ _ _ _ _ _ p
_ _ p p _ _ _ P
_ _ _ _ _ _ _ B
_ _ _ _ _ p p _
_ _ _ _ p _ _ _
_ _ _ _ _ r _ _
Turn: True, Castling: [True,True,True,True], Emp: (0,0),  Heuristic: -18.4, Eval: 6.779347985910482e18, Unlikelihood: 0
(6,1)'r' (5,2)'p' (7,3)'p' (6,3)'p' (8,4)'B' (8,5)'P' (4,5)'p' (3,5)'p' (8,6)'p' (8,7)'p' (5,7)'K' (2,7)'b' (8,8)'k' (2,8)'r'
_ r _ _ _ _ _ k
_ b _ _ _ K _ p
_ _ _ _ _ _ _ p
_ _ p p _ _ _ P
_ _ _ _ _ _ _ B
_ _ _ _ _ p p _
_ _ _ _ p _ _ _
_ _ _ _ _ r _ _
Turn: False, Castling: [True,True,True,True], Emp: (0,0),  Heuristic: -18.4, Eval: 7.136155774642613e18, Unlikelihood: 2
(6,7)'K' (6,1)'r' (5,2)'p' (7,3)'p' (6,3)'p' (8,4)'B' (8,5)'P' (4,5)'p' (3,5)'p' (8,6)'p' (8,7)'p' (2,7)'b' (8,8)'k' (2,8)'r'
_ _ _ _ _ _ r k
_ b _ _ _ K _ p
_ _ _ _ _ _ _ p
_ _ p p _ _ _ P
_ _ _ _ _ _ _ B
_ _ _ _ _ p p _
_ _ _ _ p _ _ _
_ _ _ _ _ r _ _
Turn: True, Castling: [True,True,True,True], Emp: (0,0),  Heuristic: -18.4, Eval: 7.511742920676435e18, Unlikelihood: 2
(7,8)'r' (6,7)'K' (6,1)'r' (5,2)'p' (7,3)'p' (6,3)'p' (8,4)'B' (8,5)'P' (4,5)'p' (3,5)'p' (8,6)'p' (8,7)'p' (2,7)'b' (8,8)'k'
_ _ _ _ _ _ r k
_ b _ _ _ K _ p
_ _ _ _ _ B _ p
_ _ p p _ _ _ P
_ _ _ _ _ _ _ _
_ _ _ _ _ p p _
_ _ _ _ p _ _ _
_ _ _ _ _ r _ _
Turn: False, Castling: [True,True,True,True], Emp: (0,0),  Heuristic: -18.4, Eval: 7.907097811238353e18, Unlikelihood: 2
(6,6)'B' (7,8)'r' (6,7)'K' (6,1)'r' (5,2)'p' (7,3)'p' (6,3)'p' (8,5)'P' (4,5)'p' (3,5)'p' (8,6)'p' (8,7)'p' (2,7)'b' (8,8)'k'
_ _ _ _ _ _ _ k
_ b _ _ _ K r p
_ _ _ _ _ B _ p
_ _ p p _ _ _ P
_ _ _ _ _ _ _ _
_ _ _ _ _ p p _
_ _ _ _ p _ _ _
_ _ _ _ _ r _ _
Turn: True, Castling: [True,True,True,True], Emp: (0,0),  Heuristic: -18.4, Eval: 8.323260853935109e18, Unlikelihood: 2
(7,7)'r' (6,6)'B' (6,7)'K' (6,1)'r' (5,2)'p' (7,3)'p' (6,3)'p' (8,5)'P' (4,5)'p' (3,5)'p' (8,6)'p' (8,7)'p' (2,7)'b' (8,8)'k'
_ _ _ _ _ _ _ k
_ b _ _ _ K B p
_ _ _ _ _ _ _ p
_ _ p p _ _ _ P
_ _ _ _ _ _ _ _
_ _ _ _ _ p p _
_ _ _ _ p _ _ _
_ _ _ _ _ r _ _
Turn: False, Castling: [True,True,True,True], Emp: (0,0),  Heuristic: -13.4, Eval: 8.761327214668536e18, Unlikelihood: 1
(7,7)'B' (6,7)'K' (6,1)'r' (5,2)'p' (7,3)'p' (6,3)'p' (8,5)'P' (4,5)'p' (3,5)'p' (8,6)'p' (8,7)'p' (2,7)'b' (8,8)'k'
_ _ _ _ _ _ _ _
_ _ _ _ _ _ _ _
_ _ _ _ _ _ _ _
_ _ _ _ _ _ _ _
_ _ _ _ _ _ _ _
_ _ _ _ _ _ _ _
_ _ _ _ _ _ _ _
_ _ _ _ _ _ _ _
Turn: True, Castling: [], Emp: (0,0),  Heuristic: 0.0, Eval: 9.22244969965109e18, Unlikelihood: 0
```
Drais uses a "lazy" legality checking technique whereby a move is not checked for legality until after its own visitation has begun and its (pseudo-)children have been generated. As determining the legality of a move requires knowing its pseudo-children (to determine whether the king can "get taken", etc.), delaying this step until the time of visitation facilitates parsimony and speed.

Differing from most minimax formulations, Drais does not determine a node's evaluation by simply "lifting" the evaluation of its best child. Drais instead uses a "dampening" function whereby a node's evaluation is determined by a weighted average of its best child's evaluation together with its own heuristic evaluation (where the former gets the vast majority of the weight). This "distance dampening" method has a number of advantages. For one, it provides a seamless way of convincing the engine to prefer to undertake a winning move or variation immediately, as opposed to (being indifferent to) delaying it with repeated checks or exchanges. Along a similar vein, it provides a parsimonious way of directing the engine to the shortest-possible mating (and longest-possible getting-mated) sequences. Finally, dampening has technical advantages with regard to the above "lazy" legality-checking scheme. As illegal positions are not deleted from the tree but merely given mating evaluations upon exit of visitation, dampening directs the engine to choose a legal (but eventually mated) position over a flatly illegal one.

This dampening procedure creates complications with respect to the Alpha-Beta and PVS algorithms. Indeed, the local parameters alpha and beta must serve to determine the suitability or rejectability of a child's eval with regard to the higher nodes in the tree against which it will eventually be compared, and comparison across levels is tricky because eval is affine-linearly transformed each time it is lifted. We adopt a procedure whereby the relevant alpha and beta parameters are inverted under a node's weighting function before being made to serve as benchmarks for that node's children. As the dampening function is bijective and order-preserving, order determinations made at the level of the child suffice for comparisons made at higher levels of the tree after all quantites are transformed.

Please contact me if you have any comments: bdiamond@math.jhu.edu
