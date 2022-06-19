
## L21 Vertical order traversal

answer storage -> map <vertical_level, map<horizontal_lvel,multiset<int> > 

queue -> (node*, vertical, level(horizontal) )

You can do any of inorder, preorder, postorder traversal with above structure.

```cpp

class Solution {
public:
    vector<vector<int>> verticalTraversal(TreeNode* root) {
        map<int, map<int, multiset<int>>> nodes;
        queue<pair<TreeNode*, pair<int, int>>> todo;
        todo.push({root, {0, 0}});
        while (!todo.empty()) {
            auto p = todo.front();
            todo.pop();
            TreeNode* node = p.first;
            int x = p.second.first, y = p.second.second;
            nodes[x][y].insert(node -> val);
            if (node -> left) {
                todo.push({node -> left, {x - 1, y + 1}});
            }
            if (node -> right) {
                todo.push({node -> right, {x + 1, y + 1}});
            }
        }
        vector<vector<int>> ans;
        for (auto p : nodes) {
            vector<int> col;
            for (auto q : p.second) {
                col.insert(col.end(), q.second.begin(), q.second.end());
            }
            ans.push_back(col);
        }
        return ans;
    }
};


```


## L22 Top view of binary tree

Based on above vertical order traversal.

Keep storage as just map< vertical, int > as we just need first element from top for that vertical.

```cpp

class Solution
{
    public:
    //Function to return a list of nodes visible from the top view 
    //from left to right in Binary Tree.
    vector<int> topView(Node *root)
    {
        vector<int> ans; 
        if(root == NULL) return ans; 
        map<int,int> mpp; 
        queue<pair<Node*, int>> q; 
        q.push({root, 0}); 
        while(!q.empty()) {
            auto it = q.front(); 
            q.pop(); 
            Node* node = it.first; 
            int line = it.second; 
            if(mpp.find(line) == mpp.end()) mpp[line] = node->data; 
            
            if(node->left != NULL) {
                q.push({node->left, line-1}); 
            }
            if(node->right != NULL) {
                q.push({node->right, line + 1}); 
            }
            
        }
        
        for(auto it : mpp) {
            ans.push_back(it.second); 
        }
        return ans; 
    }

};

```

## L23: Bottom view of tree

Same as above only difference is that we keep updating map even if it is already filled as we need last node from that line.

```cpp

class Solution {
  public:
    vector <int> bottomView(Node *root) {
        vector<int> ans; 
        if(root == NULL) return ans; 
        map<int,int> mpp; 
        queue<pair<Node*, int>> q; 
        q.push({root, 0}); 
        while(!q.empty()) {
            auto it = q.front(); 
            q.pop(); 
            Node* node = it.first; 
            int line = it.second; 
            mpp[line] = node->data; 
            
            if(node->left != NULL) {
                q.push({node->left, line-1}); 
            }
            if(node->right != NULL) {
                q.push({node->right, line + 1}); 
            }
            
        }
        
        for(auto it : mpp) {
            ans.push_back(it.second); 
        }
        return ans;  
    }
};

```


## L24 : Left/Right view of binary tree

Left view : root, left, right recursive traversal while maintaining level

Right view : root, right, left recursive traversal while maintaining level

```cpp
//Right view:
class Solution {
public:
    void recursion(TreeNode *root, int level, vector<int> &res)
    {
        if(root==NULL) return ;
        if(res.size()==level) {  //Not updating result for level if already updated i.e. res.size() == level
            res.push_back(root->val);
        }
        recursion(root->right, level+1, res);
        recursion(root->left, level+1, res);
    }
    
    vector<int> rightSideView(TreeNode *root) {
        vector<int> res;
        recursion(root, 0, res);
        return res;
    }
};

```

## L25: Symmetric Binary tree

Symmetric Tree:
Consider it as mirror of left half.

            1
        2      2
      3   4  4   3

```cpp

class Solution {
public:
    bool f(TreeNode *root1, TreeNode* root2) {
        if(!root1) return !root2;
        if(!root2) return !root1;
        return (root1->val == root2->val) && f(root1->left, root2->right) && f(root1->right, root2->left);
    }
    bool isSymmetric(TreeNode* root) {
        if(!root) return true;
        return f(root->left, root->right);
    }
};


```

## L26: Print Root to Node path in Binary tree

Keeping vector reference and adding node values while traversing and removing values if value not found (backtracking)

