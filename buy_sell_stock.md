# Buy & Sell Stock

Statement: given an array `prices` where `prices[i]` is the price of a given stock on the `ith` day, return the maximum profit you can achieve from this transaction. If you cannot achieve any profit, return 0.

**General Case**:

You are given an integer array prices where prices[i] is the price of a given stock on the ith day, and an integer k.

Find the maximum profit you can achieve. You may complete at most k transactions: i.e. you may buy at most k times and sell at most k times.

Note: You may not engage in multiple transactions simultaneously (i.e., you must sell the stock before you buy again).

**Main Idea**:

Solve with DP, consider the state and potential decision for each day.

There are two states for a single day: 1) hold the stock, 2) not hold the stock. And there are two decision we could do to reach each state:

1. We hold stock at ith day, we may
   1. we do not hold stock on (i - 1)th day and we buy stock on ith day.
   2. we hold stock on (i - 1)th day and we at rest(not buy or sell) on ith day.

2. We do not hold stock at ith day, we may
   1. we hold stock on (i - 1)th day and we sell stock on ith day.
   2. we do not hold stock on (i - 1)th day and we at rest on ith day.

**DP Table**:

In this case, our DP table will contaim 3-dimension: `dp[i][k][0 or 1]` i - day, k -number of transaction, state (0 represents not holdinhh stock, 1 represents holding stock). This DP table record the profit we have for current optimal decision.

```java
dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])
           // max( 今天选择 rest,        今天选择 sell       )

dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])
           // max( 今天选择 rest,         今天选择 buy         )
```

Note: `k` in dp table means the upper bound of the transaction. If we want to buy stock on ith day with upper bound k, we must make sure that the upper bound of transaction on (i - 1)th day is k - 1.

```java
// base case：
dp[-1][...][0] = dp[...][0][0] = 0
dp[-1][...][1] = dp[...][0][1] = -infinity
// i = -1 means we do not begin and k = 0 means we can not make transaction, then we do not have profit and cannot hold stock

// DP Function：
dp[i][k][0] = Math.max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])
dp[i][k][1] = Math.max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])
```

This DP structure can be applied to all buy & sell stock problem with small modification.
