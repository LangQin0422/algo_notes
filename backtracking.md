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
| [Leedcode 46](https://leetcode.com/problems/permutations/) | All the permutations |
| [Leetcode 51](https://leetcode.com/problems/n-queens/) | N-Queens with notes in the following |
| [Leetcode 52](https://leetcode.com/problems/n-queens-ii/) | N-Queens II |
| [Leetcode 2305](https://leetcode.com/problems/fair-distribution-of-cookies/) | Special end case at 0 values |

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
,             }

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

## Permutations/Combinations/Subsets with BackTracking

In general, there are three fundamental types of forms:

1. **No duplicate** elements in the input array; **No duplicate** elements in the output list. (No-No Duplicate)

2. **Duplicate** elements in the input array; **No duplicate** elements in the output list. (Yes-No Duplicate)

3. **No Duplicate** elements in the input array; **Duplicate** elements in the output list. (No-Yes Duplicate)

### No-No Duplicate

* **[Combination/Subset]** Use a parameter `start` to track the next position to be considered.

* **[Permutation]** Use a **boolean array** to track the state of each element in the input array.

Core framework:

```java
/* 组合/子集问题回溯算法框架 */
void backtrack(int[] nums, int start) {
    // 回溯算法标准框架
    for (int i = start; i < nums.length; i++) {
        // 做选择
        track.addLast(nums[i]);
        // 注意参数
        backtrack(nums, i + 1);
        // 撤销选择
        track.removeLast();
    }
}

/* 排列问题回溯算法框架 */
void backtrack(int[] nums) {
    for (int i = 0; i < nums.length; i++) {
        // 剪枝逻辑
        if (used[i]) {
            continue;
        }
        // 做选择
        used[i] = true;
        track.addLast(nums[i]);

        backtrack(nums);
        // 撤销选择
        track.removeLast();
        used[i] = false;
    }
}
```

### Yes-No Duplicate

* **[Combinatino/Subset/Permutation/]** Sort the input array to skip the same elements with `nums[i] == nums[i - 1]`.

* **[Permutation]** Maintain the relative order of duplicate elements with `nums[i] == nums[i - 1] && !used[i - 1]`.

> 标准全排列算法之所以出现重复，是因为把*相同元素形成的排列序列视为不同的序列*，但实际上它们应该是相同的；而如果固定相同元素形成的序列顺序，当然就避免了重复。

Core framework:

```java
Arrays.sort(nums);

/* 组合/子集问题回溯算法框架 */
void backtrack(int[] nums, int start) {
    // 回溯算法标准框架
    for (int i = start; i < nums.length; i++) {
        // 剪枝逻辑，跳过值相同的相邻树枝
        if (i > start && nums[i] == nums[i - 1]) {
            continue;
        }
        // 做选择
        track.addLast(nums[i]);
        // 注意参数
        backtrack(nums, i + 1);
        // 撤销选择
        track.removeLast();
    }
}


Arrays.sort(nums);

/* 排列问题回溯算法框架 */
void backtrack(int[] nums) {
    for (int i = 0; i < nums.length; i++) {
        // 剪枝逻辑
        if (used[i]) {
            continue;
        }
        // 剪枝逻辑，固定相同的元素在排列中的相对位置
        if (i > 0 && nums[i] == nums[i - 1] && !used[i - 1]) {
            continue;
        }
        // 做选择
        used[i] = true;
        track.addLast(nums[i]);

        backtrack(nums);
        // 撤销选择
        track.removeLast();
        used[i] = false;
    }
}
```

### No-Yes Duplicate

* **[Combination/Subset]** Set `start` of the next recursion the same as `i` to ensure duplication.

Core framework:

```java
/* 组合/子集问题回溯算法框架 */
void backtrack(int[] nums, int start) {
    // 回溯算法标准框架
    for (int i = start; i < nums.length; i++) {
        // 做选择
        track.addLast(nums[i]);
        // 注意参数
        backtrack(nums, i);
        // 撤销选择
        track.removeLast();
    }
}

/* 排列问题回溯算法框架 */
void backtrack(int[] nums) {
    for (int i = 0; i < nums.length; i++) {
        // 做选择
        track.addLast(nums[i]);
        backtrack(nums);
        // 撤销选择
        track.removeLast();
    }
}
```
