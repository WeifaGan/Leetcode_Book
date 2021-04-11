# 第1章 动态规划

**动态规划题目特点**

* 计数
  * 有多少种方法走到右下角
  * 有多少种方法选出k个数使得和为sum
* 求最值
* 求存在性
  * 取石子游戏，先手是否必胜
  * 能不能选出k个数使得和为sum

**举例**：你有三种硬币，分别面值为2,5,7，每枚硬币都足够多，买一个书需要27元，如何用最少的硬币组合正好付清，不需要对方找钱。

1.确定状态，这个步骤要有两个意识：最后一步和子问题。

最后一步：虽然不知道最优策略，但是最优策略肯定是k枚硬币a1,a2,...ak，和为27，所以肯定有最后一枚硬币ak，除了这枚硬币，前面硬币和为27-ak。

关键点1，我们并不关心前面k-1枚硬币是怎么拼出来27-ak。关键点2，因为是最优策略，所以拼出27-ak硬币数一定最少，否则就不是最优策略了。 

子问题：我们要求出k，那么肯定是先求出27-ak最少可以用多少枚硬币的，本来是求27最少可以用多少枚硬币，现在变成求27-ak，原问题转换为子问题，而且规模更小了。所以我们设f(x)为最少可以用多少枚硬币拼出x。如果ak=2，f(27)=f(27-2)+1(要加上最后一枚硬币2),如果ak=5，f(27)=f(27-5)+1,如果ak=7，f(27)=f(27-7)+1。所以f(27)=min{f(27-2)+1,f(27-5)+1,f(27-7)+1}

> 相比于递归，动态规划没有重复求子问题，而是把子问题的值存起来解决大问题

2.转移方程：f(x)=min{f(x-2)+1,f(x-5)+1,f(x-7)+1}

3.初始条件和边界问题

初始条件是转移方程算出来的，需要手工定义。

4.计算顺序，大部分从小到大计算

```c++
public int coinChange(int[] coins, int amount) {
    
    int coins_n = coins.length;
    //用到amount，申请+1个长度
    vector<int>dp(amount+1,Integer.MAX_VALUE);
    for(int i=1;i<=amount;i++){
        for (int j=0;i<n;j++)
        {
            if(i-coins[j]>0 && dp[i-coins[j]]!=Integer.MAX_VALUE){
                dp[i]=min(dp[i-coins[j]]+1,dp[i]);
            }

        }
    }
    if (dp[amount]==Integer.MAX_VALUE){return -1;}
    return dp[amount];

}
}
```

## 1.1坐标型&位操作

* 给定一个序列或者网格
* 需要找到序列中某些子序列或者网格的某条路径
  * 某种性质最小\最大
  * 计数
  * 存在性
* 动态规划中dp[i]的以第i个元素为结尾的满足条件的子序列性质，dp[i,j]表示前(i,j)格子为结尾的满足条件的路径的性质。
  * 最小\大值
  * 个数
  * 存在性
* 有一个比较纠结的点，dp[i]前i个元素为结尾的满足条件的子序列性质，因为是前i个，没有说前0个，只说前1个，所以dp[1]记录的是前1个元素结尾的状态，但是前1个元素的结尾元素的坐标序号其实是i-1的，这一点要明确。

### 62.不同的路径

**题目**：mxn大小的矩阵，从左上角走到右下角有多少种路径

**题目分析**：最后一步：走到(m-1,n-1)，要不就是(m-2,n-1)，要不就是(m-1,n-2)，所以状态方程dp[i,j]=dp[i-1,j]+dp[i,j-1],dp[i-1,j]和dp[i,j-1]就是两个子问题。

```c++
class Solution {
public:
    int uniquePaths(int m, int n) {
        vector<vector<int>>dp(m,vector<int>(n,0));
        for(int i=0;i<n;i++){
            dp[0][i]=1;
        }
        for(int i=0;i<m;i++){
            dp[i][0]=1;
        }
        for(int i=1;i<m;++i){
            for(int j=1;j<n;++j){
                dp[i][j]=dp[i-1][j]+dp[i][j-1];
            }
        }
        return dp[m-1][n-1];
    }
};
```

空间压缩：

```c++
class Solution {
public:
    int uniquePaths(int m, int n) {
        vector<vector<int>>dp(m,vector<int>(n,0));
        for(int i=0;i<n;i++){
            dp[0][i]=1;
        }
        for(int i=0;i<m;i++){
            dp[i][0]=1;
        }
        for(int i=1;i<m;++i){
            for(int j=1;j<n;++j){
                dp[i][j]=dp[i-1][j]+dp[i][j-1];
            }
        }
        return dp[m-1][n-1];
    }
};
```



### 63.不同的路径2

