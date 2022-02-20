## BFS

```cpp
class Solution {
public:
	vector<int>bfsOfGraph(int V, vector<int> adj[]){
	    vector<int> bfs; 
	    vector<int> vis(V, 0);
	    queue<int> q; 
	    q.push(0); 
	    vis[0] = 1; 
	    while(!q.empty()) {
	        int node = q.front();
	        q.pop(); 
	        bfs.push_back(node); 
	        
	        for(auto it : adj[node]) {
	            if(!vis[it]) {
	                q.push(it); 
	                vis[it] = 1; 
	            }
	        }
	    }
	    
	    return bfs; 
	}
};
```

## DFS

```cpp
class Solution {
    void dfs(int node, vector<int> &vis, vector<int> adj[], vector<int> &storeDfs) {
        storeDfs.push_back(node); //Storing to print output later
        vis[node] = 1; 
        for(auto it : adj[node]) {
            if(!vis[it]) {
                dfs(it, vis, adj, storeDfs); 
            }
        }
    }
public:
    //DFS of all disconnected graphs
	vector<int>dfsOfGraph(int V, vector<int> adj[]){
	    vector<int> storeDfs; 
	    vector<int> vis(V+1, 0); 
        for(int i = 1;i<=V;i++) {
            if(!vis[i]) dfs(i, vis, adj, storeDfs); 
        }
	    return storeDfs; 
	}
};
```

## Cycle detection in Undirected Graph using BFS

Do BFS traversal while maintaining parent in queue. 
If any node is already visited which is not parent, then there is a cycle.
```cpp

class Solution {

public:
    bool checkForCycle(int s, int V, vector<int> adj[], vector<int>& visited)
    {
        // Create a queue for BFS
        queue<pair<int,int>> q; //pair<node,parent>
     
        visited[s] = true;
        q.push({s, -1});
     
        while (!q.empty()) {
     
            int node = q.front().first;
            int par = q.front().second;
            q.pop();
     
            for (auto it : adj[node]) {
                if (!visited[it]) {
                    visited[it] = true;
                    q.push({it, node});
                }
                else if (par != it) //Important logic for cycle detection
                    return true;
            }
        }
        return false;
    }
public:
	bool isCycle(int V, vector<int>adj[]){
	    vector<int> vis(V, 0); 
        //Traverse disconnected components
	    for(int i = 0;i<V;i++) {
	        if(!vis[i]) {
	            if(checkForCycle(i, V, adj, vis)) return true; 
	        }
	    }
	    
	    return false; 
	}
};

```

## Cycle Detection in Undirected Graph using DFS
Cycle present, If any node other than previous node is already visited.

```cpp
class Solution {

public:
    bool checkForCycle(int node, int parent, vector<int> &vis, vector<int> adj[]) {
        vis[node] = 1; 
        for(auto it: adj[node]) {
            if(!vis[it]) {
                if(checkForCycle(it, node, vis, adj)) 
                    return true; 
            }
            else if(it!=parent) //Important logic for cycle logic
                return true; 
        }
        
        return false; 
    }
public:
	bool isCycle(int V, vector<int>adj[]){
	    vector<int> vis(V+1, 0); 
	    for(int i = 0;i<V;i++) {
	        if(!vis[i]) {
	            if(checkForCycle(i, -1, vis, adj)) return true; 
	        }
	    }
	    
	    return false; 
	}
};

```

## Bipartite Graph (BFS) | Graph Coloring

Graph that can be colored using 2 colors such that there are no 2 adjacent nodes with same color.

![](../img/bipartite_graph.png)

Observation : No odd length cycle = Bipartite Graph

Implementation : BFS Traversal with color array instead of visited array

```cpp

bool bipartiteBfs(int src, vector<int> adj[], int color[]) {
    queue<int>q;
    q.push(src); 
    color[src] = 1; 
    while(!q.empty()) {
        int node = q.front(); 
        q.pop();
        
        for(auto it : adj[node]) {
            if(color[it] == -1) {
                color[it] = 1 - color[node]; 
                q.push(it); 
            } else if(color[it] == color[node]) {
                // If already colored(visited) and has same color then return false
                return false; 
            }
        }
    }
    return true; 
}
bool checkBipartite(vector<int> adj[], int n) {
    int color[n];
    memset(color, -1, sizeof color); 
    for(int i = 0;i<n;i++) {
        if(color[i] == -1) {
            if(!bipartiteBfs(i, adj, color)) {
                return false;
            }
        }
    }
    return true; 
}

```

## Bipartite Graph (DFS) | Graph Coloring

DFS while maintaining color array instead of visited array.

```cpp

bool bipartiteDfs(int node, vector<int> adj[], int color[]) {
    for(auto it : adj[node]) {
        if(color[it] == -1) {
            color[it] = 1 - color[node];
            if(!bipartiteDfs(it, adj, color)) {
                return false; 
            }
        }
        // If already colored and has same color then return false
        else if(color[it] == color[node]) return false; 
        
    }
    return true; 
}
bool checkBipartite(vector<int> adj[], int n) {
    int color[n];
    memset(color, -1, sizeof color); 
    for(int i = 0;i<n;i++) {
        if(color[i] == -1) {
            color[i] = 1;
            if(!bipartiteDfs(i, adj, color)) {
                return false;
            }
        } 
    }
    return true; 
}

```

## Cycle Detection in Directed Graph using DFS

