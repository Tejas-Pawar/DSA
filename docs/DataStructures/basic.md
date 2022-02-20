# Basic Data Structures

## **[array](https://www.cplusplus.com/reference/array/array/)**

fixed-size sequence containers

hold a specific number of elements ordered in a strict linear sequence (contiguous)

Zero-sized arrays are valid, but they should not be dereferenced


## **[vector](https://www.cplusplus.com/reference/vector/vector/)**

sequence containers representing arrays that can change in size

contiguous storage locations

unlike arrays, their size can change dynamically, with their storage being handled automatically by the container.

vector containers may allocate some extra storage to accommodate for possible growth, and thus the container may have an actual capacity greater than the storage strictly needed to contain its elements 

## **[set](https://www.cplusplus.com/reference/set/set/)**

store unique elements following a specific order

[set](https://www.cplusplus.com/reference/set/set/) uses Red Black tree (balance binary search tree)

[unordered_set](https://www.cplusplus.com/reference/unordered_set/unordered_set/) uses hash table

[multiset](https://www.cplusplus.com/reference/set/multiset/) can have repeated elements

[unordered_multiset](https://www.cplusplus.com/reference/unordered_set/unordered_multiset/) can have repeated elements which is implemented as hash table

## **[stack](https://www.cplusplus.com/reference/stack/stack/)**

designed to operate in a LIFO context

## **[queue](https://www.cplusplus.com/reference/queue/queue/)**

designed to operate in a FIFO context

Elements are pushed into the "back" of the specific container and popped from its "front"

[priority_queue:](https://www.cplusplus.com/reference/queue/priority_queue/) designed such that its first element is always the greatest of the elements it contains, according to some strict weak ordering criterion (heap)

[priority_queue:](https://www.cplusplus.com/reference/queue/priority_queue/) elements can be inserted at any moment, and only the max heap element can be retrieved

## **[list](https://www.cplusplus.com/reference/list/list/)**

sequence containers that allow constant time insert and erase operations anywhere within the sequence, and iteration in both directions.

implemented as doubly-linked lists

[forward_list](https://www.cplusplus.com/reference/forward_list/forward_list/) are implemented as singly-linked lists

list and forward_list both lack direct access to the elements by their position

## **[map](https://www.cplusplus.com/reference/map/map/)**

[map:](https://www.cplusplus.com/reference/map/map/) store elements formed by a combination of a key value and a mapped value, following a specific order.

[map:](https://www.cplusplus.com/reference/map/map/) Implemented as balanced binary search tree (Red Black Tree)

[multimap:](https://www.cplusplus.com/reference/map/multimap/) multiple elements can have equivalent keys

[unordered_map](https://www.cplusplus.com/reference/unordered_map/unordered_map/) Implemented as hashtable

[unordered_multimap](https://www.cplusplus.com/reference/unordered_map/unordered_multimap/) allows different elements to have equivalent keys


