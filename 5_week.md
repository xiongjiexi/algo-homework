# 第五周



## 1011. 在 D 天内送达包裹的能力

```java
class Solution {
    public int shipWithinDays(int[] ws, int d) {
        int max = 0, sum = 0;
        for (int w : ws) {
            max = Math.max(max, w);
            sum += w;
        }
        int l = max, r = sum;
        while (l < r) {
            int mid = l + r >> 1;
            if (check(ws, mid, d)) {
                r = mid;
            } else {
                l = mid + 1;
            }
        }
        return r;
    }

    // 贪心 ws 重量数组  t 运载能力 d 天数
    boolean check(int[] ws, int t, int d) {
        // wi 重量数组下标
        int wi = 0;
        for (int i = 0; i < d; i++) {
            for (int sum = t; wi < ws.length && sum - ws[wi] >= 0; ) {
                sum -= ws[wi++];
            }
        }
        // d 天走完，是否走完ws重量数组
        return wi == ws.length;
    }
}
```


## 911. 在线选举
```java
class TopVotedCandidate {
    TreeMap<Integer, Integer> tree = new TreeMap<>();

    public TopVotedCandidate(int[] persons, int[] times) {
        int n = persons.length;
        Map<Integer, Integer> cntMap = new HashMap<>();
        int maxCnt = 0;
        int maxP = -1;
        for (int i = 0; i < n; i++) {
            int p = persons[i];
            int c = cntMap.getOrDefault(p, 0)+1;
            cntMap.put(p, c);

            if (c >= maxCnt) {
                maxP = p;
                maxCnt = c;
            }

            tree.put(times[i], maxP);
        }
    }
    
    public int q(int t) {
        return tree.floorEntry(t).getValue();
    }
}
```

## 875. 爱吃香蕉的珂珂
二分法：
```java
class Solution {
    public int minEatingSpeed(int[] piles, int h) {
        int l = 0, r = 0;
        for (int p : piles) {
            r = Math.max(r, p);
        }

        while (l < r) {
            int mid = l + ((r-l)>>1);
            // System.out.println("l:"+l +"r:"+r+"mid:"+mid);
            int hh = 0;
            for (int p : piles) {
                double div = ((double)p)/mid;
                if (div >= 1d) {
                    hh += (int) Math.ceil(div);
                } else {
                    hh++;
                }
                // System.out.println("div:"+Math.ceil(div)+"; hh:"+hh);
            }
            // System.out.println("hh:"+hh);
            if (hh <= h) {
                r = mid;
            } else {
                l = mid+1;
            }
        }

        return r;
    }
}
```

## 56. 合并区间
```java
class Solution {
    public int[][] merge(int[][] intervals) {
        int[][] it = intervals;
        Arrays.sort(it, (a, b) -> a[0]-b[0]);
        int i = 1, end = 0;
        while (i < it.length) {
            if (it[end][1] >= it[i][0]) {
                it[end][1] = it[end][1] > it[i][1] ? it[end][1] : it[i][1];
            } else {
                end++;
                it[end] = it[i];
            }
            i++;
        }
        return Arrays.copyOf(it, end+1);
    }
}
```


## 322. Coin Change（零钱兑换）
```java
public class Solution {
    public int coinChange(int[] coins, int amount) {
        int n = coins.length;
        int[] dp = new int[amount+1];
        Arrays.fill(dp, amount+1);
        dp[0] = 0;
        // i is amount
        for (int i = 1; i <= amount; i++) {
            for (int j = 0; j < n; j++) {
                if (i-coins[j] >= 0) {
                    dp[i] = Math.min(dp[i], dp[i-coins[j]]+1);
                }
            }
        }
        if (dp[amount] == amount+1){
            return -1;
        }
        return dp[amount];
    }
}
```

```java
// 超时 @jesse
class Solution {
    // dp+贪心
    // [10,7,1] 15 => 10+1+1+1+1 | 7+7+1
    // [10,8,7,1] 25 => 10+10+1+1... | 10+8+7
    // 递归树
    // 剪枝：1.同level下后续节点的子节点是从同值开始遍历 2.记录最小深度，如果深度大于前面最小深度，即此时最小深度为最少硬币个数
    int min = 999999;
    public int coinChange(int[] coins, int amount) {
        if (amount <= 0)
            return 0;
        coins = IntStream.of(coins).boxed().sorted(Comparator.reverseOrder()).mapToInt(Integer::intValue).toArray();
//        System.out.println(Arrays.toString(coins));
        for (int i = 0; i < coins.length; i++) {
            recursive(coins, i, amount, 1);
        }
        if (min == 999999) return -1;
        return min;
    }

    private void recursive(int[] cs, int i, int remainder, int deep) {
        if (i >= cs.length) return;
        if (cs[i] > remainder) {
            return;
        }
        remainder -= cs[i];
//        System.out.println("remainder:"+remainder+";cs[i]:"+cs[i]);
        if (remainder <= 0) {
//            System.out.println("deep:"+deep+";min:"+min);
            if (deep < min) {
                min = deep;
            }
            return;
        }
        for (int j = i; j < cs.length; j++) {
            recursive(cs, j, remainder, deep+1);
        }
    }
}
```