```cpp

bool getPath(TreeNode *root, vector<int> &arr, int x) {
    // if root is NULL
    // there is no path
    if (!root)
        return false;
     
    // push the node's value in 'arr'
    arr.push_back(root->val);   
     
    // if it is the required node
    // return true
    if (root->val == x)   
        return true;
     
    // else check whether the required node lies
    // in the left subtree or right subtree of
    // the current node
    if (getPath(root->left, arr, x) ||
        getPath(root->right, arr, x))
        return true;
     
    // required node does not lie either in the
    // left or right subtree of the current node
    // Thus, remove current node's value from
    // 'arr'and then return false   
    arr.pop_back();
    return false;  
}
vector<int> Solution::solve(TreeNode* A, int B) {
    vector<int> arr; 
    if(A == NULL) {
        return arr; 
    }
    getPath(A, arr, B);
    return arr; 
}

```

## L27: Lowest Common Ancestor in Binary Tree

Naive:
One way could be to print path for both nodes using above function. 
And find last common node in both results. But that will use extra result.

Recursive:


```cpp

class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        //base case
        if (root == NULL || root == p || root == q) {
            return root;
        }
        TreeNode* left = lowestCommonAncestor(root->left, p, q);
        TreeNode* right = lowestCommonAncestor(root->right, p, q);

        //result
        if(left == NULL) {
            return right;
        }
        else if(right == NULL) {
            return left;
        }
        else { //both left and right are not null, we found our result
            return root;
        }
    }
};


```

## LCA in BST

Note: Previous question was binary tree and this question is binary `search` tree.

<br />If both p and q values are less than cur value, then recursively search in right.
<br />If both p and q values are greater than cur value, then recursively search in left.
<br /> Else cur node is the LCA.



```cpp

class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(!root) return NULL;
        int curr = root->val; 
        if(curr < p->val && curr < q->val) {
            return lowestCommonAncestor(root->right, p, q);
        }
        if(curr > p->val && curr > q->val) {
            return lowestCommonAncestor(root->left, p, q);
        }
        return root;
    }
};
```


## L28: Maximum Width of binary tree

The width of one level is defined as the length between the end-nodes (the leftmost and rightmost non-null nodes), where the null nodes between the end-nodes that would be present in a complete binary tree extending down to that level are also counted into the length calculation.<br/>

Indexing of nodes is important here.
It is possible that few nodes in the middle part can be missing but still width is last - first + 1.


                      1                                           1                     
                2             3                             1             2             
            4       5      6     7                      1       2       3     4          
        8   9   10  11   12  13  14  15             1   2    3    4   5  6  7  8     

```cpp

class Solution {
public:
    int widthOfBinaryTree(TreeNode* root) {
        if(!root)
            return 0;
        int ans = 0;
        queue<pair<TreeNode*, int>> q;
        q.push({root,0});
        while(!q.empty()){
            int size = q.size();
            int mmin = q.front().second;    //to make the id starting from zero
            int first,last;
            for(int i=0; i<size; i++){
                int cur_id = q.front().second-mmin; //to start with index with 0
                TreeNode* node = q.front().first;
                q.pop();
                if(i==0) first = cur_id;
                if(i==size-1) last = cur_id;
                if(node->left)
                    q.push({node->left, cur_id*2+1});
                if(node->right)
                    q.push({node->right, cur_id*2+2});
            }
            ans = max(ans, last-first+1);
        }
        return ans;
    }
};


```


## L29: Children Sum Property in Binary Tree

For any node, value should be equivalent to left + right.
Allowed to increment any node by +1 any no of times.

              
                   2             ->           45                              
            35           10              35         10                           
         2      3     5      2       30     5    8      2


If sum of children is greater than root, then change root = sum of children
else change both children to root.


```cpp


void reorder(BinaryTreeNode < int > * root) {
    if(root == NULL) return; 
    int child = 0;
    if(root->left) {
        child += root->left->data; 
    }
    if(root->right) {
        child += root->right->data; 
    }
    
    if(child >= root->data) root->data = child; 
    else {
        if(root->left) root->left->data = root->data; 
        else if(root->right) root->right->data = root->data; 
    }
    
    reorder(root->left); 
    reorder(root->right); 
    
    int tot = 0; 
    if(root->left) tot += root->left->data; 
    if(root->right) tot+= root->right->data; 
    if(root->left or root->right) root->data = tot;  
}
void changeTree(BinaryTreeNode < int > * root) {
    reorder(root); 
}  


```


## Print all nodes at a distance of K in Binary Tree

<br/>First mark parent pointers in a map using BFS.
<br/>Do radial BFS traversal from given node.
<br/>1. Keep visited map.
<br/>2. Radially visiting -> Visit parent, left, right

