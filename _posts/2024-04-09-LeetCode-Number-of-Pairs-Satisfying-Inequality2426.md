---
title: LeetCode 2426. Number of Pairs Satisfying Inequality
description: 
author: Gismet Majidov
date: 2024-04-09 17:25:00 +0800
categories: [Problem-Solving, LeetCode]
tags: [LeetCode, Ordered Set, Segment Tree, Merge Sort, Binary Search]
math: true
---

# 2426. Number of Pairs Satisfying Inequality

You are given two **0-indexed** integer arrays `nums1` and `nums2`, each of size `n`, and an integer `diff`. Find the number of pairs `(i, j)` such that:

- `0 <= i < j <= n - 1` and
- `nums1[i] - nums1[j] <= nums2[i] - nums2[j] + diff`.
  
Return the **number of pairs** that satisfy the conditions.

**Constraints:**

- n == nums1.length == nums2.length
- 2 <= n <= $10^5$
- $-10^4$ =<= nums1[i], nums2[i] <= $10^4$
- $-10^4$ <= diff <= $10^4$


**Example 1:**<br/>
**Input: nums1 = [3,2,5], nums2 = [2,2,1], diff = 1** <br/>
**Output: 3**<br/>
**Explanation:**<br/>
*There are 3 pairs that satisfy the conditions:*

1. i = 0, j = 1: 3 - 2 <= 2 - 2 + 1. Since i < j and 1 <= 1, this pair satisfies the conditions.
1. i = 0, j = 2: 3 - 5 <= 2 - 1 + 1. Since i < j and -2 <= 2, this pair satisfies the conditions.
2. i = 1, j = 2: 2 - 5 <= 2 - 1 + 1. Since i < j and -3 <= 2, this pair satisfies the conditions. Therefore, we return 3.

**Example 2:**

**Input: nums1 = [3,-1], nums2 = [-2,2], diff = -1**<br/>
**Output: 0**<br/>
**Explanation:**<br/>
*Since there does not exist any pair that satisfies the conditions, we return 0.*


## Brute force solution

One possible solution is to try all `(i, j)` pairs and find those that satisfy our conditions.

```java
class Solution {
    public long numberOfPairs(int[] nums1, int[] nums2, int diff) {
        int n = nums1.length, ans = 0;
        for(int i = 0; i < n; i++)
        {
            for(int j = i + 1; j < n; j++)
            {
                if(nums1[i] - nums1[j] <= nums2[i] - nums2[j] + diff)
                    ans++;
            }
        }
        return ans;
    }
}
```

Let's answer the following questions now.

1. *Does this algorithm correctly solves the problem?*
    * Yes, it solves correctly the problem for the given inputs
2. *Does this algorithm give correct result for all inputs?*
    * Yes, it does. As we check all the possible `(i, j)` pairs, it is guaranteed that we find the correct result.
3. *Is the algorithm efficient?*
   * Although the algorithm uses no auxiliary space, the time complexity is $O(n^2)$ which, given the constraints, is not efficient. 


We concluded that the algorithm is slow although correct. So we have to pursue other avenues. 

Effectively, we have to look for something faster than $O(n^2)$, maybe $O(n log(n))$ or $O(n)$


## A different perspective

Let's try to deduce something from the problem description that would help us solve problem more efficiently.

Particularly let's analyse the following expression from the problem description. 

- `nums1[i] - nums1[j] <= nums2[i] - nums2[j] + diff`.

This expression says a lot. It might seem a bit confusing but can be rearranged to transform the current problem into a different problem. 

Let's , for the sake of argument, call `nums1` `a` and `nums2` `b`. 
And also `nums1[i]` will be denoted by $a_i$ and `nums2[i]` by $b_i$

Now our original expression looks like the following:

- $a_i - a_j <= b_i - b_j + diff$
  

Let's rearrange some terms.

$a_i - a_j - b_i + b_j <= diff$

$(a_i - b_i) - (a_j - b_j) <= diff$

Now, we can see that both $(a_i - b_i)$ and $(a_j - b_j)$ represent the difference between elements of `nums1` and `nums2`. So we can calculate the difference `nums1[i] - nums2[i]` for all indices `i` such taht `0 <= i < n`.

