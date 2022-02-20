## Leetcode 297: Serialize and deserialise a binary tree

Need to add indicator(delimiter) for null pointers in preorder traversal.

```cpp
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