**题目**：mxn大小的矩阵，中间可能会遇到障碍物，1,0分别表示障碍物有无，从左上角走到右下角有多少种路径

**分析**：状态方程跟上面的一样，但是因为有障碍物，所以第一行初始化时候，障碍物及其后面的dp都是0了，第一列同理。计算其他dp时候，如果该格子有障碍物，就不用算了。

```c++
class Solution {
public:
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        int m = obstacleGrid.size();
        int n = obstacleGrid[0].size();
        vector<vector<int>>dp(m,vector<int>(n,0));
        for (int i=0;i<n;++i){
            if (obstacleGrid[0][i]!=1){
                dp[0][i]=1;
            }
            else{break;}
        }
        for(int i=0;i<m;++i){
            if (obstacleGrid[i][0]!=1){
                dp[i][0]=1;
            }
            else{break;}
        }

        for(int i=1;i<m;i++){
            for(int j=1;j<n;j++){
                if (obstacleGrid[i][j]!=1)
                {dp[i][j]=dp[i-1][j]+dp[i][j-1];}
            }
        }

        return dp[m-1][n-1];


    }
};
```



### 256 油漆房子

**题目**：现在我们需要给房屋染色，分别有红色蓝色和绿色。每个房屋染不同的颜色费用也不同，你需要设计一种染色方案使得**相邻的房屋颜色不同**，并且费用最小，返回最小的费用。

**分析**：

最后一步：要油漆最后一个房子的时候，我们不仅仅要知道前一个房子的最小花费，而且还要知道前一个房子油漆的是什么颜色，这样子才好判断最后一个房子是油漆什么颜色。但是我们怎么知道前面的房子油漆什么颜色呢？**不知道的状态什么就用记录下来！** 设dp[i,0]，dp[i,1]，dp[i,2]分别是前i个房子并且第i个房子(坐标i-1)是红色蓝色和绿色的最小花费(i从1开始i=1,2,3....,但是对应的房子的序号从0开始)。

子问题：要求dp[i,0]，那么就要求出第i-1个房子油漆红色的最小花费。 

状态转移方程：dp[i,0]=min(costs[i-1,1],cost[i-1,2])+dp[i,0]，因为第i-1个房子油漆红色，所以第i个房子只能油漆蓝色或者绿色，求最小然后把第i-1个房子的费用相加。

代码：

```c++
public class Solution {
    /**
     * @param costs: n x 3 cost matrix
     * @return: An integer, the minimum cost to paint all houses
     */
    public int minCost(vector<vector<int>>& costs) {
   
        int m = costs.length;
        if (m==0) return 0;
        int n = costs[0].length;
        vector<vector<int>>dp(m+1,vector<int>(n,0));
   
        for (int i=1;i<=m;i++){
            dp[i][0] = min(costs[i-1][1],costs[i-1][2])+dp[i-1][0];
            dp[i][1] = min(costs[i-1][0],costs[i-1][2])+dp[i-1][1];
            dp[i][2] = min(costs[i-1][1],costs[i-1][0])+dp[i-1][2];
            
        }
        int c = *max_element(dp[m].begin(),dp[m].end()); 
        return c;
    }
}
```

空间压缩：画出状态转移矩阵后，发现如果dp可以用cost来替代，但是i=1时候，dp[i-1]怎么办呢？dp[0]应该是要初始化为0的，不可能用cost[0]来替代dp[0]啊。

### 64.最小路径和

**题目**：包含非负数的mxn网格grid，只能向下和向右走，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

**题目分析**：

最后一步，要求最后一步的路径总和最小，要么从左边最优策略中走过来，要么从上面最优策略中走下来，那么dp[i,j]=min(dp[i-1,j],dp[i,j])+grid[i,j]，那么问题就变成了求dp[i-1,j]和dp[i,j-1]两个子问题。

代码

```c++
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        int m =grid.size(),n = grid[0].size();
        if (m==0) return 0;
        vector<vector<int>>dp(m+1,vector<int>(n+1,0));
      
        for(int i=1;i<=m;i++){
            for(int j=1;j<=n;j++){
                if (i==1){dp[i][j]=dp[i][j-1]+grid[i-1][j-1];}
                else if (j==1){dp[i][j]=dp[i-1][j]+grid[i-1][j-1];}
                else{
                    dp[i][j]=min(dp[i-1][j],dp[i][j-1])+grid[i-1][j-1];
                }
                
            }
        }
        return dp[m][n];
    }
};
```



计算第i行，只跟第i行和第i-1这两行，因此可以空间压缩：

