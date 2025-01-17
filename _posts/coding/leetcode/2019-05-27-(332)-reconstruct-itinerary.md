---
layout: post
title: 332. Reconstruct Itinerary
subtitle:
author: Bin Li
tags: [Coding, LeetCode, DFS]
image: 
comments: true
published: true
---

Given a list of airline tickets represented by pairs of departure and arrival airports [from, to], reconstruct the itinerary in order. All of the tickets belong to a man who departs from JFK. Thus, the itinerary must begin with JFK.

Note:

1. If there are multiple valid itineraries, you should return the itinerary that has the smallest lexical order when read as a single string. For example, the itinerary ["JFK", "LGA"] has a smaller lexical order than ["JFK", "LGB"].
2. All airports are represented by three capital letters (IATA code).
3. You may assume all tickets form at least one valid itinerary.

Example 1:
```
Input: [["MUC", "LHR"], ["JFK", "MUC"], ["SFO", "SJC"], ["LHR", "SFO"]]
Output: ["JFK", "MUC", "LHR", "SFO", "SJC"]
```
Example 2:
```
Input: [["JFK","SFO"],["JFK","ATL"],["SFO","ATL"],["ATL","JFK"],["ATL","SFO"]]
Output: ["JFK","ATL","JFK","SFO","ATL","SFO"]
Explanation: Another possible reconstruction is ["JFK","SFO","ATL","JFK","ATL","SFO"].
             But it is larger in lexical order.
```
           
## Solutions
　　要理解题意，这是飞机起飞降落的飞停机场编号的串联。这里是欧拉回路问题。

　　另一种计算欧拉路的算法是 Hierholzer 算法。这种算法是基于这样的观察：

<p align="center">
  <img width="" height="" src="/img/media/15589364080900.jpg">
</p>

在手动寻找欧拉路的时候，我们从点 4 开始，一笔划到达了点 5，形成路径 4-5-2-3-6-5。此时我们把这条路径去掉，则剩下三条边，2-4-1-2 可以一笔画出。

这两条路径在点 2 有交接处（其实点 4 也是一样的）。那么我们可以在一笔画出红色轨迹到达点 2 的时候，一笔画出黄色轨迹，再回到点 2，把剩下的红色轨迹画完。

由于明显的出栈入栈过程，这个算法可以用 DFS 来描述。

```python
DFS(u):
	While (u存在未被删除的边e(u,v))
		删除边e(u,v)
		DFS(v)
	End
	PathSize ← PathSize + 1
	Path[ PathSize ] ← u
```

　　参考了下 discussion 的解法：
```python
class Solution(object):
    def findItinerary(self, tickets):
        """
        :type tickets: List[List[str]]
        :rtype: List[str]
        """
        targets = collections.defaultdict(list)
        for a, b in sorted(tickets)[::-1]:
            targets[a] += b,
        route = []
        def visit(airport):
            while targets[airport]:
                visit(targets[airport].pop())
            route.append(airport)
        visit('JFK')
        return route[::-1]
# Runtime: 52 ms, faster than 89.13% of Python online submissions for Reconstruct Itinerary.
# Memory Usage: 12.3 MB, less than 46.29% of Python online submissions for Reconstruct Itinerary.
```

　　迭代方式：
```python
class Solution(object):
    def findItinerary(self, tickets):
        """
        :type tickets: List[List[str]]
        :rtype: List[str]
        """
        targets = collections.defaultdict(list)
        for a, b in sorted(tickets)[::-1]:
            targets[a] += b,
        route, stack = [], ['JFK']
        while stack:
            while targets[stack[-1]]:
                stack += targets[stack[-1]].pop(),
            route += stack.pop(),
        return route[::-1]

# Runtime: 60 ms, faster than 80.71% of Python online submissions for Reconstruct Itinerary.
# Memory Usage: 12.1 MB, less than 73.48% of Python online submissions for Reconstruct Itinerary.
```
## References
1. [332. Reconstruct Itinerary](https://leetcode.com/problems/reconstruct-itinerary/)
2. [欧拉回路](https://ikely.me/2015/06/28/%E6%AC%A7%E6%8B%89%E8%B7%AF/)