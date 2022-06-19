# Basic Data Structures

## BST

## Graphs

## Tries


During delete operation we delete the key in bottom up manner using recursion. The following are possible conditions when deleting key from trie, 

<br/> 1. Key may not be there in trie. Delete operation should not modify trie.
<br/> 2. Key present as unique key (no part of key contains another key (prefix), nor the key itself is prefix of another key in trie). Delete all the nodes.
<br/> 3. Key is prefix key of another long key in trie. Unmark the leaf node.
<br/> 4. Key present in trie, having atleast one other key as prefix key. Delete nodes from end of key until first leaf node of longest prefix key. e.g. app, apple. delete(apple) should not delete app
<br/> 

```cpp

#include <iostream>
using namespace std;
 
// Define the character size
#define CHAR_SIZE 128
 
// A class to store a Trie node
class Trie
{
public:
    bool isLeaf;
    Trie* character[CHAR_SIZE];
 
    // Constructor
    Trie()
    {
        this->isLeaf = false;
 
        for (int i = 0; i < CHAR_SIZE; i++) {
            this->character[i] = nullptr;
        }
    }
 
    void insert(string);
    bool deletion(Trie*&, string);
    bool search(string);
    bool haveChildren(Trie const*);
};
 
// Iterative function to insert a key into a Trie
void Trie::insert(string key)
{
    // start from the root node
    Trie* curr = this;
    for (int i = 0; i < key.length(); i++)
    {
        // create a new node if the path doesn't exist
        if (curr->character[key[i]] == nullptr) {
            curr->character[key[i]] = new Trie();
        }
 
        // go to the next node
        curr = curr->character[key[i]];
    }
 
    // mark the current node as a leaf
    curr->isLeaf = true;
}
 
// Iterative function to search a key in a Trie. It returns true
// if the key is found in the Trie; otherwise, it returns false
bool Trie::search(string key)
{
    // return false if Trie is empty
    if (this == nullptr) {
        return false;
    }
 
    Trie* curr = this;
    for (int i = 0; i < key.length(); i++)
    {
        // go to the next node
        curr = curr->character[key[i]];
 
        // if the string is invalid (reached end of a path in the Trie)
        if (curr == nullptr) {
            return false;
        }
    }
 
    // return true if the current node is a leaf and the
    // end of the string is reached
    return curr->isLeaf;
}
 
// Returns true if a given node has any children
bool Trie::haveChildren(Trie const* curr)
{
    for (int i = 0; i < CHAR_SIZE; i++)
    {
        if (curr->character[i]) {
            return true;    // child found
        }
    }
 
    return false;
}
 
// Recursive function to delete a key in the Trie
bool Trie::deletion(Trie*& curr, string key)
{
    // return if Trie is empty
    if (curr == nullptr) {
        return false;
    }
 
    // if the end of the key is not reached
    if (key.length())
    {
        // recur for the node corresponding to the next character in the key
        // and if it returns true, delete the current node (if it is non-leaf)
 
        if (curr != nullptr &&
            curr->character[key[0]] != nullptr &&
            deletion(curr->character[key[0]], key.substr(1)) &&
            curr->isLeaf == false)
        {
            if (!haveChildren(curr))
            {
                delete curr;
                curr = nullptr;
                return true;
            }
            else {
                return false;
            }
        }
    }
 
    // if the end of the key is reached
    if (key.length() == 0 && curr->isLeaf)
    {
        // if the current node is a leaf node and doesn't have any children
        if (!haveChildren(curr))
        {
            // delete the current node
            delete curr;
            curr = nullptr;
 
            // delete the non-leaf parent nodes
            return true;
        }
 
        // if the current node is a leaf node and has children
        else {
            // mark the current node as a non-leaf node (DON'T DELETE IT)
            curr->isLeaf = false;
 
            // don't delete its parent nodes
            return false;
        }
    }
 
    return false;
}

```

## Min Heap/ Max Heap

  ![](../img/heap1.png)

## Disjoint set


## Fenwick Trees

<br/>Array len = 10^5
<br/>Queries = 10^5
<br/>1. replace arr[i]
<br/>2. Find sum from l..r 

Finding right most set bit of x:
<br/>x = a 1 b     where a is any random number and b is all 0s
<br/>x = 0101 1 0000

-x = a' 1 b  where a' can be ignored and b is all 0s
<br/>-x is 2's complement i.e. 1's complement i.e. 101001111 +1
<br/>-x = 1010 1 1111
<br/>x & -x = 0000 1 0000

Fenwick tree ( BIT[i] )  stores such that, at any index i we can store sum of some numbers (j+1...i) of the given array. This can also be called a partial sum tree. 
<br/>Here j is obtained by making last set bit of i as 0 ie. **i-(i&-1)**.

Always use 1 based indexing

```cpp

void update(int x, int delta)       //add "delta" at index "x"
{
  //while updating we keep adding delta to numbers obtained by adding x&-x
  for(; x <= n; x += x&-x) 
        BIT[x] += delta;
}

```

```cpp

int sum(int x)      //returns the sum of first x elements in given array a[]
{
  //while getting sum, we keep adding BIT[i] where i is obtained by subtracting x&-x
  int sum = 0;
  for(; x > 0; x -= x&-x)
      sum += BIT[x];
  return sum;
}

```

Inversion count problem can be solved using BIT.

<br/>While traversing input array, update(+1) count of arr[i] by 1. 
<br/>So BIT(x) will give count of numbers till x.
<br/>We want to find already present count of numbers greater than current i.e. BIT(max) - BIT(arr[i])

```cpp

vector<int> input = {};
vector<int> bit(input.size()+1);
int count=0;
for(int i=0; i<input.size(); i++ )
{
  //first check numbers greater than input[i] till i
  count += sum(max) - sum(input[i]);
  //add input[i]
  update(input[i],1);
}

```





