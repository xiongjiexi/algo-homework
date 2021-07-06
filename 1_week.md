# 第一周

## 66. Plus One（加一）
```java
public int[] plusOne(int[] d) {
    int n = d.length;
    int up = 1;
    for (int i = n-1; i > -1; i--) {
        int tmp = d[i] + up;
        if (tmp >= 10) {
            up = 1;
        } else {
            up = 0;
        }
        d[i] = tmp % 10;
    }
    if (up > 0) {
        int[] res = new int[n+1];
        res[0] = 1;
        System.arraycopy(d, 0, res, 1, n);
        return res;
    }
    return d;
}
```

```go
func plusOne(digits []int) []int {
    n := len(digits)
    d := digits[0:n]
    plus := 1
    for i := n-1; i >= 0; i-- {
        d[i] = d[i] +  plus
        if d[i] > 9 {
            plus = 1
            d[i] = 0
        } else {
            plus = 0
            break
        }
    }
    if plus == 1 {
        d[0] = 0
        d = append([]int{1}, d...)
    }
    return d
}
```

## 21. Merge Two Sorted Lists（合并两个有序链表）
```java
class Solution {
    public ListNode mergeTwoLists(ListNode a, ListNode b) {
        ListNode head = new ListNode(0);
        ListNode node = head;
        while (a != null && b != null) {
            if (a.val <= b.val) {
                node.next = a;
                a = a.next;
            } else {
                node.next = b;
                b = b.next;
            }
            node = node.next;
        }

        while (a != null) {
            node.next = a;
            a = a.next;
            node = node.next;
        }

        while (b != null) {
            node.next = b;
            b = b.next;
            node = node.next;
        }
        return head.next;
    }
}
```

```go
func mergeTwoLists(l1 *ListNode, l2 *ListNode) *ListNode {
    head := new(ListNode)
    var res *ListNode = head
    for l1 != nil && l2 != nil {
        if l1.Val < l2.Val {
            res.Next = l1
            l1 = l1.Next
            res = res.Next
        } else {
            res.Next = l2
            l2 = l2.Next
            res = res.Next
        }
    }
    
    var l3 *ListNode
    if l1 != nil {
        l3 = l1
    }
    if l2 != nil {
        l3 = l2
    }
    
    for l3 != nil {
        res.Next = l3
        l3 = l3.Next
        res = res.Next
    }
    
    return head.Next
}
```

## 641. 设计循环双端队列
使用双向链表实现：
```java
class MyCircularDeque {
    LinkedList<Node> list = new LinkedList<>();
    int max;
    int size;
    
    class Node {
        int val;
        public Node(int val) {
            this.val = val;
        }
    }
    
    public MyCircularDeque(int k) {
        max = k;
        size = 0;
    }
    
    public boolean insertFront(int value) {
        if (size >= max) return false;
        
        list.addFirst(new Node(value));
        size++;
        return true;
    }
    
    public boolean insertLast(int value) {
        if (size >= max) return false;
        
        list.addLast(new Node(value));
        size++;
        return true;
    }
    
    public boolean deleteFront() {
        if (size <= 0) return false;
        
        list.removeFirst();
        size--;
        return true;
    }
    
    public boolean deleteLast() {
        if (size <= 0) return false;
        
        list.removeLast();
        size--;
        return true;
    }
    
    public int getFront() {
        if (size <= 0) return -1;
        
        return list.peekFirst().val;
    }
    
    public int getRear() {
        if (size <= 0) return -1;
        
        return list.peekLast().val;
    }
    
    public boolean isEmpty() {
        return size <= 0;
    }
    
    public boolean isFull() {
        return size >= max;
    }
}
```