```cpp

class Solution {
    void markParents(TreeNode* root, unordered_map<TreeNode*, TreeNode*> &parent_track, TreeNode* target) {
        queue<TreeNode*> queue;
        queue.push(root);
        while(!queue.empty()) { 
            TreeNode* current = queue.front(); 
            queue.pop();
            if(current->left) {
                parent_track[current->left] = current;
                queue.push(current->left);
            }
            if(current->right) {
                parent_track[current->right] = current;
                queue.push(current->right);
            }
        }
    }
public:
    vector<int> distanceK(TreeNode* root, TreeNode* target, int k) {
        unordered_map<TreeNode*, TreeNode*> parent_track; // node -> parent
        markParents(root, parent_track, target); 
        
        unordered_map<TreeNode*, bool> visited; 
        queue<TreeNode*> queue;
        queue.push(target);
        visited[target] = true;
        int curr_level = 0;
        while(!queue.empty()) { /*Second BFS to go upto K level from target node and using our hashtable info*/
            int size = queue.size();
            if(curr_level++ == k) break;
            for(int i=0; i<size; i++) {
                TreeNode* current = queue.front(); queue.pop();
                if(current->left && !visited[current->left]) {
                    queue.push(current->left);
                    visited[current->left] = true;
                }
                if(current->right && !visited[current->right]) {
                    queue.push(current->right);
                    visited[current->right] = true;
                }
                if(parent_track[current] && !visited[parent_track[current]]) {
                    queue.push(parent_track[current]);
                    visited[parent_track[current]] = true;
                }
            }
        }
        vector<int> result;
        while(!queue.empty()) {
            TreeNode* current = queue.front(); queue.pop();
            result.push_back(current->val);
        }
        return result;
    }
};


```

## L31: Minimum time taken to BURN the Binary Tree from a Node

<br/>This is similar to above question to print nodes at distance K from given node. 
<br/>Here only difference is to find max length from given node. 
<br/>In previous question, we did radial bfs traversal for K length and break out of loop after k 
<br/>but in current question we need to keep going until all nodes are traversed and increment time(maxi) only if we are burning some node.

```cpp

#include<bits/stdc++.h> 

int findMaxDistance(map<BinaryTreeNode<int>*, BinaryTreeNode<int>*> &mpp, BinaryTreeNode<int>* target) {
    queue<BinaryTreeNode<int>*> q; 
    q.push(target); 
    map<BinaryTreeNode<int>*,int> vis; 
    vis[target] = 1;
    int maxi = 0; 
    while(!q.empty()) {
        int sz = q.size();
        int fl = 0; //Flag to indicate that at least 1 node is burnt in this level
        for(int i = 0;i<sz;i++) {
            auto node = q.front();
            q.pop();
            if(node->left && !vis[node->left]) {
                fl = 1; 
                vis[node->left] = 1; 
                q.push(node->left); 
            }
            if(node->right && !vis[node->right]) {
                fl = 1; 
                vis[node->right] = 1; 
                q.push(node->right); 
            }
            
            if(mpp[node] && !vis[mpp[node]]) {
                fl = 1; 
                vis[mpp[node]] = 1; 
                q.push(mpp[node]); 
            } 
        }
        if(fl) maxi++; 
    }
    return maxi; 
}

BinaryTreeNode<int>* bfsToMapParents(BinaryTreeNode<int>* root, 
                                     map<BinaryTreeNode<int>*, BinaryTreeNode<int>*> &mpp, int start) {
    queue<BinaryTreeNode<int>*> q; 
    q.push(root); 
    BinaryTreeNode<int>* res; 
    while(!q.empty()) {
        BinaryTreeNode<int>* node = q.front(); 
        if(node->data == start) res = node; 
        q.pop(); 
        if(node->left) {
            mpp[node->left] = node; 
            q.push(node->left); 
        }
        if(node->right) {
            mpp[node->right] = node;
            q.push(node->right); 
        }
    }
    return res; 
}

int timeToBurnTree(BinaryTreeNode<int>* root, int start)
{
    map<BinaryTreeNode<int>*, BinaryTreeNode<int>*> mpp; 
    BinaryTreeNode<int>* target = bfsToMapParents(root, mpp, start); 
    int maxi = findMaxDistance(mpp, target); 
    return maxi; 
}

```

## L32: Count total node in a balanced binary tree

Following is a balanced binary tree but not a complete binary tree.
If left height == right height i.e. If complete binary tree then no of nodes = 2^h - 1

                     1
            2                 3
        4       5         6       7
      8   9   10   11


Time complexity = (lg N)^2

```cpp

class Solution {
public:
    int countNodes(TreeNode* root) {
        if(root == NULL) return 0; 
        
        int lh = findHeightLeft(root); 
        int rh = findHeightRight(root); 
        
        if(lh == rh) return (1<<lh) - 1; 
        
        return 1 + countNodes(root->left) + countNodes(root->right); 
    }
    int findHeightLeft(TreeNode* node) {
        int hght = 0; 
        while(node) {
            hght++; 
            node = node->left; 
        }
        return hght; 
    }
    int findHeightRight(TreeNode* node) {
        int hght = 0; 
        while(node) {
            hght++; 
            node = node->right; 
        }
        return hght; 
    }
};

```


