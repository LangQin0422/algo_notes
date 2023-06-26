# Binary Search and Its Variants

1. Contains
2. First/Last occurrence
3. Least geater/Greatest lesser

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

Idea: let `slow` and `fast` be the two pointers. Let `slow` be the head and move both `slow` by one step and `fast` by two step. When they meet, denote the distance before cycle as `a`, `slow` traveled within cycle as `b`, and the length of cycle as `c`. 

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
