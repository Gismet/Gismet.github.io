---
title: LeetCode -678 Valid Parenthesis String 
description: 
author: Gismet Majidov
date: 2024-04-08 18:58:00 +0800
categories: [Problem-Solving, LeetCode]
tags: [LeetCode, Dynamic programming, Stack, Greedy]
math: true
---

# 678 Valid Parenthesis string

Given a string `s` containing only three types of characters: `'('`, `')'` and `'*'`, return true if s is valid.

The following rules define a valid string:

- Any left parenthesis `'('` must have a corresponding right parenthesis `')'`.
- Any right parenthesis `')'` must have a corresponding left parenthesis `'('`.
- Left parenthesis `'('` must go before the corresponding right parenthesis `')'`.
- `'*'` could be treated as a single right parenthesis `')'` or a single left parenthesis `'('` or an empty string `""`.


**Example 1 :**

`Input: s = "()"`

`Output: true`


**Example 2 :**

`Input: s = "(*)"`

`Output: true`


**Example 3 :**

`Input: s = "(*))"`

`Output: true`



Let's solve this problem. 


## Simplified problem
The problem would be easy if it asked us to check if the given string consisting of `'('` and `')'` characters is valid. 

For example, if we had to check whether or not this string `"()((())())"` is valid, we could do that really easily. In a valid string like this, the number of opening parentheses `'('` is equal to the closing parentheses `')'`. So in other words, each opening parantheses will be balanced by a corresponding closing parantheses. For example, if we have `5` opening parantheses in a valid string, then we will have `5` closing parantheses. 

Let's have a look at `"()((())())"`. In this string, we have `5` `'('` and `5` `')'`. We would start with a counter, which represents the number of `'('` encountered so far that has not been balanced by `')'`. Each time we come across a `'('`, we increment the counter. Otherwise, the character is a `')'`, then we would decrement the counter. If the counter is zero, this means we do not have any opening parantheses that would match the current closing parantheses `')'`, thus we can stop the process and return false.  

If we process all of the characters without counter going down to zero, we successfully matched all the closing parantheses with a corresponding opening parantheses. Now we have to check if we actually used all of the opening parantheses, meaning that we have to check if counter is zero. If it is zero, the string is valid, otherwise it is not valid. 


## Approach
In our problem, we have also `'*'`, which can denote any of `""`, `'('`, `')'`. For example, the string `"(*)"` could be `"()"`, `"(()"`, `"())"`, where `'*'` is replaced by `""`, `'('`, `')'` respectively. 

So for each `'*'`, there are 3 possibilities of replacement. We replace it with any of these `""`, `'('`, `')'`. Now let's say there are `k` characters of `'*'`. For each, we have 3 choices, giving us a total of **$3^k$** possible strings. 

One possible solution is to solve this problem with a [dynamic programmign](https://codeforces.com/blog/entry/43256) approach. Particularly memoization approach. 

Imagine a function `f(counter, index, string)` that takes the number of opening parantheses and an index and the string. This function checks if the given string is valid startign from the given index by taking the given counter into account. We can use this function to solve the problem of checking if a given string is valid, where the string does not contain `'*'`. 

Given `s = "(())()"`, the function call `f(0, 0, s)` would return true, as the given string is valid. 

We can also use this function to solve our original problem. If at index, we come across `'*'`, we know that that means three 3 possible choices, meaning replaceing it with `""`, '`('`, or `')'`. If we replace it with empty string, the counter does not change, so we call `f(counter, index + 1, s)`. If we replace it with `'('`, the counter increases by `1` and we call `f(counter + 1, index + 1, s)`. And if we replace it with `')'`, then counter decreases by `1`. And of course, we don't forget to check if the counter is already zero much as we did in the solution of simplified problem. 
If it is zero, we return `false`, otherwise we call `f(counter - 1, index + 1, s)`. If any of these function calls returns `true`, then we return `true`. 

Now let's see the solution 

> Note: I have used some shortcuts in the code such as `vv`.
{: .prompt-info }

> Note: This problem can be solved in `O(n)` time and `O(1)` space with a greedy approach, which can be driven from the dynamic programming solution
{: .prompt-info } 

```cpp
#define vv std::vector
vv<vv<int>> dp;

bool solve(int cnt, int i, std::string &s)
{
    //this means we ran out of opening parantheses in the previous index
    if (cnt < 0) 
        return false;
    //check if we reached the end of the string
    if (i >= int(s.length()))
    {
        if (cnt == 0)
            return true;
        else
            return false;
    }

    //check if we have already calculated the answer for this subproblem
    if (dp[cnt][i] != -1)
        return dp[cnt][i];

    //increment the counter and move on to the next index
    if (s[i] == '(')
    {
        dp[cnt][i] = solve(cnt + 1, i + 1, s);
        return dp[cnt][i];
    }
    else if (s[i] == ')')
    {
        //counter is zero, we don't have any opening parantheses
        if(cnt == 0) return false;
        //otherwise, decrement the counter and move to the next index
        dp[cnt][i] = solve(cnt - 1, i + 1, s);
        return dp[cnt][i];
    }
    else
    { // we encountered '*'. Check 3 possibilites
        dp[cnt][i] = solve(cnt, i + 1, s) || solve(cnt + 1, i + 1, s) || solve(cnt - 1, i + 1, s);
        return dp[cnt][i];
    }
}

class Solution {
public:
    Solution()
    {
        std::ios_base::sync_with_stdio(false); std::cin.tie(NULL);
    }
    bool checkValidString(string s) {
        dp = vv<vv<int>>(100, vv<int>(100, -1));
        int cnt = 0;
        return solve(cnt, 0, s);
    }
};
```

**I hope you enjoyed your time. Come back for more!**


> This post is not complete and future refinements are possible.
{: .prompt-danger}