Let's now call $(a_i - b_i)$ and $(a_j - b_j)$ `diff1` and `diff2` respectivly. 

- `diff1 - diff2 <= diff`

Now given `diff1` and `diff`, we need to find `diff2 >= diff1 - diff`. 

Let's know demonstrate this on an example.

**`nums1 = [3,2,5], nums2 = [2,2,1], diff = 1`** <br/>

Let's first calculate the difference `nums1[i] - nums2[i]` for all indices `i` such taht `0 <= i < n` and store them in an array called `diffs`

`diffs = [1, 0, 4]`

Here `diffs[0]` is `nums1[0] - nums2[0]1` and `diffs[1]` is `nums1[1] - nums2[1]` and so on. 


Let's fix `diff1` and find all `diff2` such that `diff2 >= diff1 - diff`.

`diff1 = diffs[0] = 1` `diff2 >= 1 - 1`. So we have to find all `diff2` such that `diff2 >= 0`. We will look for `diff2` to the right of `diff1`. There are two such values, namely `diffs[1] = 0` and `diffs[2] = 4`. 

Now let's fix `diff1` again. `diff1 = 0`. `diff2 >= 0 - 1`. `diff2 >= -1`. There is one such value, namely `diffs[2] = 4`.  

Now let's fix `diff1` again. `diff1 = 4`. `diff2 >= 4 - 1`. `diff2 >= 3`. 
There is no value to the right of `diffs[2]`. 

So in total, we have `3` such pairs. 

**But how do we find `diff2`?**

For example, how do we find all values of `diff2` that is greater than or equal to `0`. 

Well, if the range in which we search for `diff2` was sorted, we could use `binary search`. But the range is not guaranteed to be sorted. For example the range in which we search `diff2 >= 0` is sorted `diffs = [1, 0, 4] (from index 1 to the end)`. But that might not always be the case. 

Given an array of numbers and an integer `x`, do we find the number of elements that are greater than or equal to `x` ?  Is there a data structure? 

## C++ Policy based data structure

There is a policy based data structure in C++ STL that allows us to find the `kth` largest element (0-indexed) and the number of elements that are strictly smaller than a given value. It is called [**ordered_set**](https://codeforces.com/blog/entry/11080), which is kind of an extension to `std::set` with the aforementioned operations. 


How can we use this data structure to solve our problem? 

It provides us a way to find the number of elements that are strictly greater than a given value. For example, if the ordered set contains the followig values: 

- **nums = [2, 3, 1, -4, 5, 3, 8, 9]**

And given `x = 4`, we can find the number of elements strictly smaller than `4`. There are `5` such values. What about the other elements ? other elements are ***greater than or equal to*** `4`, which is what we need. We need to find the number of elements greater than or equal to a given value. Now we can easily do this by finding the number of elemnts that are strictly smaller than the current element and subtract that from the total number of elements to get the number of elements greater than or equal to the current element. 

That's essentially it. The key to solve this problem is to do the necessary transformation we did. 

>NOTE: This problem can also be solved with the previous method. Search for `diff2` with binary search. As we noted, in order to use binary search, the range has to be sorted. There are a few ways to sort the range we search in. We could use `Merge Sort Tree`, which is the recursive tree generated by the 'Merge Sort` algorithm. 
{: .prompt-info}

Let's have a look at the code.

## Code
```cpp
//necessary includes for ordered_set
#include <ext/pb_ds/assoc_container.hpp>
#include <ext/pb_ds/tree_policy.hpp>

using namespace __gnu_pbds;


typedef tree<int, null_type, std::less_equal<int>, rb_tree_tag, tree_order_statistics_node_update> ordered_mset; // lower_bound(x - 1)
#define ll long long
#define vv std::vector

class Solution {
public:
 

    long long numberOfPairs(vector<int>& nums1, vector<int>& nums2, int diff) {
        ordered_mset obj;

        ll ans = 0;
        int n = int(nums1.size());
        for (int i = n - 1; i >= 0; i--)
        {
            int a = nums1[i] - nums2[i];
            int x = a - diff;
            ans = ans + (int(obj.size()) - obj.order_of_key(x));
            obj.insert(a);
        }

        return ans;
    }
};
```

**I hope you had a great time reading this article. Come back for more!**