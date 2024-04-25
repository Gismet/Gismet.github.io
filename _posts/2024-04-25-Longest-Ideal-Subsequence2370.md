---
title: 2370. Longest Ideal Subsequence
description:
author: Gismet Majidov
date: 2024-04-25 14:22:00 +0800
categories: [Problem-Solving, LeetCode]
tags: [LeetCode, Dynamic programming, String]
math: true
---


# [2370. Longest Ideal Subsequence](https://leetcode.com/problems/longest-ideal-subsequence/description/)

You are given a string `s` consisting of lowercase letters and an integer k. We call a string `t` **ideal** if the following conditions are satisfied:

 * `t` is a **subsequence** of the string `s`.
 * The absolute difference in the alphabet order of every two **adjacent** letters in `t` is less than or equal to `k`.
  

Return the length of the **longest** ideal string.

A **subsequence** is a string that can be derived from another string by deleting some or no characters without changing the order of the remaining characters.

**Note** that the alphabet order is **not cyclic**. For example, the absolute difference in the alphabet order of `'a'` and `'z'` is `25`, not `1`.


**Example 1:**

**Input: s = "acfgbd", k = 2** <br/>
**Output: 4** <br/>
**Explanation: The longest ideal string is "acbd". The length of this string is 4, so 4 is returned.
Note that "acfgbd" is not ideal because 'c' and 'f' have a difference of 3 in alphabet order.**


**Example 2:**

**Input: s = "abcd", k = 3** <br/>
**Output: 4** <br/>
**Explanation: The longest ideal string is "abcd". The length of this string is 4, so 4 is returned.**



## Solution explanation

Given a character `c` and an array of **ideal** strings, we need to find the longest **ideal** string that ends with a character whose absolute difference with `c` is less than or equal to `k`.

Let's see an exmaple


> ideal strings = `["abc", "bd", "cf", "fgg"]` and `c` = `'d'` and `k = 2`

Now we need to find the longest **ideal** string among our strings. 

Let's check them one by one, starting off with `"abc"`. 

`"abc"` ends with `'c'`. Let's denote the absoltue difference of two characters as `diff`.

Then  $diff = |d - c| = 1$. So $diff <= k$. Now we have a new **ideal** string `"abcd"` of length `4`.


`"bd"` ends with `'d'`. $diff = |d - d| = 0$. So $diff <= k$. Now we have a new **ideal** string `"bdd"` of length 3.

`"cf"` ends with `'f'`. $diff = |f - d| = 2$. $diff <= k$. Now we have a new **ideal** string `"cfd"` of length 3. 

`"fgg"` ends with `'g'`. $diff = |g - d| = 3$. $diff > k$. We can't add `'d'` to the end of `"fgg"`. So we ignore it.

For the given character `c = 'd'`, we only need to consider the **ideal** strings that ends with a character that at most `2` away from `'d'` in the alphabet. These characters are `['b', 'c', 'd', 'e', 'f']`. `'d'` is zero away from `'d'`. `'c'` and `'e'` are one away from `'d'`. Finally `'b'` and `'f'` are two away. 

If we know the longest **ideal** strings that ends with each one of these characters, then we take the longest of those **ideal** strings and add the current character `'d'` to get another **ideal**  string. Now the new **ideal** string ends with `'d'` and we need to update the longest **ideal** string that previous ended with `'d'`. If the current new **ideal** string is longer than the previous longest string, we replace it with the current string. 

For the above case, we found that `"abcd"` was the longest **ideal** string we could get. 

To consider our problem, we will start from the left of the string and proceed charcter by character. For the current character, we will consider all the valid **ideal** strings to the end of which the current character can be appended. We will take the longest of those, and append the current character. The string we get after appending is the longest **ideal** string that ends with the current character. We will keep track of the longest **ideal** string seen so far. 


## Code

```cpp
#define vv std::vector

class Solution {
public:
    int longestIdealString(string s, int k) {
        // the longest ideal strings that ends with a certain character
        vv<int> dp(26);

        //the longest seen so far
        int ans = 0;

        for (char c : s)
        {
            //subtracting the ASCII value of 'a' from the current character
            //to get a value between 0 and 25
            int x = (c - 'a');
            int curr = 0;
            //Look for all the characters that are k away from the current
            //character in the alphabet
            for (int i = (x - k) > 0 ? (x - k) : 0; i < 26 && i <= (x + k); i++)
            {
                curr = std::max(curr, dp[i]);
            }
            ++curr; // add one for the appending of the current character
            dp[x] = curr; // the longest ideal string ending with the current character
            //update the answer
            ans = std::max(dp[x], ans);
        }

        return ans;
    }
};
```

**I hope you enjoyed this article and there are more articles to come!** <br/>
**If you have any suggestions, please feel free to write them down in the comments below.**