Keep 2 arrays 

1. vis : maintain overall visited for disconnected components

2. dfsVis : visited in current dfs. (Backtrack while returning)
If adjacent node is present in dfsVis then cycle is present.

```cpp

class Solution {
private:
    bool checkCycle(int node, vector<int> adj[], int vis[], int dfsVis[]) {
        vis[node] = 1; 
        dfsVis[node] = 1; 
        for(auto it : adj[node]) {
            if(!vis[it]) {
                if(checkCycle(it, adj, vis, dfsVis)) return true;
            } else if(dfsVis[it]) {
                return true; 
            }
        }
        dfsVis[node] = 0; 
        return false;
    }
public:
	bool isCyclic(int N, vector<int> adj[]) {
	   int vis[N], dfsVis[N]; 
	   memset(vis, 0, sizeof vis); 
	   memset(dfsVis, 0, sizeof dfsVis); 
	   
	   for(int i = 0;i<N;i++) {
	       if(!vis[i]) {
	           // cout << i << endl; 
	           if(checkCycle(i, adj, vis, dfsVis)) {
	               return true; 
	           }
	       }
	   }
	   return false; 
	}
};


```

## Topological sort (DFS)

Topological sort: Linear ordering of vertices such that if there is an edge u->v, u appears before v in that ordering.

DFS with auxilliary stack to maintain toposort.
Add node to auxilliary stack on completion of dfs call of each node. 
This way last node will be added to stack first. 
Popping nodes from stack will give topo sort.

```cpp

class Solution{
    void findTopoSort(int node, vector<int> &vis, stack<int> &st, vector<int> adj[]) {
        vis[node] = 1; 
        
        for(auto it : adj[node]) {
            if(!vis[it]) {
                findTopoSort(it, vis, st, adj); 
            }
        }
        st.push(node); 
    }
	public:
	vector<int> topoSort(int N, vector<int> adj[]) {
	    stack<int> st; 
	    vector<int> vis(N, 0); 
	    for(int i = 0;i<N;i++) {
	        if(vis[i] == 0) {
	            findTopoSort(i, vis, st, adj); 
	        }
	    }
	    vector<int> topo;
	    while(!st.empty()) {
	        topo.push_back(st.top()); 
	        st.pop(); 
	    }
	    return topo; 
	    
	}
};

```

## Topological Sort (BFS) | Kahn's Algorithm

Setup:
Create an indegree vector.
Add nodes with indegree 0 to queue.

Loop:
Pop front element from queue, and do BFS and decrement indegree on visiting.
If indegree becomes 0, add it to queue.


```cpp

class Solution {
public:
	vector<int> topo(int N, vector<int> adj[]) {
        queue<int> q; 

        //create and update indegree vector
	    vector<int> indegree(N, 0); 
	    for(int i = 0;i<N;i++) {
	        for(auto it: adj[i]) {
	            indegree[it]++; 
	        }
	    }

        //Add nodes with indegree 0 to queue
	    for(int i = 0;i<N;i++) {
	        if(indegree[i] == 0) {
	            q.push(i); 
	        }
	    }

	    vector<int> topo;
	    while(!q.empty()) {
	        int node = q.front(); 
	        q.pop(); 
	        topo.push_back(node); //update toposort on popping node from queue
	        for(auto it : adj[node]) {
	            indegree[it]--;
	            if(indegree[it] == 0) {
	                q.push(it); 
	            }
	        }
	    }
	    return topo;
	}
};


```

## Cycle Detection in Directed Graph using BFS(Kahn's Algorithm)

Observation: Topological sort is not possible with cyclic graph.

Algo: Try to generate topological sort with above Kahn's algorithm.
If successful, then no cycle.

```cpp hl_lines="18 22 32"

class Solution {
public:
	bool isCyclic(int N, vector<int> adj[]) {
        queue<int> q; 
	    vector<int> indegree(N, 0); 
	    for(int i = 0;i<N;i++) {
	        for(auto it: adj[i]) {
	            indegree[it]++; 
	        }
	    }
	    
	    for(int i = 0;i<N;i++) {
	        if(indegree[i] == 0) {
	            q.push(i); 
	        }
	    }
	    int cnt = 0; //Counter
	    while(!q.empty()) {
	        int node = q.front(); 
	        q.pop(); 
	        cnt++; 
	        for(auto it : adj[node]) {
	            indegree[it]--;
	            if(indegree[it] == 0) {
	                q.push(it); 
	            }
	        }
	    }
	    if(cnt == N) return false;  //Cycle check
	    return true; 
	}
};


```

## Shortest Path in Undirected Graph with Unit Weights

Initialise distance vector with INT_MAX.
Basically a BFS but add to queue only if current node distance + 1 is less that distance in distance vector i.e. if(dist[node] + 1 < dist[it]).


```cpp

void BFS(vector<int> adj[], int N, int src) 
{ 
	int dist[N];
	for(int i = 0;i<N;i++) dist[i] = INT_MAX; 
	queue<int>  q;
	
	dist[src] = 0;
	q.push(src); 

	while(q.empty()==false) 
	{ 
		int node = q.front(); 
		q.pop();
		 
		for(auto it:adj[node]){
		    if(dist[node] + 1 < dist[it]){
		        dist[it]=dist[node]+1;
		        q.push(it);
		    }
		} 
	} 
	for(int i = 0;i<N;i++) cout << dist[i] << " "; 
	
} 

```