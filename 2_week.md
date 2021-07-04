# 第二周

## 146. LRU 缓存机制
使用哈希表+双向链表实现：
```java
class LRUCache {
    int size = 0;
    int capacity;
    Map<Integer, Node> map = new HashMap<>();
    Node first;
    Node last;

    private class Node {
        Node pre;
        Node next;
        int val;
        int key;
        Node() {}
        Node(int key, int value) {
            this.key = key;
            this.val = value;
        }
        public String toString() {
            return "key:"+key+"; val:"+val;
        }
    }

    public LRUCache(int capacity) {
        this.capacity = capacity;
        first = new Node(-1, -1);
        last = new Node(-99, -99);
        first.next = last;
        last.pre = first;
    }

    public int get(int key) {
        Node res = map.get(key);
        if (res != null) {
            Node old_pre = res.pre;
            Node old_next = res.next;
            connect(old_pre, old_next);
            
            Node old_first = first.next;
            connect(first, res);
            connect(res, old_first);
            
            // printFirst();
            // printLast();
            return res.val;
        }
        return -1;
    }

    public void put(int key, int value) {
        Node n = map.get(key);
        if (n == null) {
            n = new Node(key, value);
            Node old = first.next;
            connect(first, n);
            connect(n, old);
            if (size >= capacity) {
                Node delete = last.pre;
                Node end = last.pre.pre;
                connect(end, last);
                map.remove(delete.key);
            } else {
                size++;
            }
        } else {
            int rundent = get(key);
            n.val = value;
        }
        // printFirst();
        // printLast();
        map.put(key, n);
    }
    
    public void connect(Node a, Node b) {
        a.next = b;
        b.pre = a;
    }
    
    public void printFirst() {
        Node tmp = first.next;
        while (tmp != null) {
            System.out.print(tmp.val+", ");
            tmp = tmp.next;
        }
        System.out.println("first end");
    }
    
    public void printLast() {
        Node tmp = last.pre;
        while (tmp != null) {
            System.out.print(tmp.val+", ");
            tmp = tmp.pre;
        }
        System.out.println("last end");
    }
}
```

## 811. 子域名访问计数
哈希表：
```java
class Solution {
    public List<String> subdomainVisits(String[] cpdomains) {
        String[] cd = cpdomains;
        HashMap<String, Integer> map = new HashMap<>();
        
        for (String s : cd) {
            String[] arr = s.split(" ");
            String[] dm = arr[1].split("\\.");

            String dd = dm[dm.length-1];
            map.put(dd, map.getOrDefault(dd, 0)+Integer.valueOf(arr[0]));
            for (int i = dm.length-2; i >= 0; i--) {
                dd = dm[i] + "." + dd;
                map.put(dd, map.getOrDefault(dd, 0)+Integer.valueOf(arr[0]));
            }
        }
        List<String> res = new ArrayList<>();
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            res.add(entry.getValue() + " " + entry.getKey());
        }
        
        return res;
    }
}
```


## 697. 数组的度
哈希表：
```java
class Solution {
    public int findShortestSubArray(int[] nums) {
        int max = -1;
        int min = 0x3f3f3f3f;
        HashMap<Integer, int[]> map = new HashMap<>();
        
        for (int i = 0 ; i < nums.length; i++) {
            int[] cur = map.get(nums[i]);
            if (cur == null){
                cur = new int[]{1, i, i};
            } else {
                cur[0]++;
                cur[2] = i;
            }
            map.put(nums[i], cur);
            
            if ((cur[0] > max) || (cur[0] == max && (cur[2]-cur[1]) <= min)) {
                max = cur[0];
                min = cur[2]-cur[1]+1;
            }
        }
        return min;
    }
}

```

## 1074. 元素和为目标值的子矩阵数量
同363题，为它的简化版，使用二维前缀和+哈希表：
```java
class Solution {
    public int numSubmatrixSumTarget(int[][] matrix, int target) {
        int[][] ma = matrix;
        int n = ma.length, m = ma[0].length;
        int[][] pre = new int[n+1][m+1];
        for (int r = 1; r <= n; r++) {
            for (int c = 1; c <= m; c++) {
                pre[r][c] = pre[r-1][c] + pre[r][c-1] - pre[r-1][c-1] + ma[r-1][c-1];
            }
        }
        // System.out.println(Arrays.deepToString(pre));
        int cnt = 0;
        for (int top = 1; top <= n; top++) {
            for (int bot = top; bot <= n; bot++) {
                Map<Integer, Integer> map = new HashMap<>();
                for (int c = 1; c <= m; c++) {
                    int right = pre[bot][c] - pre[top-1][c];
                    if (right == target) cnt++;
                    if (map.containsKey(right-target)) cnt += map.get(right-target);
                    map.put(right, map.getOrDefault(right, 0)+1);
                }
            }
        }
        return cnt;
    }
}
```