## L34: Construct a Binary Tree from Preorder and Inorder traversal

Note that inorder traversal is very important to reconstruct a unique tree as it gives exact position of root in pre or post order and then only we know what is left part and right part of unique tree.

Steps: 
Put complete inorder traversal in hash map i.e. inMap. This helps in finding location of root in inorder. And we can recursively call buildTree for left part and right part.

```cpp

class Solution {

private:
   TreeNode* buildTree(vector<int>& preorder, int preStart, int preEnd, vector<int>& inorder, int inStart, int inEnd, map<int, int> &inMap) {
        if(preStart > preEnd || inStart > inEnd) return NULL;

        TreeNode* root = new TreeNode(preorder[preStart]);
        int inRoot = inMap[root->val];
        int numsLeft = inRoot - inStart;

        root->left = buildTree(preorder, preStart + 1, preStart + numsLeft, inorder, inStart, inRoot - 1, inMap);
        root->right = buildTree(preorder, preStart + numsLeft + 1, preEnd, inorder, inRoot + 1, inEnd, inMap);

        return root;
    }

public:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        map<int, int> inMap; 

        for(int i = 0; i < inorder.size(); i++) {
            inMap[inorder[i]] = i;
        }

        TreeNode* root = buildTree(preorder, 0, preorder.size() - 1, inorder, 0, inorder.size() - 1, inMap);
        return root;
    }
};


```


## L35: Construct binary tree from post order and inorder traversal

This is similar to above question. In preorder first node is the root, in postorder last node is the root.

```cpp

//Modify above code and construct

class Solution {
public:
    TreeNode* buildTree(vector<int>& postorder, vector<int>& inorder) {
        map<int, int> inMap; 

        for(int i = 0; i < inorder.size(); i++) {
            inMap[inorder[i]] = i;
        }

        TreeNode* root = buildTree(preorder, 0, postorder.size() - 1, inorder, 0, inorder.size() - 1, inMap);
        return root;
    }
   TreeNode* buildTree(vector<int>& postorder, int postStart, int postEnd, vector<int>& inorder, int inStart, int inEnd, map<int, int> &inMap) {
        if(preStart > preEnd || inStart > inEnd) return NULL;

        TreeNode* root = new TreeNode(postorder[postEnd]);
        int inRoot = inMap[root->val];
        int numsLeft = inRoot - inStart;

        root->left = buildTree(postorder, postStart, preStart + numsLeft - 1, inorder, inStart, inRoot - 1, inMap);
        root->right = buildTree(postorder, postStart + numsLeft, postEnd - 1, inorder, inRoot + 1, inEnd, inMap);

        return root;
    }
};


```



## L36: Leetcode 297: Serialize and deserialise a binary tree


You can use any traversal, not necessarily preorder traversal.
Need to add indicator(delimiter) for null pointers in preorder traversal.

```cpp

//Following implementation is using BFS:
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */

class Codec {
public:

    // Encodes a tree to a single string.
    string serialize(TreeNode* root) {
        if(!root) return "";
        
        string s ="";
        queue<TreeNode*> q;
        q.push(root);
        while(!q.empty()) {
           TreeNode* curNode = q.front();
           q.pop();
           if(curNode==NULL) s.append("#,");
           else s.append(to_string(curNode->val)+',');
           if(curNode != NULL){
               q.push(curNode->left);
               q.push(curNode->right);            
           }
        }
        return s;
    }

    // Decodes your encoded data to tree.
    TreeNode* deserialize(string data) {
        if(data.size() == 0) return NULL; 
        stringstream s(data);
        string str; 
        getline(s, str, ',');
        TreeNode *root = new TreeNode(stoi(str));
        queue<TreeNode*> q; 
        q.push(root); 
        while(!q.empty()) {
            
            TreeNode *node = q.front(); 
            q.pop(); 
            
            getline(s, str, ',');
            if(str == "#") {
                node->left = NULL; 
            }
            else {
                TreeNode* leftNode = new TreeNode(stoi(str)); 
                node->left = leftNode; 
                q.push(leftNode); 
            }
            
            getline(s, str, ',');
            if(str == "#") {
                node->right = NULL; 
            }
            else {
                TreeNode* rightNode = new TreeNode(stoi(str)); 
                node->right = rightNode;
                q.push(rightNode); 
            }
        }
        return root; 
    }
};


//Following implementation is using DFS:
class Codec {
public:
    // Encodes a tree to a single string.
    void dfs(TreeNode* curr,string& s){
        if(!curr){
            s.push_back('*');
            return;
        }
        s.append(to_string(curr->val)+",");
        dfs(curr->left,s);
        dfs(curr->right,s);
    }
    
    string serialize(TreeNode* root) {
        string s="";
        dfs(root,s);
        cout<<s;
        return s;
    }

    // Decodes your encoded data to tree.
    TreeNode *decode(string& data){
        if(data.size()==0)  return NULL;
        if(data[0]=='*'){
            data = data.substr(1);
            return NULL;
        }
        int pos = 0;
        string no="";
        while(data[pos]!=',')
            no += data[pos++];
        
        TreeNode *curr = new TreeNode(stoi(no));
        data = data.substr(pos+1);
        curr->left = decode(data);
        curr->right = decode(data);
        return curr;
    }
    
    TreeNode* deserialize(string data) {
        if(data.size()<=1)  return NULL;
        return decode(data);
    }
};

// Your Codec object will be instantiated and called as such:
// Codec ser, deser;
// TreeNode* ans = deser.deserialize(ser.serialize(root));

```

