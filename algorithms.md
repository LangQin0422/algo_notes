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

