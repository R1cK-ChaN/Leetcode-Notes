# 代码随想录算法训练营第三十四天

## 动态规划第二天

### 考点总结

- **动态规划五步曲**
  1. 确定dp数组（dp table）以及下标的含义
  2. 确定递推公式
  3. dp数组如何初始化
  4. 确定遍历顺序
  5. 举例推导dp数组

---

#### 不同路径

[题目：Leetcode 62](https://leetcode.com/problems/unique-paths)

##### 简单思路

- 题目：一个m*n的二维数组，问从左上角走到右下角有多少种路径
- `dp[i][j]`含义：走到`[i][j]`有多少种不同路径
- 递推公式：只能往下和往右走，因此当前的路径数来自左边和上面的格子
  - `dp[i][j] = dp[i-1][j] + dp[i][j-1]`
- dp数组初始化：最左边和最上面的两条边需要初始化
  - 因为这两条边只能有一条路径，即最左边：向左；最上边：向右
  - 所以这两条边上面的路径都要被初始化为1
- 遍历顺序：就是两层for循环嵌套遍历

##### 代码呈现

```python
class Solution(object):
    def uniquePaths(self, m, n):
        dp = [[0]*n for _ in range(m)] # 构建dp二维数组（棋盘）
        for i in range(m): # 初始化最左边
            dp[i][0] = 1
        for j in range(n): # 初始化最上边
            dp[0][j] = 1
        for i in range(1, m): # 两层for循环嵌套遍历
            for j in range(1, n): # 遍历范围是[1, n-1]
                dp[i][j] = dp[i-1][j] + dp[i][j-1] # 当前的路径数来自左边和上面的格子
        return dp[m-1][n-1] # retturn 最右下方的格子
```

##### 易错点

- 用列表生成式new棋盘
- 最左边和最上面两条边需要初始化
  - 初始化为一，因为这两条边都是只有一条路径
- 遍历整个棋盘时，需要两层for循环嵌套，而且遍历范围是`for i in range(1, m)`，从1开始

---

#### 不同路径Ⅱ

[题目：Leetcode 63](https://leetcode.com/problems/unique-paths-ii)

#####  简单思路

- 现在棋盘上面有障碍物，路径不能包含障碍物
  - 如果有障碍的格子，dp直接为0
  - 起点或终点有障碍物，直接return 0
- 障碍物会影响初始化，如果最左边和最上边出现障碍物，障碍物后面的格子都是dp=0
  - 因为无法到达障碍物后面的格子
- 遍历棋盘的时候，如果需要障碍物，直接跳过，没有障碍物才递归

##### 代码呈现

```python
class Solution(object):
    def uniquePathsWithObstacles(self, obstacleGrid):
        m = len(obstacleGrid) # 棋盘的行数
        n = len(obstacleGrid[0]) # 棋盘的列数
        if obstacleGrid[0][0] == 1 or obstacleGrid[m-1][n-1] == 1:
            return 0 # 起点或终点有障碍物，直接return 0
        dp = [[0]*n for _ in range(m)] # 构建dp数组
        for i in range(m): # 最左边
            if obstacleGrid[i][0] == 0:
                dp[i][0] = 1
            else: # 遇到障碍物即停止，后面都是0
                break
        for j in range(n): # 最上边
            if obstacleGrid[0][j] == 0:
                dp[0][j] = 1
            else:
                break
        for i in range(1, m): 
            for j in range(1, n):
                if obstacleGrid[i][j] == 1:
                    continue # 遇到障碍物即跳过
                dp[i][j] = dp[i-1][j] + dp[i][j-1]
        return dp[m-1][n-1]
```

##### 易错点

- 需要注意初始化最左边和最上边遇障碍物的处理
  - 直接break，障碍物后面的格子全是0
- 遍历整个棋盘时，遇到障碍物即跳过

