# Moore's Voting Algorithm

This is a pretty easy question to solve: 
>Given an array of size _n_, find the majority element. The majority element is the element that appears **more than**  `⌊n/2⌋` times.

There are straight-forward solutions using hashmaps which uses O(N) time and O(N) space complexity, or using sorting which uses O(N) time and O(1) space complexity (code for these two are also provided)

But Moore's Voting algorithm is an important algorithm which let's us solve this question in O(1) space complexity and O(N) time.

#### Algorithm
1. Maintain 2 variables `count` and `candidate`, initially put first element as `candidate` and `count = 0`
2. Iterate Loop from first to last element
3. While iterating 
	a. `if element == candidate`, then add 1 to `count`
	b. otherwise decrease subtract 1 from `count`
	d. if `count >= ⌊n/2⌋` then this is the answer
	c. `if count == 0` then set `candidate = element` and `count = 1`
4.  When you stop iterating a loop, you have a `candidate` for answer, Count its frequency in O(N) steps to verify it as the answer


#### Intuitive Proof (from Quora)

>Imagine that there are `2m+1` people and every one has a vote. He can use this vote to add 1 point for himself, or to take 1 point off another. As we already know that there will be a winner `X` (he has at least m supporters). Consider the toughest situation for `X` to win the election: All the other m people ally with each other and forms one big group. Even in this situation, `X` will have at least 1 points in the end, while others all sacrifices. Not to mention that the other m people may form smaller groups and fight with each other (Moore Voting Algorithm actually is a reflection of this situation)

**Question-Link:** [LeetCode Question](https://leetcode.com/problems/majority-element/)

#### Sorting Solution

```cpp
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        sort(nums.begin(),nums.end());
        int cnt = 0;
        for(int i = 0; i < nums.size(); ++i){
            if(i == 0 || nums[i] == nums[i-1])cnt++;
            else cnt = 1;
            if(cnt > (nums.size() >> 1))return nums[i];
        }
        return 0;
    }
};
```

#### HashMap Solution

```cpp
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        map<int,int> m;
        for(int i : nums){
            m[i]++;
            if(m[i]*2 > nums.size())return i;
        }
        return 0;
    }
};
```
#### Moore's Solution

```cpp
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int f = 0 , cnt = 0, val = nums[0];
        for(int i : nums){
            if(i == val)cnt++;
            else cnt--;
            if(cnt == 0)val=i,cnt=1;
            if(cnt > (nums.size()>>1))return val;
        }
        // We don't need this part as question already states
        // that answer exists and candidate will be answer if it exists
        for(int i : nums)f+=(i==val);
        return f > (nums.size() >> 1) ? val : 0;
    }
};
```