----

## L37: Morris traversal

Does not consume extra auxilliar<br/>y space of O(N) which is used in recursive or iterative traversal.
This is based on concept of threaded binary tree.

<br/>Intuition: In inorder, we go back to root after last node of left subtree.
<br/>Critical step: Find rightmost node of left subtree and
<br/>1. connect last(rightmost) node of left subtree to root. or
<br/>2. if rightmost node == current node at any point in loop, then cut the thread and move to right

```cpp
//Inorder Morris traversal
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> inorder; 
        
        TreeNode* cur = root; 
        while(cur != NULL) {
            if(cur->left == NULL) {
                inorder.push_back(cur->val); 
                cur = cur->right; 
            }
            else {
                TreeNode* prev = cur->left; 
                while(prev->right != NULL && prev->right != cur) {
                    prev = prev->right; 
                }
                
                if(prev->right == NULL) {
                    prev->right = cur;
                    cur = cur->left; 
                }
                else {
                    prev->right = NULL;
                    //Add to inorder when cutting thread 
                    inorder.push_back(cur->val); 
                    cur = cur->right; 
                }
            }
        }
        return inorder; 
    }
};



//Preorder Morris traversal
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> preorder; 
        
        TreeNode* cur = root; 
        while(cur != NULL) {
            if(cur->left == NULL) {
                preorder.push_back(cur->val); 
                cur = cur->right; 
            }
            else {
                TreeNode* prev = cur->left; 
                while(prev->right != NULL && prev->right != cur) {
                    prev = prev->right; 
                }
                
                if(prev->right == NULL) {
                    //Add to preorder when moving to left
                    prev->right = cur;
                    preorder.push_back(cur->val); //Preorder change: add to result when finding rightmost
                    cur = cur->left; 
                }
                else {
                    prev->right = NULL;
                    cur = cur->right; 
                }
            }
        }
        return preorder; 
    }
};


```

----


## L38: Flatten Binary tree

```cpp

Approach 1: Recursive
// TC - O(N) 
// SC - O(N)
class Solution {
    TreeNode* prev = NULL;
public:
    void flatten(TreeNode* root) {
        if(root == NULL) return; 
        
        flatten(root->right); 
        flatten(root->left); 
        
        root->right = prev;
        root->left = NULL; 
        prev = root; 
    }
};

// TC - O(N) 
// SC - O(N) 
// Iterative (Simplest)
// pop top from stack, push right, left to stack (note right first)
// set cur->right = stack.top
// set cur->left = null
class Solution {
public:
    void flatten(TreeNode* root) {
        if(root == NULL) return; 
        stack<TreeNode*> st; 
        st.push(root); 
        while(!st.empty()) {
            TreeNode* cur = st.top(); 
            st.pop(); 
            
            if(cur->right != NULL) {
                st.push(cur->right); 
            }
            if(cur->left != NULL) {
                st.push(cur->left); 
            }
            if(!st.empty()) {
                cur->right = st.top(); 
            }
            cur->left = NULL;
        }
        
    }
};


//This is similar to Morris Traversal
//Difference: Connect last node of left subtree to right node instead of cur.
//cur->right = cur->left
// TC - O(N) 
// SC - O(1) 
class Solution {
public:
    void flatten(TreeNode* root) {
        TreeNode* cur = root;
		while (cur)
		{
			if(cur->left)
			{
				TreeNode* pre = cur->left;
				while(pre->right)
				{
					pre = pre->right;
				}
				pre->right = cur->right;
				cur->right = cur->left;
				cur->left = NULL;
			}
			cur = cur->right;
		}
    }
};


```

----

## L41 Ceil 

Smallest value greater than key.

