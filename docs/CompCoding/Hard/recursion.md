## Print all permutation of given sequence

Question: Given an array nums of distinct integers, return all the possible permutations. You can return the answer in any order.

```cpp

class Solution {
private: 
    void recurPermute(vector<int> &ds, vector<int> &nums, vector<vector<int>> &ans, int freq[]) {
        if(ds.size() == nums.size()) {
            ans.push_back(ds); 
            return; 
        }
        for(int i = 0;i<nums.size();i++) {
            if(!freq[i]) {
                ds.push_back(nums[i]);
                //Mark that we have used index i
                freq[i] = 1;
                recurPermute(ds, nums, ans, freq);
                freq[i] = 0; 
                ds.pop_back(); 
            }
        }
        
    }
public:
    vector<vector<int>> permute(vector<int>& nums) {
        vector<vector<int>> ans; 
        vector<int> ds; 
        int freq[nums.size()];
        for(int i = 0;i<nums.size();i++) freq[i] = 0; 
        recurPermute(ds, nums, ans, freq); 
        return ans; 
    }
};

//Approach 2:

class Solution {
private: 
    void recurPermute(int index, vector<int> &nums, vector<vector<int>> &ans) {
        if(index == nums.size()) {
            ans.push_back(nums); 
            return; 
        }
        for(int i = index;i<nums.size();i++) {
            swap(nums[index], nums[i]); 
            recurPermute(index+1, nums, ans); 
            swap(nums[index], nums[i]); 
        }
        
    }
public:
    vector<vector<int>> permute(vector<int>& nums) {
        vector<vector<int>> ans; 
        recurPermute(0, nums, ans); 
        return ans; 
    }
};

```


## N queens

```cpp

class Solution {

public: 
    void solve(int col, vector<string> &board, vector<vector<string>> &ans, 
               vector<int> &leftRow, 
               vector<int> &upperDiagonal, vector<int> &lowerDiagonal, int n) {
        if(col == n) {
            ans.push_back(board); 
            return; 
        }
    
        
        for(int row = 0;row<n;row++) {
            if(leftRow[row]==0 && lowerDiagonal[row + col] == 0 
               && upperDiagonal[n-1 + col - row] == 0) {
                
                board[row][col] = 'Q'; 
                leftRow[row] = 1; 
                lowerDiagonal[row+col] = 1; 
                upperDiagonal[n-1 + col - row] = 1;
                solve(col+1, board, ans, leftRow, upperDiagonal, lowerDiagonal, n); 
                board[row][col] = '.'; 
                leftRow[row] = 0; 
                lowerDiagonal[row+col] = 0; 
                upperDiagonal[n-1 + col - row] = 0;
            }
        }
    }
public:
    vector<vector<string>> solveNQueens(int n) {
        vector<vector<string>> ans; 
        vector<string> board(n);
        string s(n, '.'); 
        for(int i = 0;i<n;i++) {
            board[i] = s; 
        }
        vector<int> leftRow(n, 0), upperDiagonal(2 * n - 1, 0), lowerDiagonal(2 * n - 1, 0); 
        solve(0,board, ans, leftRow, upperDiagonal, lowerDiagonal, n); 
        return ans; 
    }
    
};
```

----


## Subset sums

Return all subset sums.

```cpp

public: 
    void func(int ind, int sum,vector<int> &arr, int N, vector<int> &sumSubset) {
        if(ind == N) {
            sumSubset.push_back(sum); 
            return; 
        }
        
        // pick the element 
        func(ind + 1, sum + arr[ind], arr, N, sumSubset); 
        
        // Do-not pick the element
        func(ind + 1, sum, arr, N, sumSubset);
    }
public:
    vector<int> subsetSums(vector<int> arr, int N)
    {
        vector<int> sumSubset; //use set directly
        func(0, 0, arr, N, sumSubset);
        sort(sumSubset.begin(), sumSubset.end());
        return sumSubset;
    }
```

----


## [Leetcode 90. Subsets II](https://leetcode.com/problems/subsets-ii/)

Question: Given an integer array nums that may contain duplicates, return all possible subsets (the power set).
The solution set must not contain duplicate subsets. Return the solution in any order.