```c++
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        int m =grid.size(),n = grid[0].size();
        if (m==0) return 0;
        vector<int>dp(n+1,0);
      
        for(int i=1;i<=m;i++){
            for(int j=1;j<=n;j++){
                if (i==1){dp[j]=dp[j-1]+grid[i-1][j-1];}
                else if (j==1){dp[j]=dp[j]+grid[i-1][j-1];}
                else{
                    dp[j]=min(dp[j-1],dp[j])+grid[i-1][j-1];
                }
                
            }
        }
        return dp[n];
    }
};
```

如何最小路径和的路径？

思路：记录dp[i]的是从左边走过来的还是从右边走过来的，然后从最后一个元素开始倒着找路径。

```c++
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        int m =grid.size(),n = grid[0].size();
        if (m==0) return 0;
        vector<int>dp(n+1,0);
        //从左边过来记录0，上面下来记录1
      	vector<vector<int>>pi(m,vector<int>(n+1,0));
        for(int i=1;i<=m;i++){
            for(int j=1;j<=n;j++){
                if (i==1){dp[j]=dp[j-1]+grid[i-1][j-1];}
                else if (j==1){
                    dp[j]=dp[j]+grid[i-1][j-1];
                    pi[i-1][j-1]=1;
                }
                else{
                    int tep = dp[j];
                    dp[j]=min(dp[j-1],dp[j])+grid[i-1][j-1];
                    if (dp[j]-grid[i-1][j-1]==tep){pi[i-1][j-1]=1;}
                }
                
            }
        }
        
        vector<int>path(m+n-1);
  
        int x=m-1,y=n-1;
        path[0]=grid[x][y];
      	for(int i=1;i<m+n-1;i++){
         if(pi[x][y]==1){
            x--;
            path[i]  =grid[x][y];
             
        }
        	else{y--;path[i]=grid[x][y];}
        }
      	
        for(int i=m+n-2;i>=0;i--)
        {
            cout<<path[i];
        }

        return dp[n];
    }
};
```





### 338.比特位计数

**题目**：给定一个非负整数 **num**。对于 **0 ≤ i ≤ num** 范围中的每个数字 **i** ，计算其二进制数中的 1 的数目并将它们作为数组返回。

**题目分析**：

二进制表示算法：

```
repeat:
第一步:i%2是bit位
第二步：i=floor(i/2)
若i==0返回
```

以100101为例，最后一步，观察去除最低位剩下的二进制10010有多少个1，然后加上最低位1的个数，那么就变成求子问题10010有多少个1。那么怎样能从100101得到10010呢，其实将100101进行右移操作。我们设dp[i]为i的二进制中1的个数。

代码：

```
class Solution {
public:
    vector<int> countBits(int num) {
        vector<int>dp(num+1,0);
        for(int i=0;i<=num;i++){
            dp[i]=dp[i>>1]+i%2;
        }
        return dp;
    }
};
```

i%2计算比较慢，可用i&1代替，因为%2其实就想要最后一位，最后一位是0,1，&出来分别就是0,1,

```c++
class Solution {
public:
    vector<int> countBits(int num) {
        vector<int>dp(num+1,0);
        for(int i=0;i<=num;i++){
            dp[i]=dp[i>>1]+(i&1);
        }
        return dp;
    }
};
```



### 300.最长递增子序列

**题目**：给定一个未排序的整数数组，求最长的递增子序列。

> Note:按照leetcode的习惯，子序列不必连续，子数组或者子字符串必须连续

**题目分析**：求最长，明显动态规划。序列不断递增，最后序列结尾那个数最大，那么我们将问题划分为求出第1个数,...,第i个数字作为结尾的最长递增子序列(子问题)，然后求出最大值。其实就是你不知道哪一个数字结尾的递增序列是最长的，所以会求出所有递增序列，然后求最大，但是求第i个数字结尾的递增子序列时，设起长度为dp[i]，最后一步，假设已经知道第i-1个数字结尾的递增子序列dp[i-1]，若nums[i]>nums[i-1]，则dp[i]=dp[i-1]+1,变相求dp[i-1]子问题了。求dp[i-1]同理。nums[i]>nums[j]，所以dp[i]=max(dp[i],dp[j]+1)，其实就是求出所有比第i个元素小的元素结尾的递增子序列中最大的值，所以要枚举。

**代码**

```
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int max_length=0,n=nums.size();
        if (n<=1) return 1;
        vector<int>dp(n,1);
        for (int i=1;i<n;i++){
            for(int j=0;j<i;j++){
                if(nums[i]>nums[j]){
                          dp[i]=max(dp[i],dp[j]+1);
                }
        }
         max_length = max(max_length,dp[i]);
        }
       return max_length;
    }
  
};
```

采用二分法进行快速搜索