```cpp

int findCeil(BinaryTreeNode<int> *root, int key){

	int ceil = -1; 
    while (root) {

        if (root->data == key) {
            ceil = root->data;
            return ceil;
        }
 
        if (key > root->data) {
            root = root->right;
        }
        else {
            ceil = root->data; 
            root = root->left;
        }
    }
    return ceil; 
}


```

----

## L42:  Floor

Greatest value smaller than given key.

```cpp

int floorInBST(TreeNode<int> * root, int key)
{
    int floor = -1; 
    while (root) {
 
        if (root->val == key) {
            floor = root->val;
            return floor;
        }
 
        if (key > root->val) {
            floor = root->val;
            root = root->right;
        }
        else {
            root = root->left;
        }
    }
    return floor;
}


```

----

## L43. Insert in BST

```cpp
class Solution {
public:
    TreeNode* insertIntoBST(TreeNode* root, int val) {
        if(root == NULL) return new TreeNode(val);
        TreeNode *cur = root;
        while(true) {
            if(cur->val <= val) {
                if(cur->right != NULL) cur = cur->right;
                else {
                    cur->right = new TreeNode(val);
                    break;
                }
            } else {
                if(cur->left != NULL) cur = cur->left;
                else {
                    cur->left = new TreeNode(val);
                    break;
                }
            }
        }
        return root;
    }
};
```

## L44. Delete a node from BST




```cpp

class Solution {
public:
    TreeNode* deleteNode(TreeNode* root, int key) {
        if (root == NULL) {
            return NULL;
        }
        if (root->val == key) {
            return helper(root);
        }
        TreeNode *dummy = root;
        while (root != NULL) {
            if (root->val > key) {
                if (root->left != NULL && root->left->val == key) {
                    root->left = helper(root->left);
                    break;
                } else {
                    root = root->left;
                }
            } else {
                if (root->right != NULL && root->right->val == key) {
                    root->right = helper(root->right);
                    break;
                } else {
                    root = root->right;
                }
            }
        }
        return dummy;
    }

    //Attaches right subtree to last node of left subtree and returns pointer to left subtree.
    TreeNode* helper(TreeNode* root) {
            if (root->left == NULL) 
            {
                return root->right;
            } 
            else if (root->right == NULL)
            {
                return root->left;
            } 
            TreeNode* rightChild = root->right;
            TreeNode* lastRight = findLastRight(root->left);
            lastRight->right = rightChild;
            return root->left;
    }
    TreeNode* findLastRight(TreeNode* root) {
        if (root->right == NULL) {
            return root;
        }
        return findLastRight(root->right);
    }
};

```

----

## L45 Kth smallest element in binary search tree

You can use any Inorder traversal with count.
<br/>Increment count at cout(process root) step.

```cpp

//iterative inorder traversal
class Solution {
    public int kthSmallest(TreeNode root, int k) {
        Stack<TreeNode> stack = new Stack<TreeNode>();
        TreeNode node = root;
        int cnt = 0; 
        while(true){
            if(node != null){
                stack.push(node);
                node = node.left;
            }
            else{
                if(stack.isEmpty()){
                    break;
                }
                node = stack.pop();
                // inorder.add(node.val);
                cnt++; 
                if(cnt == k) return node.val; 
                node = node.right;
            }
        }
        return -1; 
    }
}

```

----

## L46. Check if a tree is a BST or BT | Validate a BST

```cpp

//Recursive
class Solution {
    public boolean isValidBST(TreeNode root) {
        return isValidBST(root, Long.MIN_VALUE, Long.MAX_VALUE);
    }
    
    public boolean isValidBST(TreeNode root, long minVal, long maxVal) {
        if (root == null) return true;
        if (root.val >= maxVal || root.val <= minVal) return false;
        return isValidBST(root.left, minVal, root.val) && isValidBST(root.right, root.val, maxVal);
    }
}

//Iterative
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        stack<TreeNode*> st; 
        TreeNode* node = root;
        TreeNode* prev = NULL; 
        while(true) {
            if(node != NULL) {
                st.push(node); 
                node = node->left; 
            }
            else {
                if(st.empty() == true) break; 
                node = st.top(); 
                st.pop(); 
                // inorder.push_back(node->val); 
                //since we keep going from left to right, make sure node's value should be greater than prev value
                if(prev != NULL && node->val <= prev->val) return false; 
                prev = node; 
                node = node->right; 
            }  
        }
        return true;
    }
};

```

----

## L47 LCA in Binary Search Tree

```cpp

class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(!root) return NULL;
        int curr = root->val; 
        if(curr < p->val && curr < q->val) {
            return lowestCommonAncestor(root->right, p, q);
        }
        if(curr > p->val && curr > q->val) {
            return lowestCommonAncestor(root->left, p, q);
        }
        return root;
    }
};


```

----

