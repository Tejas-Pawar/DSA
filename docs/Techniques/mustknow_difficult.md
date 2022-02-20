# Must Know Techniques

> ## Generate prime numbers till n

Reference: **[GFG Article](https://www.geeksforgeeks.org/sieve-of-eratosthenes/)**

```cpp

// Time Complexity: O(n*log(log(n)))
// C++ program to print all primes smaller than or equal to n using Sieve of Eratosthenes
void SieveOfEratosthenes(int n)
{
    //Create a boolean array "prime[0..n]" and initialize all entries it as true.
    //A value in prime[i] will finally be false if i is Not a prime, else true.
    bool prime[n + 1];
    memset(prime, true, sizeof(prime));
 
    for (int p = 2; p * p <= n; p++)
    {
        // If prime[p] is not changed, then it is a prime
        if (prime[p] == true)
        {
            //Update all multiples of p greater than or equal to the square of it
            //numbers which are multiple of p and are less than p^2 are already been marked.
            for (int i = p * p; i <= n; i += p)
                prime[i] = false;
        }
    }
 
    // Print all prime numbers
    for (int p = 2; p <= n; p++)
        if (prime[p])
            cout << p << " ";
}

```

Follow up question:
> Shortest path to reach one prime to other by changing single digit at a time

```cpp

/* find out all 4 digit prime numbers till 9999 using above technique, 
compare each prime number with all next prime number 
and form a connection if only 1 digit changes
Run BFS traversal to find min path from x to y
*/

class graph {
    int V; 
    list<int>* l; 
public:
    graph(int V)
    {
        this->V = V;
        l = new list<int>[V];
    }
    void addedge(int V1, int V2) 
    {
        //Adjacency List
        l[V1].push_back(V2);
        l[V2].push_back(V1);
    }
    int bfs(int in1, int in2);
};

// in1 and in2 are two vertices of graph which are 
// actually indexes in pset[](prime numbers till 9999)
int graph::bfs(int in1, int in2) 
{
    int visited[V];
    memset(visited, 0, sizeof(visited));
    queue<int> que;
    visited[in1] = 1;
    que.push(in1);
    list<int>::iterator i;
    while (!que.empty()) {
        int p = que.front();
        que.pop();
        for (i = l[p].begin(); i != l[p].end(); i++) {
            if (!visited[*i]) {
                visited[*i] = visited[p] + 1;
                que.push(*i);
            }
            if (*i == in2) {
                return visited[*i] - 1;
            }
        }
    }
}

// Returns true if num1 and num2 differ 
// by single digit.
bool compare(int num1, int num2)
{
    // To compare the digits
    string s1 = to_string(num1);
    string s2 = to_string(num2);
    int c = 0;
    if (s1[0] != s2[0])
        c++;
    if (s1[1] != s2[1])
        c++;
    if (s1[2] != s2[2])
        c++;
    if (s1[3] != s2[3])
        c++;
  
    // If the numbers differ only by a single
    // digit return true else false
    return (c == 1);
}

int shortestPath(int num1, int num2)
{
    // Generate all 4 digit
    vector<int> pset; 
    SieveOfEratosthenes(pset);
  
  
    // Create a graph where node numbers are indexes
    // in pset[] and there is an edge between two 
    // nodes only if they differ by single digit.
    graph g(pset.size()); 
    for (int i = 0; i < pset.size(); i++) 
        for (int j = i + 1; j < pset.size(); j++) 
            if (compare(pset[i], pset[j]))
                g.addedge(i, j);     
      
  
    // Since graph nodes represent indexes of numbers
    // in pset[], we find indexes of num1 and num2.
    int in1, in2;
    for (int j = 0; j < pset.size(); j++) 
        if (pset[j] == num1)
            in1 = j; 
    for (int j = 0; j < pset.size(); j++) 
        if (pset[j] == num2)
            in2 = j; 
  
    return g.bfs(in1, in2);
}

```

> ## Euclidean algorithms

GCD of two numbers is the largest number that divides both of them. A simple way to find GCD is to factorize both numbers and multiply common prime factors.

Basic Euclidean Algorithm for GCD 
The algorithm is based on the below facts. 

If we subtract a smaller number from a larger (we reduce a larger number), GCD doesn’t change. So if we keep subtracting repeatedly the larger of two, we end up with GCD.
Now instead of subtraction, if we divide the smaller number, the algorithm stops when we find remainder 0.
Below is a recursive function to evaluate gcd using Euclid’s algorithm. 

```cpp
//Time complexity: O(Log max(a, b))  
// Function to return gcd of a and b
int gcd(int a, int b)
{
    if (a == 0)
        return b;
    return gcd(b % a, a);
}
```

The extended Euclidean algorithm updates results of gcd(a, b) using the results calculated by recursive call gcd(b%a, a). Let values of x and y calculated by the recursive call be x1 and y1. x and y are updated using the below expressions. 

```cpp
x = y1 - ⌊b/a⌋ * x1
y = x1
```

```cpp

// Function for extended Euclidean Algorithm
int gcdExtended(int a, int b, int *x, int *y)
{
    // Base Case
    if (a == 0)
    {
        *x = 0;
        *y = 1;
        return b;
    }
 
    int x1, y1; // To store results of recursive call
    int gcd = gcdExtended(b%a, a, &x1, &y1);
 
    // Update x and y using results of recursive call
    *x = y1 - (b/a) * x1;
    *y = x1;
 
    return gcd;
}

int main()
{
    int x, y, a = 35, b = 15;
    int g = gcdExtended(a, b, &x, &y);
    cout << "GCD(" << a << ", " << b << ") = " << g << endl;
    return 0;
}

```

How does Extended Algorithm Work? 

```
As seen above, x and y are results for inputs a and b,
   a.x + b.y = gcd                      ----(1)  

And x1 and y1 are results for inputs b%a and a
   (b%a).x1 + a.y1 = gcd   
                    
When we put b%a = (b - (⌊b/a⌋).a) in above, 
we get following. Note that ⌊b/a⌋ is floor(b/a)

   (b - (⌊b/a⌋).a).x1 + a.y1  = gcd

Above equation can also be written as below
   b.x1 + a.(y1 - (⌊b/a⌋).x1) = gcd      ---(2)

After comparing coefficients of 'a' and 'b' in (1) and 
(2), we get following
   x = y1 - ⌊b/a⌋ * x1
   y = x1
```


How is Extended Algorithm Useful? 

The extended Euclidean algorithm is particularly useful when a and b are coprime (or gcd is 1). Since x is the modular multiplicative inverse of “a modulo b”, and y is the modular multiplicative inverse of “b modulo a”. In particular, the computation of the modular multiplicative inverse is an essential step in RSA public-key encryption method.