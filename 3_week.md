# 第三周

先是作业题：

## 106. 从中序与后序遍历序列构造二叉树

```java
class Solution {
    public TreeNode buildTree(int[] inorder, int[] postorder) {
        return recursive(postorder, 0, postorder.length-1, inorder, 0, inorder.length-1);
    }

    private TreeNode recursive(int[] post, int pl, int pr, int[] in, int il, int ir) {
        if (pl > pr || il > ir || pl < 0 || pr >= post.length || il < 0 || ir >= in.length) {
            return null;
        }

        int root = post[pr];
        TreeNode node = new TreeNode(root);
        // 在中序遍历数组中找到root节点
        int index = -1;
        for (int i = il; i <= ir; i++) {
            if (in[i] == root) {
                index = i;
                break;
            }
        }
        int lsize = index - il;
        node.left = recursive(post, pl, pl+lsize-1, in, il, index-1);
        node.right = recursive(post, pl+lsize, pr-1, in, index+1, ir);

        return node;
    }
}
```

## 210. 课程表 II
拓扑排序：
```java
class Solution {
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        int nc = numCourses;
        int[][] pres = prerequisites;

        HashSet<Integer>[] relate = new HashSet[nc];
        for (int i = 0; i < nc; i++) {
            relate[i] = new HashSet<>();
        }

        int[] degree = new int[nc];
        for (int[] pre : pres) {
            degree[pre[0]]++;
            relate[pre[1]].add(pre[0]);
        }

        Queue<Integer> q = new LinkedList<>();
        for (int i = 0; i < nc; i++) {
            if (degree[i] == 0) q.offer(i);
        }

        int[] res = new int[nc];
        int cnt = 0;
        while (!q.isEmpty()) {
            int k = q.poll();
            res[cnt++] = k;
            HashSet<Integer> set = relate[k];

            for (int n : set) {
                degree[n]--;
                if (degree[n] == 0) q.offer(n);
            }
        }

        if (cnt == nc) {
            return res;
        }
        return new int[]{};
    }
}
```


## 130. 被围绕的区域
```java
// bfs
class Solution {
    public void solve(char[][] board) {
        bfs(board);
    }

    private int[][] directions = new int[][]{{-1, 0}, {0, -1}, {1, 0}, {0, 1}};

    public void bfs(char[][] board) {
        Queue<int[]> q = new LinkedList<>();

        int rows = board.length;
        int cols = board[0].length;
        // 放入边界节点
        // 加入列的边界'0'节点
        for (int i = 0; i < rows; i++) {
            if (board[i][0] == 'O') {
                q.offer(new int[]{i, 0});
            }
            if (board[i][cols-1] == 'O') {
                q.offer(new int[]{i, cols-1});
            }
        }
        // 加入行的边界'0'节点
        for (int i = 0; i < cols; i++) {
            if (board[0][i] == 'O') {
                q.offer(new int[]{0, i});
            }
            if (board[rows-1][i] == 'O') {
                q.offer(new int[]{rows-1, i});
            }
        }

        while (!q.isEmpty()) {
            int sz = q.size();
            for (int i = 0; i < sz; i++) {
                int[] cur = q.poll();

                int y = cur[0];
                int x = cur[1];
                board[y][x] = '#';

                for (int[] d : directions) {
                    int newY = y + d[0];
                    int newX = x + d[1];

                    if (inArea(newX, newY, rows, cols) && board[newY][newX] == 'O') {
                        q.offer(new int[]{newY, newX});
                    }
                }
            }
        }

        // 恢复
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (board[i][j] == '#') {
                    board[i][j] = 'O';
                } else if (board[i][j] == 'O') {
                    board[i][j] = 'X';
                }
            }
        }

    }

    private boolean inArea(int x, int y, int rows, int cols) {
        return x >= 0 && x < cols && y >= 0 && y < rows;
    }
}
```



课堂题：

