# LeetCode 57 Insert Interval

### Question

[Origin](https://leetcode.com/problems/insert-interval/description/)

You are given an array of non-overlapping intervals `intervals` where `intervals[i] = [starti, endi]` represent the start and the end of the `ith` interval and `intervals` is sorted in ascending order by `starti`. You are also given an interval `newInterval = [start, end]` that represents the start and end of another interval.

Insert `newInterval` into `intervals` such that `intervals` is still sorted in ascending order by `starti` and `intervals` still does not have any overlapping intervals (merge overlapping intervals if necessary).

Return `intervals` after the insertion.

Example 1:

    Input: intervals = [[1,3],[6,9]], newInterval = [2,5]
    Output: [[1,5],[6,9]]

Example 2:

    Input: intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
    Output: [[1,2],[3,10],[12,16]]
    Explanation: Because the new interval [4,8] overlaps with [3,5],[6,7],[8,10].
    
### Answer

```python
class Solution:
    def insert(self, intervals: List[List[int]], newInterval: List[int]) -> List[List[int]]:
        heap = intervals
        heapq.heapify(intervals)
        heapq.heappush(heap, newInterval)
        stack = [  ]
        while len(heap):
            out_s, out_e = heapq.heappop(heap)
            if len(stack) and stack[-1][1] >= out_s:
                stack_s, stack_e = stack.pop()
                stack.append( [stack_s, max(stack_e, out_e)] )
            else:
                stack.append( [out_s, out_e] )
        return stack
```

### Explaination

python [heapq](https://desolve.medium.com/從零開始學python-24-資料結構模組heapq-除了前幾名以外-在座的各位都是垃圾-914ad351b3b4)

* Just used a minHeap to insert the interval.
* What MinHeap does for us is that it sorts are values by starting position unless there is a tie. In case of a tie it used the ending position.
* Now we pop all these values one by one and insert them in a stack.
* Before insertion we check, a> if stack has atleast one element already and b> if last element of stack overlaps with the element we are about to insert.
* If there is an overlap we just pop the last element from the stack and insert the merged interval instead.
* The merged interval will always start from the start of element that we popped from stack because of heap's sorting. But end element could be either stack's pop or the element we are about to insert, whichever is greater/farther.
