---
title: LeetCode 421. Maximum XOR of Two Numbers in an Array
description: Given an integer array nums, return the maximum result of nums[i] XOR nums[j], where 0 <= i <= j < n.
author: Gismet Majidov
date: 2024-04-07 12:58:00 +0800
categories: [Problem-Solving, LeetCode]
tags: [LeetCode, Bit Manipulation, Trie]
math: true
---

# 421 Maximum XOR of Two Numbers in an Array

Given an integer array nums, return the maximum result of `nums[i] XOR nums[j]`, where `0 <= i <= j < n`.

**Example 1:** 

**Input: nums = [3,10,5,25,2,8]** <br/>
**Output: 28** <br/>
**Explanation: The maximum result is 5 XOR 25 = 28.**

**Example 2:**

**Input: nums = [14,70,53,83,49,91,36,80,92,51,66,70]** <br/>
**Output: 127**


# Approach 

When is the value of `a ^ b` maximum ? To answer the question let's see an example. 

`101` and `101`. If we take XOR of these two numbers, we get `0`. Why ?
As all of the bits of two numbers are identical, we get `0` as a result of XOR operation. 

```
    1011
XOR  
    0101
   ______
    1110
```

We get a `0` bit when both bits are the same, meaing `0 XOR 0` and `1 XOR 1` give us `0`. When bits differ, we get a `1` bit, meaning `1 XOR 0` and `0 XOR 1` give us `1`. 

So if we want to maximize `a XOR b`, we want as many oppesite bits as possible. For example, if the current bit of `a` is `1`, then we want the current bit of `b` to be `0`. Ideally we want `b` to be the complement of `a` or vise versa. 

>**What is the complement of a number ?**

We can obtain the complement of an integer in `C++` by using the following syntax. 

```cpp
int n = 5; //0b101
int m = ~i; //0b010
```
We get the complement of a number by inverting each bit (makeing `0` bit `1` and vise versa).

For a given number `n`, if there exist its complement, then taking XOR of `n` and its complement result in the maximum value for `n`. 

Consequently, for each number `n` in the array, we have to look for a number `x` that has as many same bits as possible with the complement of `n`. It is possible that we also find exact complement of `n` as well. 
Furthermore, we want the matching bits as far left as possible. Because it is good to have matching bits near to `MSB (Most Significant Bit)`, which is the left most bit. 

For instance, `n` is `1001101`. Its complement is `0110010`. We have to look for the aforementioned number `x`. If we find that `x` is `0110010`, then it is great. We have found the exact complement of `n`. We might find something like `0010010`. That was not bad. Only one bit (3rd bit from left) is defferent. The rest matches the complement. 

So here's our idea. For each number in the array, we will search for a number that is closest to the complement of the current number in terms of matching bits. Then we will take XOR of the current number and the found number and update our answer accordingly. 

> **How are we going to find that number?**

Essentially, we will work with the binary forms of the numbers. We are working on 32-bit type integers. 

For doing the actual matching, we will use `trie` data structure.

> [**Trie**](https://en.wikipedia.org/wiki/Trie) is a tree-like data structure most suitable for efficient prefix and suffix searching.
{: .prompt-info}

In our problem, we will build the trie based on the given numbers in the array. We will use the full 32 bit binary representation of each number. After adding each number to the `trie`, we will do the actual matter - searching for the closest match for complement of each number. 

In a usual trie implementation, there is a query operation that search for a given prefix in the `trie`. When it falls to find a prefix match, it directly returns `false` to implay that the given prefix does not match any string in the `trie`. 

In our case we do not want that as we want to match as many bits as possible while allowing some bits to be different than the complement. While searching the `trie`, if the current bit in complement does not match the current bit of the `trie`, we will choose the other bit of the `trie` and proceed with the rest of the bits of  the complement.

Now let's have a look at the code. 

**Before looking at the code, please acquaint yourself with the following bit manipulation techniques.**

> - `x << k` left bit shift operator. It shifts bits of `x` `k` bit to the left. <br/>
> - `x >> k` right bit shift operator. It shifts bits of `x` `k` bit to the right <br/>
> - `x & y` `AND` operator gives a number that has one bits in positions where `x` and `y` have set bits (one bits) <br/>
> - `x | y` `OR` operator gives a number that has one bits in positions where at leasat one of `x` and `y` has a one bit. <br/>
> - `x ^ y` `XOR` operator gives a number that has one bits in postions where `x` and `y` have different bits. <br/>
> - `~x` `NOT` operator gives a number where all bits of `x` have been flipped (`0` turns into `1` and vise versa)
> - `1 << k` bit mask of this form has a one bit in position k (0-indexed). The rest of the bits are zero. We use this bit mask to access a single bit of a number. You can combine this bit mask for different bit manipulation purposes, such as setting a bit to one or zero, flipping a particular bit.
> - Last but not least, a postive integer `x` is a power of two if `x & (x - 1) == 0`.
{: .prompt-tip}


```cpp
//Build the trie on the given array
//Then search the complement of each number in the trie
//There might not be the exact complement of the given number 
//We will match as much as possible
//then we take the XOR of the curren number and what we found in trie.

struct Trie
{
    Trie* nodes[2];
    Trie() {
        nodes[0] = NULL;
        nodes[1] = NULL;
    }
};

void insert(Trie*& t, int n)
{
    Trie* curr = t;
    for(int i = 31; i >= 0; i--)
    {
        int bit = (n&(1<<i)) == 0 ? 0 : 1; //ith bit
        if(curr->nodes[bit] == NULL)
            curr->nodes[bit] = new Trie();
        curr = curr->nodes[bit];
    }
}

int find(Trie*& t, int n)
{
    Trie* curr = t;
    int res = n;
    for(int i = 31; i >= 0; i--)
    {
        int bit = (n&(1<<i)) == 0 ? 0 : 1;
        if(curr->nodes[bit] == NULL)
        {
            res = res ^ (1<<i);
            curr = curr->nodes[bit ^ 1];
        } else 
        {
            curr = curr->nodes[bit];
        }
    }

    return res;
}

void bin(int n )
{
    for(int i = 31; i >= 0; i--)
    {
       int x = (n&(1<<i)) == 0 ? 0 : 1;
       std::cout << x ;
    }
}

class Solution {
public:

    Solution()
    {
        std::ios_base::sync_with_stdio(false); std::cin.tie(NULL);
    }

    int findMaximumXOR(vector<int>& a) {
        Trie* t = new Trie();
        int ans = 0;
        for(int i : a)
        {
            insert(t, i);
        }

        for(int i : a)
        {
            int x = find(t, ~i);
            ans = std::max(ans, (x ^ i));
        }

        return ans;
    }
};
```

**I hope you enjoyed your time. Come back for more!.**
