---
title: LeetCode 2611. Mice and Cheese
description: 
author: Gismet Majidov
date: 2024-04-09 12:50:00 +0800
categories: [Problem-Solving, LeetCode]
tags: [LeetCode, Greedy, Sorting]
math: true
---

# 2611. Mice and Cheese

There are two mice and `n` different types of cheese, each type of cheese should be eaten by exactly one mouse.

A point of the cheese with index `i` (0-indexed) is:

- `reward1[i]` if the first mouse eats it.
- `reward2[i]` if the second mouse eats it.
  
You are given a positive integer array `reward1`, a positive integer array `reward2`, and a non-negative integer `k`.

Return **the maximum** points the mice can achieve if the first mouse eats **exactly** `k` types of cheese.

**Example 1:**

**Input: reward1 = [1,1,3,4], reward2 = [4,4,1,1], k = 2** <br/>
**Output: 15**

**Explanation:** *In this example, the first mouse eats the 2nd (0-indexed) and the 3rd types of cheese, and the second mouse eats the 0th and the 1st types of cheese.
The total points are 4 + 4 + 3 + 4 = 15.
It can be proven that 15 is the maximum total points that the mice can achieve.*

**Example 2:**

**Input: reward1 = [1,1], reward2 = [1,1], k = 2**<br/>
**Output: 2**

**Explanation:** *In this example, the first mouse eats the 0th (0-indexed) and 1st types of cheese, and the second mouse does not eat any cheese.
The total points are 1 + 1 = 2.
It can be proven that 2 is the maximum total points that the mice can achieve.*

## First idea

We are asked to maximize points mice can achieve by eating all the cheese.<br/>
But there is a catch! The first mouse must eat **exactly** `k` types of cheese. The second mouse can eat any amount of cheese. Thus second mouse will eat the rest of the cheese after the first mouse eats `k` types of cheese. 

***Which types of cheese should the first mouse eat?***  

Since we want to maximize the points, let's allow the first mouse to eat `k` types of cheese with the most points. Let's have a look at the first example again. 

`reward1 = [1,1,3,4], reward2 = [4,4,1,1], k = 2`

For this example, the first mouse eats two types of cheese with points `3` and `4` (in `reward1`), achieving a total of `7` points. Now that the first mouse ate exactly `k` types of cheese, the second mouse can eat up the rest of the cheese, getting a totle of `8` points by eating the first and the second type of cheese. In total, the mice obtain `15` points, which is the maximum that can be achieved. 

***Is this appraoch correct? Does it always yield the maximim points?***

Basically, the first mouse ate the `k` types of cheese with the most points.

Let's see another example. 

`reward1 = [1, 1, 3, 4], reward2 = [4, 4, 50, 50] k = 2`

Now let's try our approach on this example. We let the first mouse eat `2` types of cheese with the most points. The first mouse gets `3` + `4` = `7` points by eating the third and fourth types of cheese (in `reward1`). The second mouse eats the first and the second types of cheese, getting a total of `8` points. Overall, we got `15` again whereas the maximium is `102` by letting the first mouse eat the first and the second types of cheese and the second mouse eat the third and fourth types of cheese. So our idea of allowing the first mouse to greedily eat the types of cheese with the most points falls short.

## Second attempt

Let's change our previous approach a little bit.We will let the first mouse eat `k` types of cheese with the most points only if the corresponding points of the second mouse is less than the points of the first mouse. Let's examine the previous example for which our first idea failed. 

`reward1 = [1, 1, 3, 4], reward2 = [4, 4, 50, 50] k = 2`

The fourth cheese is the most valuable cheese for the first mouse but it gives less points if the first mouse eats it. Therefore we let the second mouse eat it as it gives us more points. The next valuable cheese for the first mouse is the third cheese, which gives `3` points. Again it is less than the point the second mouse achieves. Once again we let the second mouse eat it. Now we are only left with `2` types of cheese and the first mouse have not yet eaten any cheese. We can't let the second mouse eat any more cheese because then we would not have enough cheese for the first mouse. So the last `2` types of cheese got eaten by the first mouse. We get a total of `102` points, which is actually the maximim value. 

***Is this approach correct for all inputs?***

Well, let's see another example. 

`reward1 = [1, 1, 2, 3], reward2 = [60, 60, 50, 50] k = 2`

We allow the second mouse to eat the third and fourth cheese because they would give us less points if the first mouse ate them. So the second mouse ate them. Now the first mouse is forced to eat the first and second types of cheese. This way, we get `102` points while the optimal answer is `125`. The first mouse should eat the third and fourth cheese and the second mouse eats the first and the second cheese.

As promising and greedy as this approach sounds, it also fails to give us the optimal answer. 

**What is the remedy?**

## Optimal strategy

Let's say we have two types of cheese. 

` reward1 = [1, 2], reward2 = [60, 50] k = 1`

In this example, the first mouse eats `1` cheese. Which cheese should the first mouse eat?

If we adhere to our previous approach, the first mosue should eat the first type of cheese, which gives `1` point. And then the second mouse eats the second cheese with a point of `50`. In total, we get `51`, which is not optimal. The maximim we can achieve is `62`. The first mouse eats the second and the second mouse eats the first cheese. What is the strategy then?

Let's look at the point differences between the points of the second mouse and first mouse.

`60 - 1 = 59` - the first cheese<br/>
`50 - 2 = 48` - the second cheese

The first mouse eats the second type of cheese which has less difference than the first type of cheese. This difference tells us that the first mouse should eat `k` types of cheese where the differnece between the poings of the second mouse and the first mouse is small. So the first mouse eats types of cheese with the smallest `k` differences. The second mouse eats the rest of the cheese. This gives us an optimal strategy.

Now let's see the code.

```cpp
#define vv std::vector
#define pp std::pair
class Solution {
public:
    int miceAndCheese(vector<int>& reward1, vector<int>& reward2, int k) {
        int n = int(reward1.size());
        vv<pp<int, pp<int, int>>> diffs(n);
        for(int i = 0; i < n; i++)
        {
            //{difference, reward1[i], reward2[i]}
            diffs[i] = {reward2[i] - reward1[i], {reward1[i], reward2[i]}};
        }

        //sort based on the difference
        std::sort(diffs.begin(), diffs.end());

        int ans = 0;
        //the first mouse eats the first k cheese (according to smallest difference)
        for(int i = 0; i < k; i++)
        {
            ans += diffs[i].second.first;
        }

        //the second mouse eats the remaing cheese
        for(int i = k; i < n; i++)
        {
            ans += diffs[i].second.second;
        }

        return ans;
    }
};
```

**I hope you enjoyed your time. Come back for more!**