## 94. Binary Tree Inorder Traversal（二叉树的中序遍历）
```java
class Solution {
    // 迭代
    public List<Integer> inorderTraversal(TreeNode n) {
        List<Integer> r = new ArrayList<>();
        Stack<TreeNode> s = new Stack<>();
        while (!s.empty() || n != null) {
            while (n != null) {
                s.push(n);
                n = n.left;
            }
            n = s.pop();
            r.add(n.val);
            n = n.right;
        }
        return r;
    }

    // 递归
    public List<Integer> inorderTraversal2(TreeNode root) {
        List<Integer> r = new ArrayList<>();
        recursive(root, r);
        return r;
    }

    private void recursive(TreeNode n, List<Integer> r) {
        if (n == null) return;
        recursive(n.left, r);
        r.add(n.val);
        recursive(n.right, r);
    }
}
```

## 589. N 叉树的前序遍历
```java
class Solution {
    List<Integer> res = new ArrayList<>();
    public List<Integer> preorder(Node root) {
        f(root);
        return res;
    }
    
    private void f(Node node) {
        if (node == null) return;
        res.add(node.val);
        List<Node> list = node.children;
        for (Node n : list) {
            f(n);
        }
    }
}
```

```go
func preorder(root *Node) []int {
    res := []int{}
    recursive(root, &res)
    return res
}

func recursive(node *Node, res *[]int) {
    if node == nil {
        return
    }
    *res = append(*res, node.Val)
    for _, n := range node.Children {
        recursive(n, res)
    }
}
```

