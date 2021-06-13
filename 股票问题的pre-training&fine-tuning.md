\- [理解动态规划](#理解动态规划)

\- [动态规划的通解](#动态规划的通解)

\- [特解—具体的问题情境对应具体的状态转移方程、具体的最简单情况](#特解具体的问题情境对应具体的状态转移方程具体的最简单情况)

  \- [【121】k=1](#121k1)

​    \- [最简单情况](#最简单情况)

​    \- [状态转移方程](#状态转移方程)

​    \- [代码实现](#代码实现)

​    \- [代码tips](#代码tips)

  \- [【122】k=+∞](#122k)

​    \- [最简单情况](#最简单情况-1)

​    \- [状态转移方程](#状态转移方程-1)

​    \- [代码实现](#代码实现-1)

  \- [【123】k=2](#123k2)

  \- [【188】k任意](#188k任意)

​    \- [最简单情况](#最简单情况-2)

​    \- [状态转移方程](#状态转移方程-2)

​    \- [代码实现](#代码实现-2)

  \- [【309】k=+∞，有冷冻时间(影响用哪一天)](#309k有冷冻时间影响用哪一天)

​    \- [最简单情况](#最简单情况-3)

​    \- [状态转移方程](#状态转移方程-3)

​    \- [代码实现](#代码实现-3)

  \- [【714】k=+∞，每笔交易都有手续费（影响收益）](#714k每笔交易都有手续费影响收益)

​    \- [最简单情况](#最简单情况-4)

​    \- [状态转移方程](#状态转移方程-4)

​    \- [代码实现](#代码实现-4)



# 理解动态规划

结果由哪几个特征决定，就分别设几维，分析这么分析，降维或者优化是后边要处理的，上来的时候不那么精细才有助于分析解题

# 动态规划的通解

- 定义 

  ```java
  T[i][k] 
  //在第i天结束时，最多进行k次交易，可以获得的最大收益是T[i][k]
  // 买入让k+1，卖出让k-1
  ```

- 基准benchmark—每一维都不开始

  ```java
  T[-1][k]=0; //没到交易的日子
  T[i][0]=0;  //不允许交易进行
  ```

  数组的索引>=0

  分析的时候该怎么分析就怎么分析，写代码时肯定有方法去解决的

- 状态转移方程—本质：子数组&问题情景

  - 子数组

    ```java
    T[i-1][k] 
    T[i][k-1]
    T[i-1][k-1] 
    ```

  - 问题情景：在第i天

    - 买入
    - 卖出
    - 休息

    题目的限制—可以对一支股票来买卖多次；在手中有股票的时候不能再买入新股票，当前能做的要么是休息要么是卖出。重新定义数组（因为二进制，这样定义数组也是醉了）

    ```java
    // 数组定义
    T[i][k][0] //手里有0支股票
    T[i][k][1] //手里有1支股票
    ```

    ```java
    // benchmark
    T[-1][k][0]=0; //没到交易的日子，手里没有股票
    T[-1][k][1]=-Infinity; //没到交易的日子，手里有股票，则欠钱了
    
    T[i][0][0]=0; //不允许进行交易，手里没有股票
    T[i][0][1]=-Infinity; //不允许进行交易，手里有股票，则欠钱了
    ```

    ```java
    // 状态转移—买入就亏钱，卖出就赚钱
    T[i][k][0]=Math.max(T[i-1][k][0],T[i-1][k][1]+prices[i]);
    T[i][k][1]=Math.max(T[i-1][k-1][0]-prices[i],T[i-1][k][1]);
    ```

    ```java
    // 最后要的返回结果
    T[n-1][k][0] //最后得卖出的状态，手里还有的话其实还是亏了
    ```

    

# 特解—具体的问题情境对应具体的状态转移方程、具体的最简单情况

## 【121】k=1

### 最简单情况

```java
// benchmark
T[-1][k][0]=0; //没到交易的日子，手里没有股票
T[-1][k][1]=-Infinity; //没到交易的日子，手里有股票，则欠钱了

T[i][0][0]=0; //不允许进行交易，手里没有股票
T[i][0][1]=-Infinity; //不允许进行交易，手里有股票，则欠钱了
```

发现k都是1，则可以降维

```java
// 最简单情况
dp[-1][0]=0;
dp[-1][0]=-prices[0];

dp[0][0]=0;
dp[0][1]=-prices[0];
```

### 状态转移方程

```java
T[i][1][0]=Math.max(T[i-1][1][0],T[i-1][1][1]+prices[i]);
T[i][1][1]=Math.max(T[i-1][1][1],-prices[i]); //Math.max(T[i-1][1][1],T[i-1][0][0]-prices[i]) k控制交易次数
```

发现第二维都是1，则降维到二维数组

```java
dp[i][0]=Math.max(dp[i-1][0],dp[i-1][1]+prices[i]);
dp[i][1]=Math.max(dp[i-1][1],-prices[i]);
```

### 代码实现

```java
public class Solution {
    public int maxProfit(int prices[]) {
        int len=prices.length;
        if(prices==null||len==0)
            return 0;
        int[][] dp=new int[len+1][2];
        // 数组的索引>=0
        // dp[-1][0]=0;
        // dp[-1][0]=-prices[0];
        dp[0][0]=0;
        dp[0][1]=-prices[0];
        for(int i=1;i<len;i++){
            dp[i][0]=Math.max(dp[i-1][0],dp[i-1][1]+prices[i]);
            dp[i][1]=Math.max(dp[i-1][1],-prices[i]);
        }
        return dp[len-1][0];
    }
}
```

优化：

从代码回归常识—profit1表示买入，profit1表示休息或者卖出

profit1表示第i天股票价格的相反数的最大值～第i天股票价格的最小值

确实是符合常识的买卖股票获取最大收益啊～

```java
public class Solution {
    public int maxProfit(int prices[]) {
        int len=prices.length;
        if(prices==null||len==0)
            return 0;
        int profit0=0,profit1=-prices[0];
        for(int i=1;i<len;i++){
            profit0=Math.max(profit0,profit1+prices[i]);
            profit1=Math.max(profit1,-prices[i]);
        }
        return profit0;
    }
}
```

### 代码tips

- 【数组为空】和【数组不为空，但是长度为0】不一样

  ```java
  if(prices==null||len==0)
    return 0;
  ```

- 题行一判—对最简单的情况

  ```java
  int[] prices
    
  // 数组是否为空用==null
  if(prices==null||len==0)
    return 0;
  ```

- 定义dp二维数组

  ```java
  int[][] dp=new int[len][2];
  ```

- 时至今日，终于明白了profit0表示昨天的情况下，更新就可以表示今天的了，虽然变量名字相同，但是意义不同了

- 出了个笑话

  ```java
  profit1=-prices[0];// 表示一个数值
  profit1=--prices[0];// 是自减啊！！！ 
  ```

## 【122】k=+∞

### 最简单情况

```java
// benchmark
T[-1][k][0]=0; //没到交易的日子，手里没有股票
T[-1][k][1]=-Infinity; //没到交易的日子，手里有股票，则欠钱了

T[i][0][0]=0; //不允许进行交易，手里没有股票
T[i][0][1]=-Infinity; //不允许进行交易，手里有股票，则欠钱了
```

下标是-1也用不了，数组降维成二维

```java
// 最简单情况
dp[0][0]=0;
dp[0][1]=-prices[0];
```

### 状态转移方程

```java
// 状态转移—买入就亏钱，卖出就赚钱
T[i][k][0]=Math.max(T[i-1][k][0],T[i-1][k][1]+prices[i]);
T[i][k][1]=Math.max(T[i-1][k-1][0]-prices[i],T[i-1][k][1]);
```

k=+∞时，k和(k-1)就是一个数，数组可以降维成二维的

```java
dp[i][0]=Math.max(dp[i-1][0],dp[i-1][1]+prices[i]);
dp[i][1]=Math.max(dp[i-1][0]-prices[i],dp[i-1][1]);
```

### 代码实现

```java
class Solution {
    public int maxProfit(int[] prices) {
        int len=prices.length;
        if(prices==null&&len==0)
            return 0;
        int[][] dp=new int[len][2];
        dp[0][0]=0;
        dp[0][1]=-prices[0];
        for(int i=1;i<len;i++){
            dp[i][0]=Math.max(dp[i-1][0],dp[i-1][1]+prices[i]);
            dp[i][1]=Math.max(dp[i-1][0]-prices[i],dp[i-1][1]);
        }
        return dp[len-1][0];
    }
}
```

优化

```java
class Solution {
    public int maxProfit(int[] prices) {
        int len=prices.length;
        if(prices==null&&len==0)
            return 0;
        int profit1=0,profit2=-prices[0];
        for(int i=1;i<len;i++){
            profit1=Math.max(profit1,profit2+prices[i]);
            profit2=Math.max(profit1-prices[i],profit2);
        }
        return profit1;
    }
}
```

## 【123】k=2

参考【188】k任意

```java
class Solution {
    public int maxProfit(int[] prices) {
        int len=prices.length;
        if(prices==null||len==0)
            return 0;
        if(2>(len/2))
            return solve(prices);
        int[][] dp=new int[3][2];
        for(int i=1;i<=2;i++){
            dp[i][0]=0;
            dp[i][1]=-prices[0]; 
        }
        for(int i=1;i<len;i++){
            for(int j=2;j>0;j--){
                dp[j][0]=Math.max(dp[j][0],dp[j][1]+prices[i]);
                dp[j][1]=Math.max(dp[j-1][0]-prices[i],dp[j][1]);    
            }
        }       
        return dp[2][0];
    }

    public int solve(int[] prices){
        int len=prices.length;
        int profit0=0,profit1=-prices[0];
        for (int i = 1; i < len; i++) {
            profit0 = Math.max(profit0, profit1 + prices[i]);
            profit1 = Math.max(profit1, profit0 - prices[i]);
        }
        return profit0;       
    }
}
```

## 【188】k任意

- prices数组的长度是n，则交易数量<=(n/2) (一个交易需要两天，一天买入，一天卖出，买入价格<卖出价格)；如果k>(n/2)，则是+∞问题
  - 如果是一个很大很大的n，则直接就减少了一半的运算量，时间就少了一半啊，爽！

### 最简单情况

```java
// benchmark
T[-1][k][0]=0; //没到交易的日子，手里没有股票
T[-1][k][1]=-Infinity; //没到交易的日子，手里有股票，则欠钱了

T[i][0][0]=0; //不允许进行交易，手里没有股票
T[i][0][1]=-Infinity; //不允许进行交易，手里有股票，则欠钱了
```

```java
for(int i=1;i<=k;i++){
  dp[0][i][0]=0;
  dp[0][i][1]=-prices[0]; 
}
```

### 状态转移方程

```java
// 状态转移—买入就亏钱，卖出就赚钱
T[i][k][0]=Math.max(T[i-1][k][0],T[i-1][k][1]+prices[i]);
T[i][k][1]=Math.max(T[i-1][k-1][0]-prices[i],T[i-1][k][1]);
```

第二维都是k，则降维成二维数组

```java
for(int i=1;i<len;i++){
  for(int j=k;j>0;j--){
    dp[i][j][0]=Math.max(dp[i-1][j][0],dp[i-1][j][1]+prices[i]);
    dp[i][j][1]=Math.max(dp[i-1][j-1][0]-prices[i],dp[i-1][j][1]);    
  }
}

return dp[len-1][k][0];
```

### 代码实现

```java
class Solution {
    public int maxProfit(int k, int[] prices) {
        int len=prices.length;
        if(prices==null||len==0)
            return 0;
        if(k>(len/2))
            return solve(prices);
        int[][][] dp=new int[len][k+1][2];
        for(int i=1;i<=k;i++){
            dp[0][i][0]=0;
            dp[0][i][1]=-prices[0]; 
        }
        for(int i=1;i<len;i++){
            for(int j=k;j>0;j--){
                dp[i][j][0]=Math.max(dp[i-1][j][0],dp[i-1][j][1]+prices[i]);
                dp[i][j][1]=Math.max(dp[i-1][j-1][0]-prices[i],dp[i-1][j][1]);    
            }
        }
        return dp[len-1][k][0];
    }

    public int solve(int[] prices){
        int len=prices.length;
        int[][] dp = new int[len][2];
        dp[0][0] = 0;
        dp[0][1] = -prices[0];
        for (int i = 1; i < len; i++) {
            dp[i][0] = Math.max(dp[i - 1][0], dp[i - 1][1] + prices[i]);
            dp[i][1] = Math.max(dp[i - 1][1], dp[i - 1][0] - prices[i]);
        }
        return dp[len-1][0];       
    }
}
```

看到

```java
dp[i][...]只和dp[i-1][...]有关，所以可以降维成二维数组
```

```java
class Solution {
    public int maxProfit(int k, int[] prices) {
        int len=prices.length;
        if(prices==null||len==0)
            return 0;
        if(k>(len/2))
            return solve(prices);
        int[][] dp=new int[k+1][2];
        for(int i=1;i<=k;i++){
            dp[i][0]=0;
            dp[i][1]=-prices[0]; 
        }
        for(int i=1;i<len;i++){
            for(int j=k;j>0;j--){
                dp[j][0]=Math.max(dp[j][0],dp[j][1]+prices[i]);
                dp[j][1]=Math.max(dp[j-1][0]-prices[i],dp[j][1]);    
            }
        }       
        return dp[k][0];
    }

    public int solve(int[] prices){
        int len=prices.length;
        int profit0=0,profit1=-prices[0];
        for (int i = 1; i < len; i++) {
            profit0 = Math.max(profit0, profit1 + prices[i]);
            profit1 = Math.max(profit1, profit0 - prices[i]);
        }
        return profit0;       
    }
}
```

## 【309】k=+∞，有冷冻时间(影响用哪一天)

### 最简单情况

和k=+∞一样

### 状态转移方程

k=+∞

```java
// 状态转移—买入就亏钱，卖出就赚钱
T[i][k][0]=Math.max(T[i-1][k][0],T[i-1][k][1]+prices[i]);
T[i][k][1]=Math.max(T[i-1][k-1][0]-prices[i],T[i-1][k][1]);
```

```java
dp[i][0]=Math.max(dp[i-1][0],dp[i-1][1]+prices[i]);
dp[i][1]=Math.max(dp[i-1][0]-prices[i],dp[i-1][1]);
```

考虑到冷冻时间—买入股票要在卖出股票的第2天

```java
T[i][k][0]=Math.max(T[i-1][k][0],T[i-1][k][1]+prices[i]);
T[i][k][1]=Math.max(T[i-2][k-1][0]-prices[i],T[i-1][k][1]);
```

```java
dp[i][0]=Math.max(dp[i-1][0],dp[i-1][1]+prices[i]);
dp[i][1]=Math.max(dp[i-2][0]-prices[i],dp[i-1][1]);//i在[1,len)之间会超出数组边界
```

```java
// 对于dp[i-2][0]
if(i>=2)
  dp[i-2][0]
//else也就是dp[-1][0]：还没有开始交易，手里也没有股票
else
  0

//写成一个三目表达式
dp[i][1]=Math.max((i>=2?dp[i-2][0]:0)-prices[i],dp[i-1][1]);
```

### 代码实现

```java
class Solution {
    public int maxProfit(int[] prices) {
        int len=prices.length;
        if(prices==null||len==0)
            return 0;
        int[][] dp=new int[len][2];
        dp[0][0]=0;
        dp[0][1]=-prices[0];
        for(int i=1;i<len;i++){
            dp[i][0]=Math.max(dp[i-1][0],dp[i-1][1]+prices[i]);
            dp[i][1]=Math.max((i>=2?dp[i-2][0]:0)-prices[i],dp[i-1][1]);
        }
        return dp[len-1][0];
    }
}
```

优化—3个状态 i-2,i-1,i，用两个变量走，更新变量就可以了

```java
class Solution {
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length == 0) {
            return 0;
        }
        int pre = 0, cur1 = 0, cur2 = -prices[0];
        int length = prices.length;
        // 为什么下面这种写法不行—表面上是符合逻辑的啊
        // for (int i = 1; i < length; i++) {
               // pre在这里更新了
        //     pre = cur1;
        //     cur1= Math.max(cur1, cur2 + prices[i]);
               // 这里用的还是没有更新时的pre
        //     cur2 = Math.max(cur2, pre - prices[i]);
        // }
        for (int i = 1; i < length; i++) {           
            int newCur1= Math.max(cur1, cur2 + prices[i]);
            int newCur2 = Math.max(cur2, pre - prices[i]);
            pre = cur1;
            cur1=newCur1;
            cur2=newCur2;
        }
        return cur1;
    }
}
```

## 【714】k=+∞，每笔交易都有手续费（影响收益）

### 最简单情况

和k=+∞一样

### 状态转移方程

k=+∞

```java
// 状态转移—买入就亏钱，卖出就赚钱
T[i][k][0]=Math.max(T[i-1][k][0],T[i-1][k][1]+prices[i]);
T[i][k][1]=Math.max(T[i-1][k-1][0]-prices[i],T[i-1][k][1]);
```

```java
dp[i][0]=Math.max(dp[i-1][0],dp[i-1][1]+prices[i]);
dp[i][1]=Math.max(dp[i-1][0]-prices[i],dp[i-1][1]);
```

考虑到手续费—买入卖出是一套动作，可以在买入时就计入手续费，也可以在卖出时计入手续费

```java
// 买入时计入手续费—从0变1时要-fee
T[i][k][0]=Math.max(T[i-1][k][0],T[i-1][k][1]+prices[i]);
T[i][k][1]=Math.max(T[i-1][k-1][0]-prices[i]-fee,T[i-1][k][1]);
```

```java
dp[i][0]=Math.max(dp[i-1][0],dp[i-1][1]+prices[i]);
dp[i][1]=Math.max(dp[i-1][0]-prices[i]-fee,dp[i-1][1]);
```



```java
// 卖出时计入手续费—从1变0时要-fee
T[i][k][0]=Math.max(T[i-1][k][0],T[i-1][k][1]+prices[i]-fee);
T[i][k][1]=Math.max(T[i-1][k-1][0]-prices[i],T[i-1][k][1]);
```

```java
dp[i][0]=Math.max(dp[i-1][0],dp[i-1][1]+prices[i]-fee);
dp[i][1]=Math.max(dp[i-1][0]-prices[i],dp[i-1][1]);
```

### 代码实现

```java
class Solution {
    public int maxProfit(int[] prices, int fee) {
        int len=prices.length;
        if (prices == null || len == 0) {
            return 0;
        }
        int[][] dp=new int[len][2];
        dp[0][0]=0;
        dp[0][1]=-prices[0]-fee;
        for(int i=1;i<len;i++){
            dp[i][0]=Math.max(dp[i-1][0],dp[i-1][1]+prices[i]);
            dp[i][1]=Math.max(dp[i-1][0]-prices[i]-fee,dp[i-1][1]); 
        }
        return dp[len-1][0];
    }
}
```

优化

```java
class Solution {
    public int maxProfit(int[] prices, int fee) {
        int len=prices.length;
        if (prices == null || len == 0) {
            return 0;
        }
        int profit0=0,profit1=-prices[0]-fee;
        for(int i=1;i<len;i++){
            profit0=Math.max(profit0,profit1+prices[i]);
            profit1=Math.max(profit0-prices[i]-fee,profit1); 
        }
        return profit0;
    }
}
```

