---
layout: post
title: 417. Pacific Atlantic Water Flow
subtitle: 
author: Bin Li
tags: [Coding, LeetCode, DFS, DP, Graph]
image: 
comments: true
published: true
---

Given an m x n matrix of non-negative integers representing the height of each unit cell in a continent, the "Pacific ocean" touches the left and top edges of the matrix and the "Atlantic ocean" touches the right and bottom edges.

Water can only flow in four directions (up, down, left, or right) from a cell to another one with height equal or lower.

Find the list of grid coordinates where water can flow to both the Pacific and Atlantic ocean.

Note:

1. The order of returned grid coordinates does not matter.
2. Both m and n are less than 150.
 
Example:
```
Given the following 5x5 matrix:

  Pacific ~   ~   ~   ~   ~ 
       ~  1   2   2   3  (5) *
       ~  3   2   3  (4) (4) *
       ~  2   4  (5)  3   1  *
       ~ (6) (7)  1   4   5  *
       ~ (5)  1   1   2   4  *
          *   *   *   *   * Atlantic

Return:

[[0, 4], [1, 3], [1, 4], [2, 2], [3, 0], [3, 1], [4, 0]] (positions with parentheses in above matrix).
```

## Solutions
![-w1195](/img/media/15663923164674.jpg)

### DFS - 按照题目思路

### DFS - 按照反向思维

### DP
还使用了滚动数组。

### DFS - LeetCode 参考
　　思路比较清晰：
1. 从所有点开始做 DFS 遍历看是否它的邻居都是小于等于自己
2. 维护两个布尔类型的矩阵，分别表示太平洋或大西洋是否能够抵达对应的点
3. 最后遍历两个矩阵的每一个点，看是否同时能够到达太平洋和大西洋
4. 值得注意的是，如果一个点能够抵达，那么就不需要重复访问

```python
class Solution(object):
    def pacificAtlantic(self, matrix):
        """
        :type matrix: List[List[int]]
        :rtype: List[List[int]]
        """
        if not matrix:
            return []
        self.directions = [(1, 0), (-1, 0), (0, 1), (0, -1)]
        m, n = len(matrix), len(matrix[0])
        p_visited = [[False for _ in range(n)] for _ in range(m)]
        a_visited = [[False for _ in range(n)] for _ in range(m)]
        result = []
        
        for i in range(m):
            # p_visited[i][0] = True
            # a_visited[i][n-1] = True
            self.dfs(matrix, i, 0, p_visited, m, n)
            self.dfs(matrix, i, n-1, a_visited, m, n)
        
        for i in range(n):
            # p_visited[0][i] = True
            # a_visited[m-1][i] = True
            self.dfs(matrix, 0, i, p_visited, m, n)
            self.dfs(matrix, m-1, i, a_visited, m, n)
        
        for i in range(m):
            for j in range(n):
                if p_visited[i][j] and a_visited[i][j]:
                    result.append([i, j])
        return result
    
    def dfs(self, matrix, i, j, visited, m, n):
        # when dfs is called, meaning its caller already verified this point
        visited[i][j] = True
        for dire in self.directions:
            x, y = i + dire[0], j + dire[1]
            if x < 0 or x >= m or y < 0 or y >= n or \
                visited[x][y] or matrix[x][y] < matrix[i][j]:
                continue
            self.dfs(matrix, x, y, visited, m, n)
# Runtime: 280 ms, faster than 53.30% of Python online submissions for Pacific Atlantic Water Flow.
# Memory Usage: 13.5 MB, less than 50.00% of Python online submissions for Pacific Atlantic Water Flow.
```
## References
1. [417. Pacific Atlantic Water Flow](https://leetcode.com/problems/pacific-atlantic-water-flow/)
2. [花花酱 LeetCode 417](https://www.youtube.com/watch?v=zV3o4XVoU8M&list=PLLuMmzMTgVK423Mj1n_OaOAZZ6k5fNxyN)
3. [Python DFS bests 85%. Tips for all DFS in matrix question.](https://leetcode.com/problems/pacific-atlantic-water-flow/discuss/90739/Python-DFS-bests-85.-Tips-for-all-DFS-in-matrix-question.)
4. 相关题目
    1. [329. Longest Increasing Path in a Matrix](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/)