## L48. Construct a BST from a preorder traversal 

1. You are given pre order, sorting of pre order will give inorder. Now this is same as L34

2. Keep upper bound with recursive calls, if not possible then return null.

```cpp

class Solution {
public:
    TreeNode* bstFromPreorder(vector<int>& A) {
        int i = 0;
        return build(A, i, INT_MAX);
    }

    TreeNode* build(vector<int>& A, int& i, int bound) {
        if (i == A.size() || A[i] > bound) return NULL;
        TreeNode* root = new TreeNode(A[i++]);
        root->left = build(A, i, root->val);
        root->right = build(A, i, bound);
        return root;
    }
};

```

## BST Iterator 

```cpp

class BSTIterator {
    stack<TreeNode *> myStack;
public:
    BSTIterator(TreeNode *root) {
        pushAll(root);
    }

    /** @return whether we have a next smallest number */
    bool hasNext() {
        return !myStack.empty();
    }

    /** @return the next smallest number */
    int next() {
        TreeNode *tmpNode = myStack.top();
        myStack.pop();
        pushAll(tmpNode->right);
        return tmpNode->val;
    }

private:
    void pushAll(TreeNode *node) {
        for (; node != NULL; myStack.push(node), node = node->left);
    }
};


```


## 2 Sum BST

Create iterator next, prev.
And follow 2 pointer approach.

```cpp

class BSTIterator {
    stack<TreeNode *> myStack;
    bool reverse = true; 
public:
    BSTIterator(TreeNode *root, bool isReverse) {
        reverse = isReverse; 
        pushAll(root);
    }

    /** @return whether we have a next smallest number */
    bool hasNext() {
        return !myStack.empty();
    }

    /** @return the next smallest number */
    int next() {
        TreeNode *tmpNode = myStack.top();
        myStack.pop();
        if(!reverse) pushAll(tmpNode->right);
        else pushAll(tmpNode->left);
        return tmpNode->val;
    }

private:
    void pushAll(TreeNode *node) {
        for(;node != NULL; ) {
             myStack.push(node);
             if(reverse == true) {
                 node = node->right; 
             } else {
                 node = node->left; 
             }
        }
    }
};
class Solution {
public:
    bool findTarget(TreeNode* root, int k) {
        if(!root) return false; 
        BSTIterator l(root, false); 
        BSTIterator r(root, true); 
        
        int i = l.next(); 
        int j = r.next(); 
        while(i<j) {
            if(i + j == k) return true; 
            else if(i + j < k) i = l.next(); 
            else j = r.next(); 
        }
        return false; 
    }
};
```


## Recover BST

2 nodes are swapped, you need to correct.

2 Cases:
1. swapped nodes are adjacent (2 violations)
2. swapped nodes are not adjacent (1 violation)

                11 <- root
       prev->1       5

```cpp

/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
private: 
    TreeNode* first;
    TreeNode* prev;
    TreeNode* middle;
    TreeNode* last; 
private: 
    void inorder(TreeNode* root) {
        if(root == NULL) return; 
        
        inorder(root->left);
        
        if (prev != NULL && (root->val < prev->val))
        {
           
            // If this is first violation, mark these two nodes as
            // 'first' and 'middle'
            if ( first == NULL )
            {
                first = prev;
                middle = root;
            }
 
            // If this is second violation, mark this node as last
            else
                last = root;
        }
 
        // Mark this node as previous
        prev = root;
        inorder(root->right); 
    }
public:
    void recoverTree(TreeNode* root) {
        first = middle = last = NULL; 
        prev = new TreeNode(INT_MIN); 
        inorder(root);
        if(first && last) swap(first->val, last->val); 
        else if(first && middle) swap(first->val, middle->val); 
    }
};


```


## Largest BST in BT


```cpp

/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class NodeValue {
public:
    int maxNode, minNode, maxSize;
    
    NodeValue(int minNode, int maxNode, int maxSize) {
        this->minNode = minNode;
        this->maxNode = maxNode;
        this->maxSize = maxSize;
    }
};

class Solution {
private:
    NodeValue largestBSTSubtreeHelper(TreeNode* root) {
        // An empty tree is a BST of size 0.
        if (!root) {
            return NodeValue(INT_MAX, INT_MIN, 0);
        }
        
        // Get values from left and right subtree of current tree.
        auto left = largestBSTSubtreeHelper(root->left);
        auto right = largestBSTSubtreeHelper(root->right);
        
        // Current node is greater than max in left AND smaller than min in right, it is a BST.
        if (left.maxNode < root->val && root->val < right.minNode) {
            // It is a BST.
            return NodeValue(min(root->val, left.minNode), max(root->val, right.maxNode), 
                            left.maxSize + right.maxSize + 1);
        }
        
        // Otherwise, return [-inf, inf] so that parent can't be valid BST
        return NodeValue(INT_MIN, INT_MAX, max(left.maxSize, right.maxSize));
    }
    public:
    int largestBSTSubtree(TreeNode* root) {
        return largestBSTSubtreeHelper(root).maxSize;
    }
};


```