dfs爆搜，未考虑缓存处理，超时：
```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        Arrays.sort(coins);
        dfs(coins, amount, 0, 0, coins.length-1);
        return res == Integer.MAX_VALUE ? -1 : res;
    }
    int res = Integer.MAX_VALUE;
    private void dfs(int[] coins, int t, int sum, int cnt, int start) {
        // System.out.println("coins:"+Arrays.toString(coins)+"; t:"+t+"; sum:"+sum+"; cnt:"+cnt+"; start"+start);
        if (sum == t) {
            res = Math.min(res, cnt);
            return;
        }

        for (int i = start; i >= 0; i--) {
            if (sum + coins[i] > t) continue;
            dfs(coins, t, sum+coins[i], cnt+1, i);
        }
    }
}
```

修改爆搜，递归方法的签名支持返回结果，有利于接下来缓存结果，避免重复计算，与之前复杂度未有变化，无法ac：
```java
class Solution {
    int INF = 0x3f3f3f3f;
    public int coinChange(int[] coins, int amount) {
        int res = dfs(coins, amount);
        return res == INF ? -1 : res;
    }
    
    private int dfs(int[] coins, int t) {
        if (t == 0) {
            return 0;
        }
        if (t < 0) {
            return -1;
        }
        int res = INF;
        for (int i = 0; i < coins.length; i++) {
            if (t-coins[i] >= 0) {
                res = Math.min(res, dfs(coins, t-coins[i])+1);
            }
        }
        
        return res;
    }
}
```

继续修改爆搜，使用cache，成功ac：
```java
class Solution {
    int INF = 0x3f3f3f3f;
    int[] cache;
    public int coinChange(int[] coins, int amount) {
        cache = new int[amount+1];
        int res = dfs(coins, amount);
        return res == INF ? -1 : res;
    }
    
    private int dfs(int[] coins, int t) {
        if (t == 0) {
            return 0;
        }
        if (t < 0) {
            return -1;
        }
        if (cache[t] != 0) {
            return cache[t];
        }
        int res = INF;
        for (int i = 0; i < coins.length; i++) {
            int rem = t-coins[i];
            if (rem >= 0) {
                res = Math.min(res, dfs(coins, rem)+1);
            }
        }
        cache[t] = res;
        return res;
    }
}
```

通过缓存结果成功ac后，可以看出因为递归函数的结果计算存在重复，所以使用其参数（剩余的和）作为key，将结果缓存，就能大大降低计算时间：
```java
class Solution {
    int INF = 0x3f3f3f3f;
    public int coinChange(int[] coins, int amount) {
        // 根据递归的dfs函数看出，结果跟剩余的和相关（t）
        // define dp array 代表剩余i的零钱，最少需要dp[i]个硬币
        // 动归方程：dp[i] = dp[i-1] + dp[i]
        int[] dp = new int[amount+1];
        // 默认本来就是0，这里显示初始化一下是为了表示它的意义：在剩余金额为0的时候，需要的最少硬币是0枚
        dp[0] = 0;
        for (int i = 1; i <= amount; i++) {
            int res = INF;
            for (int c : coins) {
                if (i-c >= 0) {
                    res = Math.min(res, dp[i-c]+1);
                }
            }
            dp[i] = res;
        }
        return dp[amount] == INF ? -1 : dp[amount];
    }
}
```


## 860. 柠檬水找零
```java
class Solution {
    public boolean lemonadeChange(int[] bills) {
        int five = 0;
        int ten = 0;

        for (int b : bills) {
            if (b == 10) ten++;
            if (b == 5) five++;
            int rem = b-5;
            if (rem > 0) {
                if (rem == 5) {
                    if (five > 0) {
                        five--;
                    } else {
                        return false;
                    }
                } else if (rem == 15) {
                    if (five >= 3 || (ten >= 1 && five >= 1)) {
                        if (ten >= 1 && five >= 1) {
                            ten--;
                            five--;
                        } else if (five >= 3) {
                            five-=3;
                        } else {
                            return false;
                        }
                    } else {
                        return false;
                    }
                }
            }
        }
        return true;
    }
}
```


## 455. 分发饼干
```java
class Solution {
    public int findContentChildren(int[] g, int[] s) {
        Arrays.sort(g);
        Arrays.sort(s);
        int cnt = 0;
        int gi = 0;
        int si = 0;
        while (si < s.length && gi < g.length) {
            if (s[si] >= g[gi]) {
                si++;
                gi++;
                cnt++;
            } else {
                si++;
            }
        }
        return cnt;
    }
}
```

## 45. 跳跃游戏 II
```java
class Solution {
    public int jump(int[] nums) {
        int n = nums.length;
        int end = 0;
        int step = 0;
        int max = 0;
        for (int i = 0; i <= end; i++) {
            if (end >= n-1) return step;
            max = Math.max(max, i+nums[i]);
            if (i == end) {
                step++;
                end = max;
            }
        }
        return -1;
    }
}
```