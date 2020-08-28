开学课程压力是真滴大，三周了，一道题都没刷，今天实在不想看网课，来一道之前没思路的题。

##### 一、思路

看到这道题，会想到连通块问题。处理连通块问题，就是用dfs或bfs，以当前方块为起点，扩展出所有的连通方块。这道题也是这样的方法，不同的是这道题涉及到将符合条件的O填充为X，类似涂色。一种方法是**找出需要涂色的连通块**；另一种方法是**找出不满足条件即不需涂色的方块所在的连通块**。明显可知，前者需要遍历所有方块，找到各自的连通块（实际不需要，当方块已经被访问过或者是‘X’，就可以直接往后走）；后者只需要遍历四周的方块即可。

##### 二、代码

这里给出思路中第二种较优方法的代码

```
class Solution {
public:
    void dfs(vector<vector<char>>& board , int x , int y)
    {
        int dictions[4][2] = {{0,1},{0,-1},{1,0},{-1,0}};
        board[x][y] = 'Y';
        for (int i = 0 ; i < 4 ; ++i)
        {
            int newX = x+dictions[i][0];
            int newY = y+dictions[i][1];
            if (0 <= newX && newX < board.size() && 0 <= newY && newY < board[0].size() 
            && board[newX][newY] == 'O')
                dfs(board , newX , newY);
        }
    }

    void solve(vector<vector<char>>& board) {
        for (int i = 0 ; i < board.size() ; ++i)
        {
            for (int j = 0 ; j < board[0].size() ; ++j)
            {
                if ((i == 0 || i == board.size()-1 || j == 0 || j == board[0].size()-1) &&
                board[i][j] == 'O')
                    dfs(board , i , j);
            }
        }

        for (int i = 0 ; i < board.size() ; ++i)
        {
            for (int j = 0 ; j < board[0].size() ; ++j)
            {
                if (board[i][j] == 'O')
                    board[i][j] = 'X';
                if (board[i][j] == 'Y')
                    board[i][j] = 'O';
            }
        }
    }
};
```

时间复杂度：**O(n)**（无论如何遍历，每一个方块基本上只会被访问一次）