# Binary Search and Its Variants

1. Contains
2. First/Last occurrence
3. Least greater/Greatest lesser

## 1. Contains

Statement: given a sorted array `arr` and a target value `key`, return the index of `key` in `arr`.

```java
public int contains(int[] arr, int key) {
    int left = 0, right = arr.length - 1;

    while (left <= right) {
        int mid = left + (right - left) / 2;

        // [left, ..., mid - 1, mid, mid + 1, ..., right]
        if (arr[mid] < key) {
            left = mid + 1;
        } else if (arr[mid] > key){
            right = mid - 1;
        } else {
            return mid;
        }
    }

    return -1;
}
```

## 2. First/Last occurrence

### First occurrence

Statement: given a sorted array `arr` and a target value `key`, return the index of the first occurrence of `key` in `arr`.

Interpretation: we want to find the **left-most** `key` in `arr`. Then, we would like to check if there exist another `key` on the left of our first-found `key`.

```java
public int firstOccurrence(int[] arr, int key) {
    int ans = -1;

    int left = 0, right = arr.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;

        // [left, ..., mid - 1, mid, mid + 1, ..., right]
        if (arr[mid] < key) {
            left = mid + 1;
        } else if (arr[mid] > key) {
            right = mid - 1;
        } else {
            // search the left half for the 'left-most' key
            ans = mid;
            right = mid - 1;
        }
    }

    return ans;
}
```

### Last occurrence

Similarly, we deploy the same strategy to find the last occurrence of `key`.

Statement: given a sorted array `arr` and a target value `key`, return the index of the last occurrence of `key` in `arr`.

```java
public int lastOccurrence(int[] arr, int key) {
    int ans = -1;

    int left = 0, right = arr.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;

        // [left, ..., mid - 1, mid, mid + 1, ..., right]
        if (arr[mid] < key) {
            left = mid + 1;
        } else if (arr[mid] > key) {
            right = mid - 1;
        } else {
            // search the right half for the 'right-most' key
            ans = mid;
            left = mid + 1;
        }
    }

    return ans;
}
```

## 3. Least-Greater / Greatest-Lesser

### Least-Greater

Statement: given a sorted array `arr` and a target value `key`, return the index of least element greater than `key` in `arr`.

Interpretation: we want to find the **last** element that is greater than `key`.

```java
public int leastGreater(int[] arr, int key) {
    int ans = -1;

    int left = 0, right = arr.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;

        // [left, ..., mid - 1, mid, mid + 1, ..., right]
        if (arr[mid] <= key) {
            // search for values greater than `key`
            left = mid + 1;
        } else {
            // search the left half for smaller values
            ans = mid;
            right = mid - 1;
        }
    }

    return ans;
}
```

### Greatest-Lesser

Statement: given a sorted array `arr` and a target value `key`, return the index of greatest element less than `key` in `arr`.

```java
public int leastGreater(int[] arr, int key) {
    int ans = -1;

    int left = 0, right = arr.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;

        // [left, ..., mid - 1, mid, mid + 1, ..., right]
        if (arr[mid] < key) {
            // search the right half for greater values
            ans = mid;
            left = mid + 1;
        } else {
            // search for values less than `key`
            right = mid - 1;
        }
    }

    return ans;
}
```

# Greatest Common Divisor: Euclidian Algorithm

Statement: given two integers `a` and `b`, return the greatest common divisor of `a` and `b`.

Note: *gcd(a, b, c) = gcd(a, gcd(b, c)) = gcd(gcd(a, b), c) = gcd(gcd(a, c), b).*

```java
public int gcd(int a, int b) {
    if (b == 0) {
        return a;
    }

    return gcd(b, a % b);
}
```

# Linkedlist Reverse and Its Variants

## Reverse a Linkedlist

Statement: given a linkedlist `head`, reverse the linkedlist.

```java
public ListNode reverse(ListNode head) {
    ListNode prev = null, curr = head;

    while (curr != null) {
        ListNode next = curr.next;
        curr.next = prev;
        prev = curr;
        curr = next;
    }

    return prev;
}
```

## Reverse a Linkedlist in a Range

Statement: given the head of a singly linked list and two integers `left` and `right` where `left <= right`, reverse the nodes of the list from position `left` to position `right`, and return the reversed list.

```java
public ListNode reverseBetween(ListNode head, int m, int n) {

        // Empty list
        if (head == null) {
            return null;
        }

        // Move the two pointers until they reach the proper starting point
        // in the list.
        ListNode cur = head, prev = null;
        while (m > 1) {
            prev = cur;
            cur = cur.next;
            m--;
            n--;
        }

        // The two pointers that will fix the final connections.
        ListNode con = prev, tail = cur;

        // Iteratively reverse the nodes until n becomes 0.
        ListNode third = null;
        while (n > 0) {
            third = cur.next;
            cur.next = prev;
            prev = cur;
            cur = third;
            n--;
        }

        // Adjust the final connections as explained in the algorithm
        if (con != null) {
            con.next = prev;
        } else {
            head = prev;
        }

        tail.next = cur;
        return head;
    }
```

