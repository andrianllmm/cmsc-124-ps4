# The Search and the Stack
## The Trace
We’ll trace P07, which searches for grandparent(tom, X).


```
== trace P07 ==
Call: grandparent(tom,X)
	Call: parent(tom,_)
	Exit: parent(tom,liz)
	Call: parent(liz,X)
	Fail: parent(liz,X)
	Redo: parent(tom,liz)
	Exit: parent(tom,bob)
	Call: parent(bob,X)
	Exit: parent(bob,ann)
Exit: grandparent(tom,ann)
answer 1: X=ann
Redo: grandparent(tom,ann)
	Redo: parent(bob,ann)
	Exit: parent(bob,pat)
Exit: grandparent(tom,pat)
answer 2: X=pat
Redo: grandparent(tom,pat)
	Redo: parent(bob,pat)
	Fail: parent(bob,X)
	Redo: parent(tom,bob)
	Fail: parent(tom,_)
Fail: grandparent(tom,X)
== 2 answers ==
```


## Which lines push a frame, and which pop one

Every call pushes a frame. Prolog is starting a new goal, so it needs a new frame for it, just like a function call. Fail lines pop one. Exit means the goal worked and hands its bindings back to the goal that called it, and it pops the frame only if no alternatives remain. Fail means the goal ran out of clauses, so its frame gets thrown away. The indentation shows this too. Call: grandparent(tom, X) is at the top level, and its two parent goals get pushed one level deeper. When parent(liz,X) fails, its frame pops and the search goes back up a level. The last line, Fail: grandparent(tom,X), pops the very first frame, so the stack is empty and the search ends with 2 answers.



## What a Redo resumes

A Redo line resumes a goal that already exited. That goal still knows which clauses it hasn't tried yet, and which bindings it made, so it can undo them. A returned frame in Activity 15 had thrown all of that away. Nothing could come back to it. In the trace, Redo: parent(tom,liz) undoes the binding to liz and Exit: parent(tom,bob) tries the next fact. Likewise Redo: parent(bob,ann) leads to Exit: parent(bob,pat). This remembered record is the choice point.



## Why swapping clauses matters for reach/2 but not ancestor/2

The recursive clause of reach/2 calls itself before it reads any edge. Since it comes first, reach(a,d) calls reach(a,Z) again and again. Frames keep getting pushed, but none ever pop, because no call finishes, so it never reaches an edge and never answers. If the base clause came first, edge/2 would run first, so it could find a path to d and answer true, even though it still loops after. ancestor/2 is different because both clauses read a parent fact first, so every call goes one generation down and stops at the bottom of the tree. Swapping them only changes the order, like P08 and P09.