```
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int max_length=0,max_num=0,n=nums.size();
        if (n<=1) return 1;
        vector<int>dp(n,1);
        for (int i=1;i<n;i++){
              if(dp[i]<max_num) continue;
              max_num=dp[i];
            for(int j=0;j<i;j++){
                if(nums[i]>nums[j]){  
                          dp[i]=max(dp[i],dp[j]+1);    
            }
        }
         max_length = max(max_length,dp[i]);
        }
       return max_length;
    }
};
```

打印最长子序列：

```c++
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int max_length=0,n=nums.size();
        if (n<=1) return 1;
        vector<int>dp(n,1);
        vector<int>pi(n,0);
        int p = -1;
        for (int i=1;i<n;i++){

            for(int j=0;j<i;j++){
                if(nums[i]>nums[j]){                       
                    dp[i]=max(dp[i],dp[j]+1);
                    if (dp[i]==dp[j]+1)
                    {pi[i]=j;} //增长序列的前一个元素来自哪里(跟最小路径和的套路是一样的)               
                }
        }
         max_length = max(max_length,dp[i]);  
         if (max_length==dp[i]){p=i;}
        }    

        vector<int>seq(max_length,0);
        for(int i=max_length-1;i>=0;i--){
            seq[i]=nums[p];
            p = pi[p];
     
        }
        for(int i=0;i<max_length;i++){
            cout<<seq[i]<<' ';
        }
        return max_length;
    }
};
```









## 1.2 分割类型题目

给定长度为n的序列或字符串，要求划分成若干段

* 段数不限，或者指定k段
* 每一段满足一定的性质







### 91.编码方法

**题目**：字母A-Z可以表示为数字1-26，给你一个含有非空字符串，返回编码方法总数

**题目分析**:

给你一段字符串，问你有多少种分割方法。例如"123"，那么有"1,2,3"、“12,3”、“1,23”三种分割方法。

这题目可以用动态规划的方法，考虑最后一步，最后一个字符跟前面的组合或者自己组合，第一个是问题将第i个字符和第i-1个字符组合时候的编码总数，第二种问题是第i个字符独立组合的编码总数。

* 第二种情况，dp[i]=dp[i-1]，例如“123”，本来就有三种编码方法，现在字符串是"1234"，就只是多了一个字符。那么编码方式为"1,2,3,4"、“12,3,4”、“1,23,4”也是3种，只是在每种编码方式后面多加一个数字吧了。编码方式总数并没有改变。求前i个编码就变成求前i-1个编码总数，变成求解子问题。
* 第一种情况，dp[i]=dp[i-2]，最后两个字符结合编码，把那两个字符看成一个整体，跟第一种情况的计算还是很类似。“1234”可以分割为“1,2,34”,"12,34"，把"34"一个整体，把它当做是第i个字符，那么dp[i]=dp[i-2]。

设dp[i]为到第i个字符为止的编码总数，很明显，转移方程为dp[i]=dp[i-1]+dp[i-2]，当然这个不考虑数字大小的约束。

转移方程分情况讨论：

* 当第i个字符为"0"时，只能跟前面的字符进行编码，而且前面的字符只能是"1"或者"2"，否则返回0。dp[i]=dp[i-2]
* 当第i个字符不为“0”时，跟前面的字符编码的条件是编码后大小在1-26范围之内，否则，它只能自己跟自己编码了。

**代码**

```
class Solution {
public:
    int numDecodings(string s) {
        int n =s.length();
        if (n==0||s[0]=='0')return 0;
        vector<int>dp(n+1,1);
        for(int i=2;i<=n;i++){//从i=2开始，因为dp[i-2]，dp[i]从第1个字符开始
            if (s[i-1]=='0'){
                if (s[i-2]=='1'||s[i-2]=='2'){dp[i]=dp[i-2];}
                else return 0;
            }
            else{
                int num=10*(s[i-2]-'0')+s[i-1]-'0';
               
                if (num>=10&&num<=26){//不要写成10<=num<=26，否则都是True
         
                    dp[i]=dp[i-1]+dp[i-2];
                }
                else{
                    dp[i]=dp[i-1];
                }
            }
        }
        return dp[n];
    }
};
```

空间压缩：pre1,pre2分别代替dp[i-1],dp[i-2],但是发现速度和内存消耗更大一点。

```
class Solution {
public:
    int numDecodings(string s) {
        int n =s.length();
        if (n==0||s[0]=='0')return 0;
        vector<int>dp(n+1,1);
        int pre1=1,pre2=1,cur=1;
        for(int i=2;i<=n;i++){
            if (s[i-1]=='0'){
                if (s[i-2]=='1'||s[i-2]=='2'){
                    cur=pre2;
                    pre2=pre1;//尽管dp[i]=dp[i-2];但是pre1和pre2都要更新
                    pre1=cur;
    
                    }
                else return 0;
            }
            else{
                int num=10*(s[i-2]-'0')+s[i-1]-'0';
               
                if (num>=10&&num<=26)
                   
                    cur=pre1+pre2;
                    pre2=pre1;
                    pre1=cur;

                }
                else{
             
                    cur=pre1;
                    pre2=pre1;
                    pre1=cur;
                  
                }
            }
        }
        return cur;
    }
};
```



