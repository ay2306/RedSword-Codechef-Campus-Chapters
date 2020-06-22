# Rabin Karp Algorithm Tutorial

## Introduction
Rabin Karp algorithm is used to efficiently search pattern in a string.
Given an input string `text` and pattern `pat`, using this algorithm we can find substrings of `text` that are same as `pat`

## Time Complexity
n = length of `text`
m = length of `pat`
Average Case: O(n + m)
Worst Case: O(nm)

## Key Idea
Just like the naive algorithm for this problem, we will use a sliding window of size m, slide it through the give string and determine if it matches the pattern, but instead of matching the pattern character by character per window, we'll first check if their hash values are equal, if that is the case only then we can check equality per character.

Hence we need to efficiently calculate the Hash values of the sliding values, this is the key idea of Rabin-Karp algorithm.

### Hash Function
Let's define the Radix of the string + pattern to be d, i.e if they contain only lowercase english letters, d = 26.
Considering ASCII character encoding, d = 256.

To calculate hash of a string, we can keep multiplying the computed hash with d and adding the current character's ASCII value.
We'll also take the modulo of the calculated hash as it can be huge.

In code:
```c++
int d  = 256;
string text = "pattern";
long long hash = 0;
int Q = 1000000007;
for (char c: text)
	hash = (hash * d + c) % Q;
```

We can easily compute hash value from a window starting at `i + 1` of length m from a window starting at `i` using the formula:
hash(text[i + 1 ... i + m]) = (d(hash(text[i ... i + m - 1]) - text[i] * h) + text[i + m]) % Q

where h = d ^ (m - 1)

## Implementation
```c++
#include <bits/stdc++.h> 
using namespace std; 
 
// d = Radix value = number of available characters in ASCII encoding
int d = 256;
int Q = 1000000007;

void RabinKarpSearch(string &text, string &pat)  
{  
    int n = text.size();
    int m = pat.size();
    long long p = 0; // hash value for pattern  
    long long t = 0; // hash value for text  
    int h = 1; 
  
    // h = d ^ (m - 1) 
    for (i = 0; i < M - 1; i++)  
        h = (h * d) % Q;  
  
    // Calculate Hash value of pattern and first window.
    for (i = 0; i < M; i++)  
    {  
        p = (d * p + pat[i]) % Q;  
        t = (d * t + text[i]) % Q;  
    }  
  
    // Slide the window of length m throughout the length n of text
    for (int i = 0; i < N - M + 1; i++)  
    {  
  		// If hash values of current window equals hash value of pattern, match the strings character by character.
        if (p == t)  
        {  
            for (int j = 0; j < m; j++)  
            {  
                if (text[i+j] != pat[j])  
                    break;  
            }
            if (j == m)  
                cout << "Patter matched at: "<< i << "\n";  
        }  
  
        // Calculate hash value for next window of text: Remove  
        // leading digit, add trailing digit  
        if (i < N - M)  // only do it if we are not at last window
        {  
            t = (d * (t - text[i]*h) + text[i + M]) % Q;  
  
            if (t < 0) // if we get negative value.  
  	          t += Q;  
        } 
    }  
}  
  
int main()  
{  
    string text = "Random Text, and sensible text";  
    string pat = "and";   
    search(pat, txt);  
    return 0;  
}  
```

## Question
A good question requiring usage of this algorithm is "Longest Duplicate Substring", it can be found at https://leetcode.com/problems/longest-duplicate-substring/

### Problem Statement:
Given a string S, consider all duplicated substrings: (contiguous) substrings of S that occur 2 or more times.  (The occurrences may overlap.)

Return any duplicated substring that has the longest possible length.  (If S does not have a duplicated substring, the answer is "".)

#### Constraints:
1. 2 <= S.length <= 10^5
2. S consists of only lowercase English letters

### Editorial

#### Key Idea:
If there's a duplicate substring of length k, we only need to check if there is a duplicate substring of length > k.

#### Solution:
Use Rabin-karp's algorithm to find if there's a duplicate string of length n/2. If there is one, we only need to check if there's a duplicate substring of length > n / 2, otherwise we need to check in length < n / 2. Hence apply binary search.

Since only lowercase english letters are used, the radix (d) = 26.

#### Code:
```c++
class Solution {
    
    int d = 26;
    int Q = 1000000007;
    
    string ans = "";
    
    // check if duplicate substrings of length m exists, sets ans to that.
    bool checkDuplicates(string &s, int m)
    {
        int n = s.size();
        map<int, int> hashes; // hashvalue : startIndex
        long long h = 1;
        
        for (int i = 0; i < m - 1; i++)
            h = (h * d) % Q;
            
        long long p = 0; // hash value
        for (int i = 0; i < m; i++)
        {
            p = (d * p + s[i]) % Q;
        }
        
        // Slide over the string one by one.
        for (int i = 0; i <= n - m; i++)
        {   
            // if p is already present in hashes, then do final check.
            if (hashes[p])
            {
                int j = hashes[p] - 1;
                bool same = true;
                string temp = "";
                for (int k = 0; k < m; k++)
                {
                    temp += s[i + k];
                    if (s[i + k] != s[j + k])
                    {
                        same = false;
                        break;
                    }
                }
                if (same)
                {
                    ans = temp;
                    return true;
                }
            }
            
            hashes[p] = i + 1;
            if (i == n - m)
                continue;
            
            // calculate hash value for next window.
            p = (d * (p - s[i] * h) + s[i + m]) % Q;
            if (p < 0)
                p += Q;
        }
        return false;
    }
public:
    string longestDupSubstring(string& s) {
        int l = 0, r = s.size() - 1;
        while (l <= r)
        {
            int mid = l + (r - l) / 2;
            if (checkDuplicates(s, mid))
            {
                l = mid + 1;
            }
            else
                r = mid - 1;
        }  
        return ans;
    }
};
```
