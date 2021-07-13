# 第四周

## 355. 设计推特
利用哈希表+优先队列：
```java
class Twitter {
    int order = 0;
    Map<Integer, Set<Integer>> likeMap;
    Map<Integer, List<int[]>> tweetMap;

    /** Initialize your data structure here. */
    public Twitter() {
        likeMap = new HashMap<>();
        tweetMap = new HashMap<>();
    }
    
    /** Compose a new tweet. */
    public void postTweet(int userId, int tweetId) {
        List<int[]> list = tweetMap.getOrDefault(userId, new ArrayList<int[]>());
        list.add(new int[]{tweetId, userId, order, -1});
        order++;
        tweetMap.put(userId, list);
    }
    
    /** Retrieve the 10 most recent tweet ids in the user's news feed. Each item in the news feed must be posted by users who the user followed or by the user herself. Tweets must be ordered from most recent to least recent. */
    public List<Integer> getNewsFeed(int userId) {
        Set<Integer> set = likeMap.getOrDefault(userId, new HashSet<Integer>());
        Set<Integer> users = new HashSet<>(set);
        users.add(userId);
        List<Integer> res = new ArrayList<>();
        // System.out.println(userId + ": users: "+ users);
        PriorityQueue<int[]> q = new PriorityQueue<>((o1, o2) -> o2[2] - o1[2]);

        for (int u : users) {
            List<int[]> list = tweetMap.get(u);
            if (list != null && list.size() > 0) {
                int index = list.size()-1;
                int[] tmp = list.get(index);
                tmp[3] = index;
                q.offer(tmp);
            }
        }

        for (int i = 0; i < 10; i++) {
            if (!q.isEmpty()) {
                int[] t1 = q.poll();
                int index = t1[3]-1;
                res.add(t1[0]);
                List<int[]> list = tweetMap.get(t1[1]);
                if (list != null && list.size() > 0 && index >= 0) {
                    int[] t2 = list.get(index);
                    t2[3] = index;
                    q.offer(t2);
                }
            }
        }
        // Collections.reverse(res);
        return res;
    }
    
    /** Follower follows a followee. If the operation is invalid, it should be a no-op. */
    public void follow(int followerId, int followeeId) {
        Set<Integer> set = likeMap.getOrDefault(followerId, new HashSet<Integer>());
        set.add(followeeId);
        likeMap.put(followerId, set);
    }
    
    /** Follower unfollows a followee. If the operation is invalid, it should be a no-op. */
    public void unfollow(int followerId, int followeeId) {
        Set<Integer> set = likeMap.getOrDefault(followerId, new HashSet<Integer>());
        set.remove(followeeId);
        likeMap.put(followerId, set);

    }
}
```

## 295. 数据流的中位数
大顶堆+小顶堆：
```java
class MedianFinder {
    PriorityQueue<Integer> right = new PriorityQueue<>((o1, o2) -> o1 - o2);
    PriorityQueue<Integer> left = new PriorityQueue<>((o1, o2) -> o2 - o1);

    public MedianFinder() {

    }
    
    public void addNum(int num) {
        int len = left.size();
        int rlen = right.size();

        if (len == rlen) {
            if (len == 0) {
                left.offer(num);
            } else {
                int rmin = right.peek();
                if (num > rmin) {
                    right.poll();
                    left.offer(rmin);
                    right.offer(num);
                } else {
                    left.offer(num);
                }
            }
        } else {
            // len != rlen
            int mid = left.peek();
            if (num >= mid) {
                right.offer(num);
            } else {
                left.poll();
                right.offer(mid);
                left.offer(num);
            }
        }
    }
    
    public double findMedian() {
        // System.out.println("left:"+left);
        // System.out.println("right:"+right);
        int len = left.size();
        int rlen = right.size();

        if (len == rlen) {
            return (double) (left.peek() + right.peek()) / 2;
        }

        return left.peek();
    }
}
```

## 154. 寻找旋转排序数组中的最小值 II
```java
class Solution {
    public int findMin(int[] nums) {
        int n = nums.length;
        int l = 0, r = n-1;
        while (l < r) {
            int mid = l + ((r - l) >> 1);
            if (nums[l] == nums[mid] && nums[r] == nums[mid]) {
                l++;
                continue;
            }
            if (nums[mid] > nums[r]) {
                l = mid+1;
            } else {
                r = mid;
            }
        }
        return nums[r];
    }
}
```