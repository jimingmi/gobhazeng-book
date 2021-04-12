# 大数据查询topN

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        if (nums.length == 0 || k < 1) {
            return -1;
        }

        PriorityQueue<Integer> minHeap = new PriorityQueue<Integer>(); // Java 默认为小顶堆

        for (int i = 0; i < k; ++i) {
            minHeap.add(nums[i]);
        }

        for (int i = k; i < nums.length; ++i) {
            if (nums[i] >= minHeap.peek()) {
                // 若 nums[i] 大于等于堆顶元素，弹出堆顶元素并将 nums[i] 入堆
                minHeap.poll();
                minHeap.add(nums[i]);
            }
        }

        return minHeap.peek();
    }
}
```