```cpp
class Solution {
private:
	void subsetsWithDup(std::vector<std::vector<int> > &res, std::vector<int> &nums, std::vector<int> &vec, int begin) {
		res.push_back(vec);
		for (int i = begin; i != nums.size(); ++i)
            //i==begin is required to avoid oob for i-1
            //11112 Here if you have considered first 1 then no need to consider duplicate 1s ahead
			if (i == begin || nums[i] != nums[i - 1]) { 
				vec.push_back(nums[i]);
				subsetsWithDup(res, nums, vec, i + 1);
				vec.pop_back();
			}
	}
public:
    std::vector<std::vector<int> > subsetsWithDup(std::vector<int> &nums) {
		std::sort(nums.begin(), nums.end());
        std::vector<std::vector<int> > res;
		std::vector<int> vec;
		subsetsWithDup(res, nums, vec, 0);
		return res;
    }
};

```

----


## [Combination sum](https://leetcode.com/problems/combination-sum/)

Same as subests

```cpp

//#define Test
#ifdef Test
#define debug(x) cout << #x<<" "; _print(x); cout << endl;
#else
#define debug(x);
#endif

void _print(int t) {cout << t;}
template <class T> 
void _print(vector <T> v) 
{
    cout << "[ "; 
    for (T i : v) 
    {
        _print(i); cout << " ";
    } 
    cout << "]";
}


class Solution {
public:
    
    void func(vector<int>& candidates, vector<int>&ds, int& sum, int index, int target, vector<vector<int>>& ans) 
    {
        if(sum>target) return;
        if(index==candidates.size()){
            debug(ds)
            //cout<<"sum="<<sum<<endl;
            if(sum == target) ans.push_back(ds);
            return;
        }
        
        func(candidates,ds,sum,index+1,target,ans); //dont take current index
        //take current index
        ds.push_back(candidates[index]); 
        sum+=candidates[index];
        func(candidates,ds,sum,index,target,ans);
        sum-=candidates[index];
        ds.pop_back();
    }
    
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        vector<vector<int>> ans;
        vector<int>ds;
        int sum=0;
        func(candidates,ds,sum,0,target,ans);
        return ans;
    }
};

```

## [Combination Sum 2](https://leetcode.com/problems/combination-sum-ii/)

Question :
Given a collection of candidate numbers (candidates) and a target number (target), find all unique combinations in candidates where the candidate numbers sum to target.

Each number in candidates may only be used once in the combination.

```cpp

class Solution {
private:
void func(vector<int>& candidates, vector<int>&ds, int& sum, int index, int target, vector<vector<int>>& ans) 
    {

        if(sum==target) {
            debug(ds)
            ans.push_back(ds);
            return;
        }
        for(int i=index; i<candidates.size(); i++) {
            debug(i)
            if(i==index || candidates[i] != candidates[i-1]) {
                //func(candidates,ds,sum,i+1,target,ans);
                ds.push_back(candidates[i]);
                sum += candidates[i];
                func(candidates,ds,sum,i+1,target,ans);
                sum -= candidates[i];
                ds.pop_back();
            }
        }
    }    
    
public:
    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        vector<vector<int>> ans;
        vector<int>ds;
        sort(candidates.begin(),candidates.end());
        int sum=0;
        func(candidates,ds,sum,0,target,ans);
        return ans;
    }
};

```


## Sudoku Solver


## M coloring 


## Rat in a maze


## Kth permutation

Following example is using sorted input. But it will work even for unsorted input.

<br />ip=[1,2,3,4]
<br />n=4  -> So there are total 4! permutations
<br />k=17 i.e. find 17th permutation of given sequence

<br />    3!
<br />1 (2,3,4)   6
<br />2 (1,3,4)  +6 
<br />3 (1,2,4)  +6
<br />4 (1,2,3)  +6
<br />           24

<br />so 17th (16th in 0 based indexing) permutation will be in 3rd group.

Do recursion on this.

```cpp

class Solution {
public:
    string getPermutation(int n, int k) {
        int fact = 1; 
        vector<int> numbers; 
        for(int i = 1;i<n;i++) {
            fact = fact * i; 
            numbers.push_back(i); 
        }
        numbers.push_back(n); 
        string ans = ""; 
        k = k - 1; 
        while(true) {
            ans = ans + to_string(numbers[k / fact]); //fact is n-1! 
            numbers.erase(numbers.begin() + k / fact); //remove number added to answer
            if(numbers.size() == 0) {
                break; 
            }
            
            k = k % fact; 
            fact = fact / numbers.size();
        }
        return ans; 
    }
};



```