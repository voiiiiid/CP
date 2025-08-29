# Sparse Table

If we have an array of size n and there are queries of size q and we have to find the minimum or maximum in the given query range.

### Brute Force

The brute force appraoch would be iterating all over the queries. Find the start and end iterate all over from start to end in the array and find the answer.

#### Time Complexity - worst case O(n \* q)

### Optimal Approach - Sparse Table

Sparse Table is a data structure, that allows answering range queries. It can answer most range queries in  O(log n) , but its true power is answering range minimum queries (or equivalent range maximum queries). For those queries it can compute the answer in  O(1)  time.

The only drawback of this data structure is, that it can only be used on immutable arrays. This means, that the array cannot be changed between two queries. If any element in the array changes, the complete data structure has to be recomputed or we can use something like Segment Trees.

In this we precompute the result in O(N \* log N) time and then find the answer for each query in O(1) time.

### How ?

suppose we have these indices

0 1 2 3 4 5 6 7 8 9 10

and we have to find the minimum from 0 to 7 but we already precomputed the result for every subarray of length 4 it means we have answer for 0 to 3 and then 4 to 7. Isn't it would be easy to just find the answer for 0 to 7 and this same works for any place and for any power of 2.

we can write this as

```
m[0][power = 3, length = 8] = min(m[0][power = 2, length = 4], m[4][power = 2, length = 4])
```

means starting from 0 till length of 2 to the power 3 will get us to 8
can be solved by min of
0 to 3 and then 4 to 7

in simple terms we can write it like this

```
m[i][j] = min(m[i][j-1], m[i+ 2\*\*(j-1)][j-1])
```

here 2 \*\* (j-1) can be written as 1 << (j-1) it works the same

```
m[i][j] = min(m[i][j-1], m[i+ 1 << (j-1)][j-1])
```

means starting from i if we want to go jth power of 2 then we can go there by
minimum of starting from i to just less than the previous jth power of 2 and then i + 2 to thw power j-1 to again previous jth power of 2.

and we will fill this for every power of j starting from 1 to log N
so we can write it like this

```
for j in range(1, logN-1):
    for i in range(0, N-(1 << j)+1):
        m[i][j] = min(m[i][j-1], m[i+ 1 << (j-1)][j-1])
```

so now suppose we have a query from [L, R] then

```
total length = R - L + 1
j = log2(len)
return min(m[L][j], m[R-(2**j)+1][j])
```

and now the actual code

```
import math

def build_sparse_table(arr):
    n = len(arr)
    k = int(math.log2(n)) + 1
    m = [[0] * k for _ in range(n)]

    # initialize interval length 1
    for i in range(n):
        m[i][0] = arr[i]

    # compute values for intervals with length 2^j
    for j in range(1, k):
        length = 1 << j //2 to the power of j
        half = length >> 1 // 2 to the power of j - 1
        for i in range(n - length + 1):
            m[i][j] = min(m[i][j-1], m[i + half][j-1])

    return m

def query(L, R, m):
    length = R - L + 1
    j = int(math.log2(length))
    return min(m[L][j], m[R - (1 << j) + 1][j])

```