### 279. 完全平方数

**题目:**给定一个正整数，求其最少可以由几个完全平方数相加构成。

**题目分析**:

题目其实就是给正整数划分成k段，然后每段都是一个完全平方数，那么最后一步最优策略中最后一段呗，假设前面k-1段已经是最优策略了，最后一段是多少呢，设j^2。那么前k-1段就是n-j^2。所以dp[n]= dp[n-j^2]+1，但是j不知道呀，所以要枚举j然后求出最值。本来dp[n],现在就变成求dp[n-j^2]的子问题。但是光枚举j还不行，因为要把dp[n-j^2]=dp[i]求出来，所以还要枚举i，也就是先把小的dp求出来，才能够求出大的dp。

思考从大问题到小问题，计算从小问题到大问题。

设dp[i]为i最少的完全平方数组成个数。那么dp[i]=dp[i-kxk]+1，1表示的是kxk这一个完全平方数。题目求最少值，然后我们就不断迭代更新dp[i]，dp[i]=min(dp[i],dp[i-kxk]+1)。

**代码**

```
class Solution {
public:
    int numSquares(int n) {
        vector<int>dp(n+1,INT_MAX);
        dp[0]=0;
        for(int i=1;i<=n;i++)
        for(int k=1;k*k<=i;k++){//要=i^2，是因为完全平方数可以有1个完全平方数组成。
            dp[i]=min(dp[i],dp[i-k*k]+1);//求解子问题
        }
        return dp[n];
    }
};
```



### 132.分割回文字符串2

**题目**：给你一个字符串 `s`，请你将 `s` 分割成一些子串，使每个子串都是回文。返回符合要求的 最少分割次数 。

**分析**：这些分割类的题目，最后一步都是关注最优策略的最后一段字符[j,n-1]，假设最后一段字符[j,n-1]是回文字符串，那么dp[i]=min[dp[i],dp[j]+1]，其中dp[i]表示前i个字符的最少分割次数。所以现在的问题是如何判断回文字符串。

代码

```c++
class Solution {
public:

    int minCut(string s) {
        int n = s.length();
        vector<int>dp(n+1,INT_MAX);
        vector<vector<bool>>ispalin(n,vector<bool>(n,false));
        int i,j;
        //odd奇数，以字符为中心
        for(int c=0;c<n;c++){
            i=j=c;
            while(i>=0&&j<n&&s[i]==s[j]){
                ispalin[i][j]=true;
                i--;
                j++;
            }
        }
        //偶数，以字符间的线为中心
        for(int c=0;c<n-1;c++){
            i=c;
            j=c+1;
            while(i>=0&&j<n&&s[i]==s[j]){
                ispalin[i][j]=true;
                i--;
                j++;
            }
        }
    
        dp[0]=0;
        for(int i=1;i<=n;i++){
            for(int j=0;j<i;j++){
                if(ispalin[j][i-1]==true){
                    dp[i] = min(dp[i],dp[j]+1);
                }    
            }  
        }
        return dp[n]-1;//段数-1=分割次数
    }
};
```















## 1.3 序列型

给定一个序列，dp[i]中的下标i表示前i个元素a[0]，a[1]...，a[i-1]的某种性质(坐标型以第i个元素结尾的某种性质)



### 1143.最长公共子序列

**题目**：给定两个子序列，求他们的最长公共子序列。

**题目分析**：还是分解成子问题。设i,j分别指向text1和text2的字符，要求两个子序列的最长公共子序列，我们将问题分解成求i=1,j=1...j,i=2,j=1...j,...,多个子问题，分别求解，然后求出最值。设dp[i,j]为text1,text2的第i,j个字符为止的最长公共子序列(不要求子序列以第i,j个字符结尾)，那么接下来想的是dp[i,j]和dp[i-1,j-1]、dp[i-1,j]、dp[i,j-1]的关系。

公共子序列，那么他们的字符是相等的，而且他们的顺序相对于原字符串是没有变化的。如果text[i]=text[j]，那么dp[i,j]肯定比之前的状态dp[i-1,j-1]多了一个公共字符，最长公共子序列数量+1。所以dp[i,j]=dp[i,j]+1。如果text[i]!=text[j],那么dp[i,j]跟前一个状态对比是没有增加的，前一个状态是dp[i,j-1]或者dp[i-1,j]，在他们间求最大值即可,那什么时候dp[i,j]=dp[i,j-1]，什么时候dp[i,j]=dp[i-1,j]？

