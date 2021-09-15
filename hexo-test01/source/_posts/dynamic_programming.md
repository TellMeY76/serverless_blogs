---
title: 动态规划练习
tags: [Javascript]
categories: learning
toc: true
mathjax: true
---

练习解决动态规划问题

 <!-- more -->

#### 问题1

在一条直线上，有n个房屋，每个房屋中有数量不等的财宝，有一个盗 贼希望从房屋中盗取财宝，由于房屋中有报警器，如果同时从相邻的两个房屋中盗取财宝就会触发报警器。问在不触发报警器的前提下，最多可获取多少财宝？例如 [5，2，6，3，1，7]，则选择5，6，7


#### 解法

状态转移公式：dp[i]=Max(dp[i-2]+v[i],dp[[i-1]])
v[i]为第i栋房屋中的财产价值，dp[i]为有i栋房屋时，最多可获取的财宝数

``` js

function steal(houseArr){
  if(!houseArr||!houseArr.length){
    return 0
  }
  const nums=houseArr.length
  const maxSteal=[]
  maxSteal[0]=houseArr[0]
  if(nums >= 2){
  maxSteal[1]=Math.max(houseArr[0], houseArr[1])
  for(let i = 2;i<nums;i++){
     maxSteal[i]=Math.max(maxSteal[i-2]+houseArr[i], maxSteal[i-1])  
    }
  }
  return  maxSteal[nums-1]
}

```

#### 问题2

给定一个数组，求这个数组的连续子数组中，最大的那一段的和。
如数组[-2,1,-3,4,-1,2,1,-5,4] 的子段为：
[-2,1]、[1,-3,4,-1]、[4,-1,2,1]、...、[-2,1,-3,4,-1,2,1,-5,4]，和最大的是[4,1,2,1]，为6。

#### 解法

状态转移公式： dp[i] = max(dp[i-1]+nums[i],nums[i])
nums[i]为数组中第i项的值，dp[i]为结尾为nums[i]的子段的最大子段和

``` js

function maxSum(arr){
    if(!arr||!arr.length){
        return 0
     }
    const maxSum=[]
    const numLen=arr.length
    maxSum[0]=arr[0]
   let max=maxSum[0]
    if(numLen>=2){
      for(let i=1;i<numLen;i++){
         maxSum[i]=Math.max(arr[i],maxSum[i-1]+arr[i])
         if(maxSum[i]>max){
             max=maxSum[i]
         }
     }
  }
    return max
}

```

#### 问题3
已知不同面值的钞票，求如 何用最少数量的钞票组成某个金额，求可 以使用的最少钞票数量。如果任意数量的已知面值钞票都无法组成该金额， 则返回-1。

``` bash
Input: coins = [1, 2, 5], amount = 11
Output: 3 
Explanation: 11 = 5 + 5 + 1
Input: coins = [2], amount = 3
Output: -1

```


#### 解法

状态转移公式：dp[i] = min(dp[i-1], dp[i-2], dp[i-5]) + 1
解释公式： 求出各种面值为所需要的最后一张时所需要的张数，取最小值

``` js

function minMoneyCount(coins,amount){
    const minMoney=[];
    const coinLen=coins?coins.length:0;
     if (coinLen == 0 || amount < 0){
       return -1;
     }
    for (let i = 0; i <= amount; i++){
        minMoney[i] = -1;
    }
    for(let coin in coins){
        minMoney[coin]=1;
   }
    for(let i = 1; i <= amount;i++){
        for (let j = 0; j < coinLen; j++){
            if (i - coins[j] >= 0 && minMoney[i - coins[j]] !== -1){
                if (minMoney[i] == -1 || minMoney[i] > minMoney[i - coins[j]] + 1){
                    minMoney[i] = minMoney[i - coins[j]] + 1;
              }
            }
         }
  }
    return minMoney[amount];
}


```

#### 问题4
给定一个二维数组，其保存了一个数字三角形 triangleMatrix[] []，求从数字三角形顶端到底端各数字和最小的路径之和，每次可以向下走相邻的两个位置，例如：

由以下数组
[
[2],
[3,4],
[6,5,7],
[4,1,8,3]
]

可得出最短路径为 2 + 3 + 5 + 1 = 11

#### 解法

从下往上走，即最底层为起始层

状态转移公式： dp[i][j]=min(dp[i+1][j],dp[i+1][j+1])+triangle[i][j]

解释：dp[i][j]为到达i行j列的最短路径，triangle[i][j]为二维数组中[i][j]对应的元素，从下往上走，即为到达正下或者右下的最短路径再加上[i][j]那一格的数值

``` js
function minPath(triangle){
 const triangleLen=triangle?triangle.length:0

  if(!triangle || !triangleLen){
      return 0
  }
  const minPath=new Array(triangleLen).fill([])

  if(triangleLen===1){
      return triangle[0][0]
  }
 
 for(let i =0;i < triangle[triangleLen-1].length;i++){
     minPath[triangleLen-1][i] = triangle[triangleLen-1][i]
 }

 for(let i =triangleLen-2; i >=0;i--){
     for (let j = 0; j<=i;j++){
        minPath[i][j] = Math.min(minPath[i+1][j],minPath[i+1][j+1]) + triangle[i][j];
     }
  }

 return minPath[0][0];

}

```