---


## ** Huffman Encoding **


1. Create a leaf node for each character and add them to the priority queue.
2. While there is more than one node in the queue:
    Remove the two nodes of the highest priority (the lowest frequency) from the queue.
    Create a new internal node with these two nodes as children and a frequency equal to the sum of both nodes’ frequencies.
    Add the new node to the priority queue.
3. The remaining node is the root node and the tree is complete.

```cpp

// C++ Program for Huffman Coding
// using Priority Queue
#include <iostream>
#include <queue>
using namespace std;
 
// Maximum Height of Huffman Tree.
#define MAX_SIZE 100
 
class HuffmanTreeNode {
public:
    // Stores character
    char data;
 
    // Stores frequency of
    // the character
    int freq;
 
    // Left child of the
    // current node
    HuffmanTreeNode* left;
 
    // Right child of the
    // current node
    HuffmanTreeNode* right;
 
    // Initializing the
    // current node
    HuffmanTreeNode(char character,
                    int frequency)
    {
        data = character;
        freq = frequency;
        left = right = NULL;
    }
};
 

/*
// Custom comparator class
class Compare {
public:
    bool operator()(HuffmanTreeNode* a,
                HuffmanTreeNode* b)
    {
        // Defining priority on
        // the basis of frequency
        return a->freq > b->freq;
    }

};
*/


bool Compare()(HuffmanTreeNode* a,
                HuffmanTreeNode* b)
{
    // Defining priority on
    // the basis of frequency
    return a->freq > b->freq;
}
 
// Function to generate Huffman
// Encoding Tree
HuffmanTreeNode* generateTree(priority_queue<HuffmanTreeNode*,
                              vector<HuffmanTreeNode*>,
                                             Compare> pq)
{
 
    // We keep on looping till
    // only one node remains in
    // the Priority Queue
    while (pq.size() != 1) {
 
        // Node which has least
        // frequency
        HuffmanTreeNode* left = pq.top();
 
        // Remove node from
        // Priority Queue
        pq.pop();
 
        // Node which has least
        // frequency
        HuffmanTreeNode* right = pq.top();
 
        // Remove node from
        // Priority Queue
        pq.pop();
 
        // A new node is formed
        // with frequency left->freq
        // + right->freq
 
        // We take data as '$'
        // because we are only
        // concerned with the
        // frequency
        HuffmanTreeNode* node = new HuffmanTreeNode('$',
                                  left->freq + right->freq);
        node->left = left;
        node->right = right;
 
        // Push back node
        // created to the
        // Priority Queue
        pq.push(node);
    }
 
    return pq.top();
}
 
// Function to print the
// huffman code for each
// character.
 
// It uses arr to store the codes
void printCodes(HuffmanTreeNode* root,
                int arr[], int top)
{
    // Assign 0 to the left node
    // and recur
    if (root->left) {
        arr[top] = 0;
        printCodes(root->left,
                   arr, top + 1);
    }
 
    // Assign 1 to the right
    // node and recur
    if (root->right) {
        arr[top] = 1;
        printCodes(root->right, arr, top + 1);
    }
 
    // If this is a leaf node,
    // then we print root->data
 
    // We also print the code
    // for this character from arr
    if (!root->left && !root->right) {
        cout << root->data << " ";
        for (int i = 0; i < top; i++) {
            cout << arr[i];
        }
        cout << endl;
    }
}
 
void HuffmanCodes(char data[],
                  int freq[], int size)
{
 
    // Declaring priority queue
    // using custom comparator
    priority_queue<HuffmanTreeNode*,
                   vector<HuffmanTreeNode*>,
                   Compare>
        pq;
 
    // Populating the priority
    // queue
    for (int i = 0; i < size; i++) {
        HuffmanTreeNode* newNode
            = new HuffmanTreeNode(data[i], freq[i]);
        pq.push(newNode);
    }
 
    // Generate Huffman Encoding
    // Tree and get the root node
    HuffmanTreeNode* root = generateTree(pq);
 
    // Print Huffman Codes
    int arr[MAX_SIZE], top = 0;
    printCodes(root, arr, top);
}
 
// Driver Code
int main()
{
    char data[] = { 'a', 'b', 'c', 'd', 'e', 'f' };
    int freq[] = { 5, 9, 12, 13, 16, 45 };
    int size = sizeof(data) / sizeof(data[0]);
 
    HuffmanCodes(data, freq, size);
    return 0;
}

```