举个例子，text1="acd",text2="ac",当i<3之前，也就是找到最长公共子序列之前，dp[i,j]=dp[i,j-1]；当i=3,j=1时候，如果dp[i,j]=dp[i,j-1]=1，不对，所以当找到最长公共子序列之后，dp[i,j]=dp[i-1,j];

**代码**

```
class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {
        int m=text1.length(),n=text2.length();
        vector<vector<int>>dp(m+1,vector<int>(n+1,0));
        for(int i=1;i<=m;i++ ){
            for(int j=1;j<=n;j++){
                if (text1[i-1]==text2[j-1]){
                    dp[i][j]=dp[i-1][j-1]+1;
                }
                else{
                    dp[i][j]=max(dp[i-1][j],dp[i][j-1]);
                }
            }
        }
        return dp[m][n];
    }
};
```

### 198.打家劫舍

**题目**：小偷计划偷窃，可是相邻的房屋不能够闯入，问一夜之内能够盗取的最高金额。

**题目分析**:

设dp[i]为前i个房子盗取的最大金额。最后一步，当盗取最后一家房子的时候要确定前一家房子是不是已经盗取了，所以是不是说要像256油漆房子那道题目一样，要把前一个状态状态记下来。后来发现好像不用也可以，只要在对比一下前一家盗取和不盗取两种情况及取最大值可以了，max(dp[i-1]+nums[i-1],dp[i-1])。

代码

```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        int m = nums.size();
        if (m==0) return 0;
        vector<int>dp(m+1,0);
        dp[1] = nums[0];
        for (int i=2;i<=m;i++){
            dp[i]=max(dp[i-2]+nums[i-1],dp[i-1]);
        }
        return dp[m];
    }
};
```

确定cur,pre,ppre分别表示dp[i],dp[i-1],dp[i-2]，即可进行空间压缩：

```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        int m = nums.size();
        if (m==0) return 0;
        if (m==1) return nums[0];
        int cur = 0,ppre = 0;
        int pre = nums[0];

        for(int i=2;i<=m;i++){
            cur=max(ppre+nums[i-1],pre);
            ppre = pre;
            pre = cur;
        }
        return cur;
    }
};
```

### 213.打家劫舍2

**题目**：小偷计划偷窃，可是相邻的房屋不能够闯入，所有房子围成一圈，这意味着第一个房屋和最后一个房屋是紧挨着的，问一夜之内能够盗取的最高金额。

**题目分析**：要想办法把圈转换成序列，因为一头一尾不能同时偷，那是不是那不偷头，要不偷尾？也不是，而是要不没偷头，要不没偷尾。那么就分两种情况考虑。如果不偷头的话，就拿不计算头呗，尾同理。

代码

```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        int m = nums.size();
        if (m==0) return 0;
        if (m==1) return nums[0];
        vector<int>dp(m+1,0);
        //要么第一个房子没偷，要么最后一个房子没偷，不能认为要么第一个房子偷了，
        //要么最后一个房子偷了，其实也有可能这两个房子都没偷
        dp[1]=0;
        for (int i=2;i<=m;i++){
            dp[i]=max(dp[i-2]+nums[i-1],dp[i-1]);      
        }
        int res = INT_MIN;
        res = max(dp[m],res);
        dp[1]=nums[0]; 
        for (int i=2;i<m;i++){
        dp[i]=max(dp[i-2]+nums[i-1],dp[i-1]);
        }
        res = max(dp[m-1],res);
        return res;
    }
};
```



### 121. 买卖股票的最佳时机

**题目**：给定一个数组，表示每天的股票价格，只可以一次买卖，求最大利润。

**分析**:   当时只是想到暴力法，而且还是跟动态规划没有太大关系。设dp[i]为第i天的最大利润(并不需要知道是哪天买的)，那这个dp[i]的转移方程怎么求呢，最后一步，那我们要对比前一天为止的最大利润和当天卖出去的最大利润相比哪个更大，所以dp[i]=max(dp[i-1],p[i]-vmin)；p[i]-vmin是当天卖出去的最大利润。

代码：

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
    int m = prices.size();
    if (m==0||m==1) return 0;
    
    vector<int>dp(m+1,0);
    int vmin = prices[0];
    for(int i=1;i<=m;i++){
     
     dp[i]=max(dp[i-1],prices[i-1]-vmin);
     vmin = min(vmin,prices[i-1]);
    }
 
    return dp[m];
    }
```

空间压缩后：

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
    int m = prices.size();
    if (m==0||m==1) return 0;
    

    int vmin = prices[0];
    int res = 0;
    for(int i=1;i<=m;i++){
     
     res=max(res,prices[i-1]-vmin);
     vmin = min(vmin,prices[i-1]);
    }
 
    return res;
    }

};
```