# Merge Two Sorted LinkedLists

Statement: given two sorted linked lists `l1` and `l2`, merge them into a single sorted linked list and return it.

```java
public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
    // elegant solution
    if (list1 != null && list2 != null) {
        if (list1.val < list2.val) {
            list1.next = mergeTwoLists(list1.next, list2);
            return list1;
        } else {
            list2.next = mergeTwoLists(list1, list2.next);
            return list2;
        }
    }

    if (list1 == null) {
        return list2;
    } else {
        return list1;
    }
}
```

# Cycle Detection in a Linkedlist

## Where the cycle begins

Statement: given a linkedlist `head`, return the node where the cycle begins. If there is no cycle, return `null`.

Idea: let `slow` and `fast` be the two pointers. When they meet, let `slow` be the head and move both `slow` by one step and `fast` by two step. Denote the distance before cycle as `a`, `slow` traveled within cycle as `b`, and the length of cycle as `c`. 

![pic](https://leetcode.com/problems/linked-list-cycle-ii/Figures/142/142_cycle.drawio.png)

Then `fast` traveled `a + b + kc` (where `k` denotes the number of laps `fast` has traveled) and `slow` traveled `a + b`. Since `fast` traveled twice as `slow`, we have `a + b + kc = 2 * (a + b)`. Therefore, `a + b = kc`, i.e., `a = kc - b`. 

This implies that if we set `slow` to the head and move `slow` by one step, `slow` will arrive at the entry of the cycle after `kc - b` steps. Now, let `fast` move by **one step**, then `fast` will be at the entry of the cycle after `kc - b = (k - 1)c + (c - b)` steps.

As a result, after `slow` meets `fast`, if we move `slow` to the head and move both `slow` and `fast` by one step, they will meet at the beginning of the cycle.

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode slow = head, fast = head;

        // Move until slow meets fast
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;

            if (slow == fast) {
                break;
            }
        }

        // Check if cycle exists
        if (fast == null || fast.next == null) {
            return null;
        }

        // Reset slow to head
        slow = head;
        while (slow != fast) {
            fast = fast.next;
            slow = slow.next;
        }

        return slow;
    }
}
```

# Prefix Sum

## Range Addition

Statement: you are given an integer `length` and an array `updates` where `updates[i] = [startIdx_i, endIdx_i, inc_i]`.

You have an array `arr` of length `length` with all zeros, and you have some operation to apply on `arr`. In the ith operation, you should increment all the elements `arr[startIdx_i], arr[startIdx_i + 1], ..., arr[endIdx_i]` by `inc_i`.

Return `arr` after applying all the updates.

Idea: mark the range by two ends. For example, if `updates[i] = [1, 3, 2]`, then `arr[1]` and `arr[3 + 1]` should be incremented by `2`. Therefore, we can mark the range by `2` and `-2` at the two ends of the range.

```java
public int[] getModifiedArray(int length, int[][] updates) {
    int[] arr = new int[length];

    for (int[] update : updates) {
        int start = update[0], end = update[1], inc = update[2];
        
        arr[start] = arr[start] + inc;
        if (end < length - 1) {
            arr[end + 1] = arr[end + 1] - inc;
        }
    }

    for (int i = 1; i < length; i++) {
        arr[i] += arr[i - 1];
    }

    return arr;
}
```

## Subarray Sums Divisible by K

Statement: given an array `nums` of integers, return the number of (contiguous, non-empty) subarrays that have a sum divisible by `k`.

Idea: This problem can be treated as a variant of the ordinary prefix sum problems, as it utilizes **Prefix Modulo Sum**. 

**Main idea**:

1. If `prefixMod[i] == prefixMod[j]`, then `nums[i] + ... + nums[j]` is divisible by `k`.

2. One may derive all subarray sum modulo from `nums[0] + ... + nums[i]` by subtracting `nums[0] + ... + nums[j]` from `nums[0] + ... + nums[i]` for `j < i`.

**Goal**:

1. Find all 0-index subarray sum modulo.

2. Count the frequency of each subarray sum modulo.

**Implementation**:

1. Use `prefixMod` to track current subarray sum modulo by `prefixMod = (prefixMod + n % k + k) % k`.

2. Use `modCount[k]` to track the frequency of each subarray sum modulo.

**Caveat**:

1. `prefixMod` may be negative, so we need to add `k` to it before taking modulo.

2. `modCount` should be initialized as `modCount[0] = 1` as subarraies with 0 sum modulo must be divisible by `k`.

```java
public int subarraysDivByK(int[] nums, int k) {
    int prefixMod = 0, result = 0;

    int[] modCount = new int[k];
    modCount[0] = 1;

    for (int n : nums) {
        prefixMod = (prefixMod + n % k + k) % k;
        result += modCount[prefixMod];
        modCount[prefixMod] += 1;
    }

    return result;
}
```

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

This DP structure can aplly to all buy & sell stock problem with small modification.

# Backtracking

Backtracking framework:

```python
result = []
def backtrack(路径, 选择列表):
    if 满足结束条件:
        result.add(路径)
        return
    
    for 选择 in 选择列表:
        做选择
        backtrack(路径, 选择列表)
        撤销选择

    # Or more specifically
    for 选择 in 选择列表:
        # 做选择
        将该选择从选择列表移除
        路径.add(选择)
        backtrack(路径, 选择列表)
        # 撤销选择
        路径.remove(选择)
        将该选择再加入选择列表