## 560. Subarray Sum Equals K（和为K的子数组）

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        // presum[0] = 0;
        int[] presum = new int[nums.length+1];
        for (int i = 1; i < presum.length; i++) {
            presum[i] = presum[i-1] + nums[i-1];
        }
        // System.out.println("presum:"+ Arrays.toString(presum));
        int cnt = 0;
        HashMap<Integer, Integer> map = new HashMap<>();
        // 如果presum[i] - k==0，自然cnt要+1
        map.put(0, 1);
        for (int i = 1; i < presum.length; i++) {
            // System.out.println("presum[i] - k: "+(presum[i] - k));
            if (map.containsKey(presum[i] - k)) {
                cnt += map.get(presum[i] - k);
            }
            map.put(presum[i], map.getOrDefault(presum[i], 0) + 1);
        }

        return cnt;
    }
}
```
优化后（presum不需要使用数组存储）：
```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        int presum = 0;
        int cnt = 0;
        HashMap<Integer, Integer> map = new HashMap<>();
        // 如果presum-k==0，自然cnt要+1
        map.put(0, 1);
        for (int i = 0; i < nums.length; i++) {
            presum += nums[i];
            if (map.containsKey(presum - k)) {
                cnt += map.get(presum - k);
            }
            map.put(presum, map.getOrDefault(presum, 0) + 1);
        }

        return cnt;
    }
}
```

go解法：
```go
func subarraySum(nums []int, k int) int {
    n := len(nums)
    pre := make([]int, n)
    pre[0] = nums[0]
    
    for i := 1; i < n; i++ {
        pre[i] = pre[i-1] + nums[i]
    }
    
    var m = make(map[int]int)
    m[0] = 1
    cnt := 0
    for i := 0; i < n; i++ {
        val, ok := m[pre[i]-k]
        if ok {
            cnt += val
        }
        v, exist := m[pre[i]]
        if exist {
            m[pre[i]] = v+1
        } else {
            m[pre[i]] = 1
        }
    }
    return cnt
}
```



## 双指针、滑动窗口

### 167. 两数之和 II - 输入有序数组

```

给定一个已按照 升序排列  的整数数组 numbers ，请你从数组中找出两个数满足相加之和等于目标数 target 。

函数应该以长度为 2 的整数数组的形式返回这两个数的下标值。numbers 的下标 从 1 开始计数 ，所以答案数组应当满足 1 <= answer[0] < answer[1] <= numbers.length 。

你可以假设每个输入只对应唯一的答案，而且你不可以重复使用相同的元素。

 
示例 1：

输入：numbers = [2,7,11,15], target = 9
输出：[1,2]
解释：2 与 7 之和等于目标数 9 。因此 index1 = 1, index2 = 2 。
示例 2：

输入：numbers = [2,3,4], target = 6
输出：[1,3]
示例 3：

输入：numbers = [-1,0], target = -1
输出：[1,2]
```



双指针：
```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int n = numbers.length;
        int[] num = numbers;
        int l = 0, r = n-1;

        while (l < r) {
            int sum = num[l]+num[r];
            if (sum == target) return new int[]{l+1, r+1};

            if (sum > target) r--;
            if (sum < target) l++;
        }

        return null;
    }
}
```
二分法：
```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int[] nums = numbers;
        int n = nums.length;
        for (int i = 0; i < n-1; i++) {
            int res = Arrays.binarySearch(nums, i+1, n, target-nums[i]);
            if (res >= 0) return new int[]{i+1, res+1};
        }
        return null;
    }
}
```

## 单调栈、单调队列

### 84. 柱状图中最大的矩形

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        int[] hs = new int[heights.length+1];
        hs[hs.length-1] = 0;
        for (int i = 0; i < heights.length; i++) {
            hs[i] = heights[i];
        }
        Stack<Integer> stack = new Stack<>();
        int max = 0;
        for (int i = 0; i < hs.length; i++) {
            while (!stack.empty() && hs[stack.peek()] > hs[i]) {
                int top = stack.pop();
                int s = hs[top]*(i-(stack.empty()?0:stack.peek()+1));
                max = Math.max(max, s);
            }
            stack.push(i);
        }
        return max;
    }
}
```