## 297. 二叉树的序列化与反序列化
相同题：[剑指 Offer 37. 序列化二叉树](https://leetcode-cn.com/problems/xu-lie-hua-er-cha-shu-lcof/)

前序遍历实现：
```java
public class Codec {
    private static final String SEP = ",";

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        recursive(root, sb);
        return sb.delete(sb.length()-1, sb.length()).toString();
    }

    private void recursive(TreeNode node, StringBuilder sb) {
        if (node == null) {
            sb.append("#").append(SEP);
            return;
        }
        // 前序遍历，先将root的值加入String
        sb.append(node.val).append(SEP);
        recursive(node.left, sb);
        recursive(node.right, sb);
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        LinkedList<String> nodes = new LinkedList<>();
        for (String s : data.split(SEP)) {
            nodes.addLast(s);
        }
        return recursive(nodes);
    }

    private TreeNode recursive(LinkedList<String> nodes) {
        if (nodes.size() <= 0) {
            return null;
        }
        // 前序遍历的第一个节点一定为root节点
        TreeNode root = null;
        String node = nodes.removeFirst();
        if (!node.equals("#")) {
            root = new TreeNode(Integer.valueOf(node));
            // left
            root.left = recursive(nodes);
            // right
            root.right = recursive(nodes);
        }
        return root;
    }
}
```

后序遍历实现：
```java
public class Codec {
    private static final String SEP = ",";

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        recursive(root, sb);
        return sb.delete(sb.length()-1, sb.length()).toString();
    }

    private void recursive(TreeNode node, StringBuilder sb) {
        if (node == null) {
            sb.append("#").append(SEP);
            return;
        }
        recursive(node.left, sb);
        recursive(node.right, sb);
        // 后序遍历
        sb.append(node.val).append(SEP);
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        LinkedList<String> nodes = new LinkedList<>();
        for (String s : data.split(SEP)) {
            nodes.addLast(s);
        }
        return recursive(nodes);
    }

    private TreeNode recursive(LinkedList<String> nodes) {
        if (nodes.size() <= 0) {
            return null;
        }
        TreeNode root = null;
        // 后序遍历，root是后面第一个
        String node = nodes.removeLast();
        if (!node.equals("#")) {
            root = new TreeNode(Integer.valueOf(node));
            // 后序遍历，先right，后left，不理解一画图就明白了。
            root.right = recursive(nodes);
            root.left = recursive(nodes);
        }
        return root;
    }
}
```

反序列化时，中序遍历由于无法确定root节点位置，因此无法构造二叉树：
```java
public class Codec {
    private static final String SEP = ",";

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        recursive(root, sb);
        return sb.delete(sb.length()-1, sb.length()).toString();
    }

    private void recursive(TreeNode node, StringBuilder sb) {
        if (node == null) {
            sb.append("#").append(SEP);
            return;
        }
        recursive(node.left, sb);
        // 中序遍历
        sb.append(node.val).append(SEP);
        recursive(node.right, sb);
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        // 无法找到root节点
    }
}
```

层级遍历：

层级遍历有两种反序列化写法，逻辑其实是一样的，都是将上一层节点存入queue中，在遍历到层是，取出来，为其装上左右腿（node.left, node.right）。
```java
public class Codec {
    private static final String SEP = ",";

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        StringBuilder sb = new StringBuilder();

        Queue<TreeNode> q = new LinkedList<>();
        q.offer(root);
        while (!q.isEmpty()) {
            TreeNode node = q.poll();
            if (node == null) {
                sb.append("#").append(SEP);
            } else {
                sb.append(node.val).append(SEP);
                q.offer(node.left);
                q.offer(node.right);
            }
        }
        return sb.delete(sb.length()-1, sb.length()).toString();
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        LinkedList<String> nodes = new LinkedList<>();
        for (String s : data.split(SEP)) {
            nodes.addLast(s);
        }

        Queue<TreeNode> q = new LinkedList<>();
        String first = nodes.removeFirst();
        if (first.equals("#")) {
            return null;
        }
        TreeNode root = new TreeNode(Integer.valueOf(first));
        q.offer(root);
        while (!q.isEmpty()) {
            int size = q.size();
            for (int i = 0; i < size; i++) {
                TreeNode node = q.poll();
                if (node != null) {
                    // 加入到
                    String left = nodes.removeFirst();
                    String right = nodes.removeFirst();

                    if (!left.equals("#")) {
                        node.left = new TreeNode(Integer.valueOf(left));
                    }
                    if (!right.equals("#")) {
                        node.right = new TreeNode(Integer.valueOf(right));
                    }
                    q.offer(node.left);
                    q.offer(node.right);
                }
            }
        }
        return root;
    }

}
```

go解法：
```go
import "fmt"
type Codec struct {
    
}

func Constructor() Codec {
    c := Codec{}
    return c
}

// Serializes a tree to a single string.
func (this *Codec) serialize(root *TreeNode) string {
    res := ""
    serialize(root, &res)
    // fmt.Println(res[1:])
    return res[1:]
}

func serialize(n *TreeNode, res *string) {
    if n == nil {
        *res = *res + ",#"
        return
    }
    *res = *res + "," + strconv.Itoa(n.Val)
    
    serialize(n.Left, res)
    serialize(n.Right, res)
}

// Deserializes your encoded data to tree.
func (this *Codec) deserialize(data string) *TreeNode {
    if data == "" {
        return nil
    }
    
    arr := strings.Split(data, ",")
    // fmt.Println(arr)
    return deserialize(&arr)
}

func deserialize(s *[]string) *TreeNode {
    // fmt.Println(len(*s))
    if len(*s) == 0 {
        return nil
    }
    if (*s)[0] == "#" {
        *s = (*s)[1:]
        return nil
    }
    v,_ := strconv.Atoi((*s)[0])
    // fmt.Println(v)
    n := TreeNode{v, nil, nil}
    *s = (*s)[1:]
    
    n.Left = deserialize(s)
    n.Right = deserialize(s)
    return &n
}
```

## 105. 从前序与中序遍历序列构造二叉树

```java
class Solution {
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        return recursive(preorder, 0, preorder.length-1, inorder, 0, inorder.length-1);
    }

    private TreeNode recursive(int[] pre, int pl, int pr, int[] in, int il, int ir) {
        if (pl > pr || il > ir || pl < 0 || pr >= pre.length || il < 0 || ir >= in.length) {
            return null;
        }

        int root = pre[pl];
        TreeNode node = new TreeNode(root);
        // 在中序遍历数组中找到root节点
        int index = -1;
        for (int i = il; i <= ir; i++) {
            if (in[i] == root) {
                index = i;
                break;
            }
        }
        int lsize = index - il;
        node.left = recursive(pre, pl+1, pl+lsize, in, il, index-1);
        node.right = recursive(pre, pl+lsize+1, pr, in, index+1, ir);

        return node;
    }
}
```

## 236. Lowest Common Ancestor of a Binary Tree（二叉树的最近公共祖先）
```java
// f(l) = true 表示其子节点存在等于p或者q的
// f(left) && f(right)
// (cur==p || cur==q) && (f(left) || f(right))
class Solution {
    TreeNode father = null;

    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        f(root, p, q);
        return father;
    }

    private boolean f(TreeNode n, TreeNode p, TreeNode q) {
        if (father != null) return false;

        if (n == null) return false;

        boolean l = f(n.left, p, q);
        boolean r = f(n.right, p, q);

        if ((l && r) || ((n.val == p.val || n.val == q.val) && (l || r))) {
            father = n;
        }

        if (father == null && (n.val == p.val || n.val == q.val)) {
            // System.out.println("p:"+p.val+"q:"+q.val+"n:"+n.val);
            return true;
        }
        if (father == null && (l || r)) {
            // System.out.println("l:"+l+"r:"+r);
            return true;
        }
        return false;
    }
}
```

## 684. 冗余连接
并查集：
```java
class Solution {
    public int[] findRedundantConnection(int[][] edges) {
        int n = edges.length;
        UnionFind uf = new UnionFind(n);
        int[] res = new int[2];
        for (int i = 0; i < n; i++) {
            int[] e = edges[i];
            if (uf.connected(e[0]-1, e[1]-1)) {
                res = e;
            }
            uf.union(e[0]-1, e[1]-1);
        }
        return res;
    }
    class UnionFind {
        private int count = 0;
        private int[] parent;

        public UnionFind(int n) {
            count = n;
            parent = new int[n];
            for (int i = 0; i < n; i++) {
                parent[i] = i;
            }
        }

        public int find(int p) {
            while (p != parent[p]) {
                parent[p] = parent[parent[p]];
                p = parent[p];
            }
            return p;
        }

        public boolean connected(int p, int q) {
            return find(p) == find(q);
        }

        public void union(int p, int q) {
            int rp = find(p);
            int rq = find(q);
            if (rp == rq) return;
            parent[rp] = rq;
            count--;
        }
    }
}
```

## 207. 课程表
拓扑排序
但是思路比较乱，其实套模板即可：
```java
class Solution {
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        int nc = numCourses;
        int[][] pres = prerequisites;

        Set<Integer> set = new HashSet<>();
        Map<Integer, List<Integer>> pres_map = new HashMap<>();
        Map<Integer, Integer> degree = new HashMap<>();
        for (int[] pre : pres) {
            if (pre[0] >= 0 && pre[0] < nc) {
                if (pre[1] >= 0 && pre[1] < nc) {
                    List<Integer> list = pres_map.getOrDefault(pre[0], new ArrayList<>());
                    list.add(pre[1]);
                    pres_map.put(pre[0], list);
                    set.add(pre[1]);
                    set.add(pre[0]);
                    degree.put(pre[0], degree.getOrDefault(pre[0], 0));
                    degree.put(pre[1], degree.getOrDefault(pre[1], 0)+1);
                } else {
                    return false;
                }
            }
        }
        LinkedList<Integer> q = new LinkedList<>();
        for (Map.Entry<Integer, Integer> entry : degree.entrySet()) {
            if (entry.getValue() == 0) {
                q.add(entry.getKey());
            }
        }

        int cnt = 0;
        while (!q.isEmpty()) {
            int k = q.remove();
            cnt++;
            List<Integer> list = pres_map.getOrDefault(k, new ArrayList<>());
            for (int i : list) {
                int count = degree.get(i);
                count--;
                if (count == 0) q.add(i);
                degree.put(i, count);
            }
        }

        return cnt == set.size();
    }
}
```

## 51. N-Queens（N 皇后）

回溯剪枝：
```java
class Solution {
    List<List<String>> res = new ArrayList<>();
    public List<List<String>> solveNQueens(int n) {
        recursive(n, 0, new ArrayList<>(), -2);
        return res;
    }

    private void recursive(int n, int depth, List<String> board, int curri) {
        // 递归深度（列）超过n
        if (depth >= n) {
            res.add(new ArrayList(board));
            return;
        }

        // 横行遍历
        for (int i = 0; i < n; i++) {
            // 跳过无法使用的格子
            if (!isValid(n, board, depth, i)) {
                continue;
            }

            // 选择i为Q的位置，生成一行的字符串
            String row = "";
            for (int j = 0; j < n; j++) {
                if (j == i) {
                    row = row + "Q";
                } else {
                    row = row + ".";
                }
            }

            board.add(row);

            recursive(n, depth+1, board, i);
            
            // 回退
            board.remove(board.size()-1);
        }
    }

    private boolean isValid(int rn, List<String> board, int row, int col) {
        // cn col长度，rn row长度
        int cn = board.size();

        // 检查列是否有皇后互相冲突
        for (int i = 0; i < cn; i++) {
            if (board.get(i).charAt(col) == 'Q')
                return false;
        }
        // 检查右上方是否有皇后互相冲突
        for (int i = row - 1, j = col + 1;
             i >= 0 && j < rn; i--, j++) {
            if (board.get(i).charAt(j) == 'Q')
                return false;
        }
        // 检查左上方是否有皇后互相冲突
        for (int i = row - 1, j = col - 1;
             i >= 0 && j >= 0; i--, j--) {
            if (board.get(i).charAt(j) == 'Q')
                return false;
        }
        return true;
    }
}
```

## 200. Number of Islands（岛屿数量）
```java
class Solution {
    int col;
    int row;
    int cnt = 0;
    public int numIslands(char[][] g) {
        col = g[0].length;
        row = g.length;
//        System.out.println("col:"+col+"row:"+row);
        for (int y = 0; y < row; y++) {
            for (int x = 0; x < col; x++) {
//                System.out.println("g[y][x]::y"+y+"x"+x+";;;;val:"+g[y][x]);
                if (g[y][x] == '1') {
                    cnt++;
                    bfs(g, x, y);
                }
            }
        }
        return cnt;
    }

    private void bfs(char[][] n, int x, int y) {
        // base condtion
        if (x > col-1 || y > row-1 || y < 0 || x < 0) {
            return;
        }
        if (n[y][x] == '2' || n[y][x] == '0') {
            return;
        }
        // process
        if (n[y][x] == '1') {
            n[y][x] = '2';
        }
        // up
        bfs(n, x, y-1);
        // down
        bfs(n, x, y+1);
        // left
        bfs(n, x-1, y);
        // right
        bfs(n, x+1, y);
    }
}
```

## 433. 最小基因变化
bfs:
```java
class Solution {
    public int minMutation(String start, String end, String[] bank) {
        char[] seq = new char[]{'A', 'C', 'G', 'T'};
        Set<String> bankset = new HashSet<>();
        for (String s : bank) {
            bankset.add(s);
        }

        Queue<String> q = new LinkedList<>();
        Set<String> visited = new HashSet<>();
        q.offer(start);
        visited.add(start);

        int cnt = 0;
        while (!q.isEmpty()) {
            int sz = q.size();
            
            for (int i = 0; i < sz; i++) {
                String cur = q.poll();
                if (cur.equals(end)) return cnt;

                for (int j = 0; j < cur.length(); j++) {
                    for (char c : seq) {
                        String newStr = cur.substring(0, j) + String.valueOf(c) + cur.substring(j+1, cur.length());
                        if (bankset.contains(newStr) && !visited.contains(newStr)) {
                            q.offer(newStr);
                            visited.add(newStr);
                        }
                    }
                }
            }
            cnt++;
        }
        return -1;
    }
}
```