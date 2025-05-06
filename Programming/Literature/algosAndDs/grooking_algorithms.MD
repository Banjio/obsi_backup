# Grookign Algorithms 

# Chapter 1

## Binary Search

* Searches an Element in a __sorted__ list. Because the list is sorted if an element is not found in one half of the list only the right/left side from the middle need to further be searched. 
* Because of this Binary Search has a Running TIme of _O(log n)_

## Efficiency of an algorithms

* 2 factors are relevant when talking about the efficiency of an algorithm, __time__ and __space__. 

### Running Time

* The time efficiency of an algorithm is usually measured with the __Big O Notation__. 
* Big O measures the time an algorithm needs to complete n operations. For example, simple search (Which checks each element of a list) has a linear runnning time, thus checking 100 elements of a list takes 100 operations whereas binary search only takes 7 operations (log(100)) &Rightarrow; this may not seem much, but note that for large lists this makes the difference between seconds and days.
* Thus it measures the growth of the number of operations
* Furthermore Big O measures the runtime in the worst case scenario of an algorithm. E.g. in simple serch if the element to be searched is first in the list (Worst case would be the end of the list &Rightarrow; n-th element, thus _O(n)_) 
* But the average runtime may also be relevant in some scenarios. This is discussed later in the book

#### Common Big O notations

* _O(log n)_: log time, e.g. binary search
* _O(n)_: linear time, e.g. simple search
* _O(n * log n)_: Fast sorting algorithms like quicksort
* _O(n²)_: Slow sorting algorithms, e.g. selection srt
* _O(n!)_: Very slow algorithms, e.g. Traveling salesperson

## Traveling salesperson

* A salesperson wants to travel the shortest distance between n cities. 
* To find that he has to calculate the distance between each combination of the n cities &Rightarrow _n!_. 
* This problem is one of the unsolved in cs, because so far no faster algorithm could be found, the only solution is to calculate an approximation discussed later

# Chapter 2

## Arrays and Linked lists

... 

## Selection Sort

* Traverse trough list, find maximum --> New first element, Then search remainder for largest element and so on until the list is sorted

# Recursion

* Recursion always has a base case and a recursive case. The base case breaks out of recursion thus ending the calculation, for example:

```
def recursive_sum(arr: list):
    # Base case
    if len(list) == 0:
        return 0
    # Recursive case
    else:
        list[0] + recursive_sum(arr[1:])
```

# Quicksort

* Sorting algorithm using recursion. 
  1. Find a random pivot (any element from the list)
  2  Divide the list into elements larger than the pivot and smaller then the pivot
  3. Use quicksort on both lists created from the pivot 
   

```
// Note that we miss the random selection of the pivot, but it is strongly encouraged to pick a random element
func Quicksort(arr []float64) []float64 {
	if len(arr) < 2 {
		return arr
	} else {
		pivot := arr[0]
		searchArr := arr[1:]
		var larr []float64
		var rarr []float64
		for _, item := range searchArr {
			if item <= pivot {
				larr = append(larr, item)
			} else {
				rarr = append(rarr, item)
			}
		}
		return append(append(Quicksort(larr), pivot), Quicksort(rarr)...)
	}
```
# Hash Tables

* Very efficient data structure for many classical operations of arrays or linkes lists (i.e. inserting, selection, ...)
* Combining an array with hash functions gives a hash table (Dictionaries in Python)
* Collisions, meaning that the hash function uses the same hash for different items (creating a linked list that must be searched) are bad. Thus a hash function that creates as less collisions as possible is optimal
* With a load factor (Size used of the underlying array) is above 0.7 the table (array) must be resized
* Typical application of hash tables are caching or removing duplicates

# Graphs

* A very useful data structure for modeling complex relationships

For a simple implementation in Python the book uses the dictionary. If we want to write our own graph class in python we need adjacency lists

## Adjacency lists

* Collection of unordered lists too represent a (undirected) graph. Each unordered list describes the neighbors of a particular vertex
* The graph  c <-> a <-> b <-> c would be described by {b, c}, {a, c}, {a, b}

### Implementation Details
Wikipedia: https://en.wikipedia.org/wiki/Adjacency_list


* An adjacency list representation associates each vertex in a graph with its neighboring vertices or edges
* Many variations exist of this idea, differin in detail of how they implement the association between vertices and colection or in wether they include both vertices and edges or not

* __Guido van Rossum__: Use hash table to associate each vertex with an array of adjacent vertices. Thus a vertice can be represented by any hashable object (Only strings or integers in Python dictionaries) &Rightarrow; No representation for edges. Implementation in Python https://www.python.org/doc/essays/graphs/
* __Cormen et al__: Vertices are represented by index numbers. Thus they use an array where the index is a vertex and contains a linked list of the neighboring vertices. Thus the node of the singly linked list can be interpreted as an edge, however they do not store the whole information of an edge (only of of the two endpoints). In an undirected graph ther will always be two differen linkes list for each edge (one for each endpoints of the list) https://realpython.com/linked-lists-python/ &Rightarrow; article about linked lists in python which probably uses this approach to implement a graph 
* __Goodrich and Tamassia__: Propose a object oriented list structure. Each vertex object has an instance variable pointing to a collection object that lists the neighboring edge objects. In turn, each edge object points to two vertex objects as its endpoints. On the downside it uses more memory than other versions but can store additional information about edges

The alternative for adjacency lists are adjacency matrices

#### Excourse Linked lists in python

This article https://realpython.com/linked-lists-python/ goes in great detail about linked lists

__Main concept__

* Each element in a linked list is called a node and each node has 2 fields, __Data__ that has a value stored in the node and __Next__ containing a reference to the next node in the list

* Real world applications of linked lists are queues and stacks differing only in the way elements are taken from this data structure FIFO (Taking from the Front and appending at the Rear of the queue) vs. LIFO (Taking and appending from Top)

___Graphs__

In essence the approach from Corman et al is to model a graph as a list of linked lists, which is in its core an __adjacency list__ (thus modeling Directed Acyclic Graphs)

Implementation of realpython.com uses a dictionary again to model a graph 

```
>>> graph = {
...     1: [2, 3, None],
...     2: [4, None],
...     3: [None],
...     4: [5, 6, None],
...     5: [6, None],
...     6: [None]
... }
```
In Python however the normal list is a dynamic array as described here http://www.laurentluce.com/posts/python-list-implementation/ thus the memory usage between this type of array and a linked list is insignificant. 

___Insertion and Deletion__

Lists in python have constant time O(1) when you try to append or remove element at the end of the list.
However the closer you get to the start of it the time complexity is increasing because some shifting is needed in memory to represent the new array. Whereas with linked lists insertion and deletion at the end or beginning of the list have always constant time giving them a small advantage when implementing a queue (FIFO) where elements are added at the emd and removed from the beginning. But perform similar when implementing a stack where elements are inserted and removed from the end of the list. 

__collections.deque__

see datastructures/realpython_linkedlists.py