### 122 买卖股票的最佳时机 2

**题目**：给定一个数组，表示每天的股票价格，可以多次买卖，求最大利润。

**分析**：其实只要把所有上升的利润加起来就是最大的利润了。所以最后一步，上次的利润+上升的利润。dp[i]表示前i天的最大利润，dp[i]=max(dp[i-1]+p[i-1]-p[i-2],dp[i-1])。

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int m = prices.size();
        if(m==0||m==1) return 0;
        int res = 0;
       
        for(int i=2;i<=m;i++){
            res=max(res+prices[i-1]-prices[i-2],res);
        }
        return res;

    }
};
```





## 1.4 背包问题

背包问题是一种组合优化的 NP 完全问题:有 N 个物品和容量为 W 的背包,每个物品都有自己的体积 w 和价值 v,求拿哪些物品可以使得背包所装下物品的总价值最大。如果限定每种物品只能选择 0 个或 1 个,则问题称为 0-1 背包问题;如果不限定每种物品的数量,则问题称为无界背包问题或完全背包问题。

使用动态规划方法来解决0-1背包问题。以0-1背包问题为例，设dp[i,j]表示为前i件物品体积不超过j情况下的最大总价值。面对第i件物品时候，如果不拿，那么状态跟i-1件物品是一样的，dp[i,j]=dp[i-1,j]，如果拿了第i件物品，那么总价值应该是前i-1个物品的总价值+第i个物品的价值v，而且前i-1个物体的体积不能超过j-w，所以有dp[i,j]=dp[i-1,j-w]+v。

```python
int knapsack(vector<int> weights, vector<int> values, int N, int W){
    vector<vector<int>>dp(N+1,vector<int>(W+1,0));
    for(int i=1;i<N;i++){
        int w = weight[i-1],v = value[j-1];
        for(int j=1;j<W;j++){
            if(j>w){
                dp[i][j]=max(dp[i-1][j],dp[i-1][j-w]+v);
            }
            else{
                dp[i][j]=dp[i-1][j];
            }
            
        }
    }
    return dp[N][W]
}
```

为什么背包问题可以用动态规划来解决？动态规划就是把大问题分解成子问题，子问题又分解成子问题，所以要先解决小问题才能解决大问题。当求前i件物品体积不超过j情况下的最大总价值，我们比较拿和不拿两种情况，那么就要求出dp[i-1,j]和dp[i-1,j-w]两个子问题，以此类推。每次求i，都要求i-1，求j，就要求j-w，所以是两个变量，空间复杂度o(NM)。

假设i=2,w=2,v=3,dp[2,j]=max(dp[1,j],dp[1,j-2]+3)，状态转移矩阵如下图所示：

![](https://gitee.com/weifagan/MyPic/raw/master/img/01backpack.PNG)

因为dp[i,j]只是依靠i-1，那么我们就只需要一个维度，把更新的值更换旧的值，从图中可以看到，如果只有一维度，如果我们从j=1,2,3...开始，当计算j=5时候，当取j=3的值时，已经被更新了，但是我们需要的是更新前的值，所以我们就要逆向运算。j从最大开始j=n...1。

```python
int knapsack(vector<int> weights, vector<int> values, int N, int W){
    vector<int>dp(N+1,0);
    for(int i=1;i<N;i++){
        int w = weight[i-1],v = value[j-1];
        for(int j=W;j>w;j--){
      	dp[j]=max(dp[j],dp[j-w]+v);   
        }
    }
    return dp[N][W]
}
```



对于完全背包问题，也就是某个物品可以重复拿取，那么我们可以认为这个物品有很多份，但是序号还是i，如果不拿物品i，那么有dp[i,j]=dp[i-1,j]，如果拿n个(n>=2)，因为都是序号i物品，所以前一个状态还是dp[i,j-w]+v，而不是dp[i-1,j-w]+v。

```C++
int knapsack(vector<int> weights, vector<int> values, int N, int W){
    vector<vector<int>>dp(N+1,vector<int>(W+1,0));
    for(int i=1;i<N;i++){
        int w = weight[i-1],v = value[j-1];
        for(int j=1;j<W;j++){
            if(j>w){
                dp[i][j]=max(dp[i-1][j],dp[i][j-w]+v);
            }
            else{
                dp[i][j]=dp[i-1][j];
            }
            
        }
    }
    return dp[N][W]
}
```

状态转移矩阵为：

![](https://gitee.com/weifagan/MyPic/raw/master/img/backpack.PNG)

空间压缩后：

```c++
int knapsack(vector<int> weights, vector<int> values, int N, int W){
    vector<int>dp(W+1,0);
    for(int i=1;i<N;i++){
        int w = weight[i-1],v = value[j-1];
        for(int j=1;j<W;j++){
            if(j>w){
                dp[i]=max(dp[j],dp[j-w]+v);
            }
            else{
                dp[i][j]=dp[i-1][j];
            }
            
        }
    }
    return dp[N][W]
}
```



### 416.分割成相等的两个子集

**题目**：给定一个正整数数组，求是否能够将这个数据分成相等的两个部分。

**题目分析**：其实这个0-1背包问题，因为数组每个数只能去一次，看是否能够取某些数，让它的和为总和的一半。设dp[i,j]为前i个物体和是否能够达到j，对于某个物体，就两种选择，不拿的话，跟i-1的状态是一样的，如果拿的话，那么前一个物体i-1的状态，总和达到j-nums[i]的状态是一样的。所以dp[i,j]=dp[i-1,j] or dp[i-1,j-nums[i]]。还是跟0-1背包问题一样，就是没有价值这个概念。

梳理一下，为什么老是申明一个n+1的数组，因为我们动态规划，当前状态跟前一个状态有关系，当计算第一状态时候，必须要有前一个状态跟它扯上关系，所以通常要申明n+1的数组，并且要做初始化。

```c++	
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int sum = accumulate(nums.begin(),nums.end(),0);
        if (sum%2) return false;//奇数直接返回false
        int target = sum/2,n = nums.size();  
        vector<vector<bool>>dp(n+1,vector<bool>(target+1,false));

        for (int i=0;i<=n;i++){dp[i][0]=true;}//和为0，认为阔以

        for(int i=1;i<=n;i++){
            for(int j=nums[i-1];j<=target;j++){
                dp[i][j]=dp[i-1][j]||dp[i-1][j-nums[i-1]];
               
            }
        }

        return dp[n][target];
    }
};
```

空间压缩：

```c++
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int sum = accumulate(nums.begin(),nums.end(),0);
        if (sum%2) return false;
        int target = sum/2,n = nums.size();
        vector<bool>dp(target+1,false);

        for (int i=0;i<=n;i++){dp[i][0]=true;}

        for(int i=1;i<=n;i++){
            for(int j=target;j>=nums[i-1];j--){
                dp[j]=dp[j]||dp[j-nums[i-1]];
               
            }
        }

        return dp[target];
    }
};
```

## 1.5 字符串编辑

### 650 只有两个键的键盘

**题目**:给定一个字母A，每次只可以有两种操作，1.复制全部字符，2.粘贴之前的字符，求最少需要几次操作才可以把字符串延展到指定长度。

**分析**：一开始不是很懂题目意思，其实就是复制跟粘贴，如果复制只能复制全部字符，复制了之后那就可以多次粘贴。假如要把字符串延展到指定长度3，那么复制字符A，然后粘贴两次，那么就要操作3次了。假如长度4，那么其实只要将2的长度复制粘贴就好了，所以dp[i]=dp[j]+i/j，dp[i]表示为字符串延展到指定长度i的最少操作次数。i/j表示的就是操作次数。

```c++
class Solution {
public:
    int minSteps(int n) {
        vector<int>dp(n+1,n+1);
     
        dp[1]=0;
        for (int i=2;i<=n;i++){
            for (int j=1;j<=n/2;j++){
                if (i%j==0){
                    dp[i]=min(dp[i],dp[j]+i/j);
                }
            }
        }
        return dp[n];

    }
};
```

自己有两个问题，

1.不在知道几个循环去处理问题，这个感觉是还没完全弄明白这个状态转移方程，还没明白动态规划的逻辑是如何解决问题的。把小问题的解存储起来，用于解决大问题。

2.不知道如何初始化，因为当前状态依赖于前面的状态，所以初始化就是为了得到第一个正确的状态。

## 1.6 存在性问题

存在性问题dp的关系通常用是|| &&

### 55 跳跃问题

**题目**：给定一个非零数组，数组中的每个元素代表你在该位置可以跳跃的最大长度。判断你是否能够到达最后一个下标。

**分析**：存在性问题也会考虑用动态规划。最后一步，当前元素的值大于到最后元素的距离则可到达，dp[i]=dp[j]，那么就变成求解dp[j]这个子问题了。

```c++
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int n = nums.size();
        vector<bool>dp(n+1,false);   
        if (n==0)return false;
        if (nums[0]==0&&n>1) return false;
        dp[1]=true;
        for(int i=1;i<=n;++i){
            for(int j=1;j<i;++j){
                if (nums[j-1]>=(i-j)){
                       dp[i]=dp[j]; //或者dp[i]=dp[j]&&(nums[j-1]>=(i-j)
                    break;//如果某个元素能到达，就不用再算后面的呢。
                }
            }
        }

        return dp[n];
    }
};
```