```

## Problems with backtracking

| Link  | Note |
|---|---|
| [Leedcode 46](https://leetcode.com/problems/permutations/description/) | All the permutations |
| [Leetcode 51](https://leetcode.com/problems/n-queens/description/) | N-Queens with notes in the following |
| [Leetcode 2305](https://leetcode.com/problems/fair-distribution-of-cookies/description/) | Special end case at 0 values |
|   |   |
|   |   |

### N-Queens

Problem Statement: [Leetcode 51](https://leetcode.com/problems/n-queens/description/).

**Method**:

Use backtracking to enumerate every possibility of placing queens on the board. Problems encountered:

1. How to track the board state?

    Unlike other backtracking problems, we cannot use a boolean array to track the state of the board. Because we need to check whether the queen is under attack, we need to know the position of the queen. So we need to use a 2D array `char[][] board` to track the board state.

2. How to check whether a position is valid?

    We need to check whether there is a queen on the same row, same column, same diagonal line, or same anti-diagonal line. So we need to use 4 boolean arrays to track the state of each row, column, diagonal line, and anti-diagonal line. 

    1. Row-collision has been handled by the backtracking framework as we process the board row by row.

    2. Column-collision: `boolean[] usedCols`.

    3. Diagonal-collision: `boolean[] usedDiagonals`. Note that in a grid, the diagonal line number can be computed by `row - col`. But `row - col` can be negative, so we need to add an offset to make sure the diagonal line number is non-negative. Thus, the diagonal line number is `row - col + n - 1` for coordinate at `(row, col)`.

    4. Anti-diagonal-collision: `boolean[] usedAntiDiagonals`. Similarly, the antidiagnoal line number is `row + col`.

**Implementation**:

```java
class Solution {
    private List<List<String>> result;
    private int n;

    public List<List<String>> solveNQueens(int n) {
        result = new LinkedList<>();
        this.n = n;

        char[][] board = new char[n][n];
        boolean[] usedCols = new boolean[n];
        // # of diagonal lines is 2 * n - 1
        boolean[] usedDiag = new boolean[2 * n - 1];
        boolean[] usedAntiDiag = new boolean[2 * n - 1];

        for (char[] row : board) {
            Arrays.fill(row, '.');
        }

        backtrack(board, usedCols, usedDiag, usedAntiDiag, 0);

        return result;
    }

    private void backtrack(char[][] board, boolean[] usedCols, boolean[] usedDiag,
                           boolean[] usedAntiDiag, int r) {
        if (r == n) {
            result.add(boardToList(board));
            return;
        }

        for (int c = 0; c < n; c++) {
            if (!isValid(usedCols, usedDiag, usedAntiDiag, r, c)) {
                continue;
            }

            usedCols[c] = true;
            usedDiag[getDiagNum(r, c)] = true;
            usedAntiDiag[getAntiDiagNum(r, c)] = true;
            board[r][c] = 'Q';
            backtrack(board, usedCols, usedDiag, usedAntiDiag, r + 1);
            board[r][c] = '.';
            usedAntiDiag[getAntiDiagNum(r, c)] = false;
            usedDiag[getDiagNum(r, c)] = false;
            usedCols[c] = false;
        }
    }

    private boolean isValid(boolean[] usedCols, boolean[]usedDiag, boolean[] usedAntiDiag,
                            int r, int c) {
        if (usedCols[c] || usedDiag[getDiagNum(r, c)] || usedAntiDiag[getAntiDiagNum(r, c)]) {
            return false;
        }
        return true;
    }

    private int getDiagNum(int r, int c) {
        return n + r - c - 1;
    }

    private int getAntiDiagNum(int r, int c) {
        return r + c;
    }

    private List<String> boardToList(char[][] board) {
        List<String> result = new ArrayList<>();
        for (char[] row : board) {
            result.add(new String(row));
        }
        return result;
    }
}
```
