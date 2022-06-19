
## String matching using polynomial rolling hash

Calculate hash(text) and hash(pattern)
hash(s) = p^0 * s[0] + p^1 * s[1] + p^2 * s[2] + p^3 * s[3] + p^4 * s[4] ...

![](../img/rollinghash1.png)

![](../img/rollinghash2.png)

Here you can directly divide by P^2. So you need modulo multiplicative inverse.

```cpp

#include<bits/stdc++.h>
#define REP(i,n) for (int i = 1; i <= n; i++)
#define mod 1000000007
#define pb push_back
#define ff first
#define ss second
#define ii pair<int,int>
#define vi vector<int>
#define vii vector<ii>
#define lli long long int
#define INF 1000000000
#define endl '\n'
const double PI = 3.141592653589793238460;
typedef std::complex<double> Complex;
typedef std::valarray<Complex> CArray;
using namespace std;
lli dp[1000001];
lli inv[1000001];

lli power(lli a , lli n)
{
	lli result = 1;
	
	while(n)
	{
		if(n & 1) result = (result * a) % mod;
		
		n >>= 1;
		a = (a * a) % mod;
	}
	
	return result;
}


void init(string input_string)
{
	lli p = 31;
	lli p_power = 1;
	inv[0] = 1;
	dp[0] = (input_string[0] - 'a' + 1);
	
	
	for(int i=1;i<input_string.size();i++)
	{
		char ch = input_string[i];
		
		p_power = (p_power * p) % mod;
		inv[i] = power(p_power , mod - 2);
		
		dp[i] = (dp[i-1] + (ch - 'a' + 1)*p_power) % mod;
	}
}

lli substringHash(int L , int R)
{
	int result = dp[R];
	
	if(L > 0) result -= dp[L-1];
	
	result = (result * inv[L]) % mod;
	
	return result;
}

int main()
{
	string input_string;
	int tc , L , R;
	
	cin>>input_string;
	init(input_string);
	
	cin>>tc;
	
	REP(i , tc)
	{
		cin>>L>>R;
		cout<<substringHash(L , R)<<endl;
	}
}



```


## String matching using Rabin Karp


Same as above but no need to calculate modulo inverse.

pattern * p^L = hash[r] - hash[l-1]


## String matching using Z function

https://youtu.be/hDKTCWWGIxk?list=PL2q4fbVm1Ik6ThrYKCzgEpmaS_XWDGHjx&t=580

https://cp-algorithms.com/string/z-function.html

![](../img/zalgo.png)

```cpp

vector<int> z_function(string s) {
    int n = (int) s.length();
    vector<int> z(n);
    for (int i = 1, l = 0, r = 0; i < n; ++i) {
        //i<=r means that there was a segment match whose right pointer is to the right of i
        //So we can take headstart by peeking value from z[i-l]
        if (i <= r)
            z[i] = min (r - i + 1, z[i - l]); //r-i+1 is the boundary condition

        //i+z[i]<n is boundary condition. Calculate z[] for e.g. a a a a a a a
        while (i + z[i] < n && s[z[i]] == s[i + z[i]])
            ++z[i]; //z[i] = Length of matching prefix from 

        //Update l and r only if there is new rightmost segment match
        if (i + z[i] - 1 > r)
            l = i, r = i + z[i] - 1;
    }
    return z;
}

```


## String matching using KMP

https://www.youtube.com/watch?v=4jY57Ehc14Y

```cpp

vector<int> prefix_function(string s) {
    int n = (int)s.length();
    vector<int> LPS(n);
    for (int i = 1; i < n; i++) {
        int j = LPS[i-1];
        while (j > 0 && s[i] != s[j])
            j = LPS[j-1];
        if (s[i] == s[j])
            j++;
        LPS[i] = j;
    }
    return LPS;
}

//self written function for LPS
vector<int> computeLPS(string needle) {
    int i=1;
    int len=0;
    vector<int> lps(needle.size(),0);
    lps[0]=0;
    while(i<needle.size()) {
        if(needle[i]==needle[len]) {
            len++; lps[i]=len; i++; 
        } else {
            if(len!=0) len = lps[len-1]; //headstart: lps of previous element 
            else{
                lps[i]=0;
                i++;
            }
        }
    }
    return lps;
}

int strStr(string haystack, string needle) {
    vector<int> lps;
    if(needle.size()==0) return 0;
    lps = computeLPS(needle);
    int i=0, j=0;
    
    while(i<haystack.size()) {
        if(haystack[i] == needle[j]) {
            i++; j++;
        } else {
            if(j!=0) { 
                //Few chars have matched in current segment
                //We can take headstart from LPS
                j = lps[j-1];
            } else {
                //since first char of segment doesn't match, just increment i
                i++; 
            }
        }
        if(j==needle.length()) {
            //j = lps[j-1]; //in case you want all the occurrences
            return i-j;
        } 
    }
    return -1;
}

```