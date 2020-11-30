# BFS算法框架详解

BFS 的核⼼思想应该不难理解的，就是把⼀些问题抽象成图，从⼀个点开 始，向四周开始扩散。⼀般来说，我们写 BFS 算法都是用「队列」这种数 据结构，每次将⼀个节点周围的所有节点加入队列。（DFS 算法就是回溯算法，在[回溯算法框架套路详解](../回溯/回溯详解.md) 就写过了）

BFS 的核⼼思想应该不难理解的，就是把⼀些问题抽象成图，从⼀个点开 始，向四周开始扩散。⼀般来说，我们写 BFS 算法都是用「队列」这种数 据结构，每次将⼀个节点周围的所有节点加入队列。

BFS 相对 DFS 的最主要的区别是：**BFS 找到的路径⼀定是最短的，但代价 就是空间复杂度比 DFS 大很多**，⾄于为什么，我们后面介绍了框架就很容易看出来了。

本⽂就由浅⼊深写两道 BFS 的典型题⽬，分别是「⼆叉树的最小高度」和 「打开密码锁的最少步数」，⼿把⼿教你怎么写 BFS 算法。

## 一、算法框架

要说框架的话，我们先举例⼀下 BFS 出现的常见场景好吧，**问题的本质就 是让你在⼀幅「图」中找到从起点``` start``` 到终点 ```target``` 的最近距离，这 个例子听起来很枯燥，但是 BFS 算法问题其实都是在干这个事儿**，把枯燥的本质搞清楚了，再去欣赏各种问题的包装才能胸有成竹嘛。

这个⼴义的描述可以有各种变体，比如走迷宫，有的格子是围墙不能⾛，从 起点到终点的最短距离是多少？如果这个迷宫带「传送门」可以瞬间传送呢？

再比如说两个单词，要求你通过某些替换，把其中一个变成另一个，每次只 能替换一个字符，最少要替换几次？

再比如说连连看游戏，两个方块消除的条件不仅仅是图案相同，还得保证两 个⽅块之间的最短连线不能多于两个拐点。你玩连连看，点击两个坐标，游戏是如何判断它俩的最短连线有几个拐点的？

这些问题都没啥奇技淫巧，本质上就是⼀幅「图」，让 你从⼀个起点，走到终点，问最短路径。这就是 BFS 的本质，框架搞清楚 了直接默写就好。

记住下⾯这个框架就 OK 了：

```java
// 计算从起点 start 到终点 target 的最近距离
int BFS(Node start, Node target) {
	Queue<Node> q; // 核⼼数据结构
	Set<Node> visited; // 避免⾛回头路
    
	q.offer(start); // 将起点加⼊队列
	visited.add(start);
	int step = 0; // 记录扩散的步数
    
	while (q not empty) {
		int sz = q.size();
		/* 将当前队列中的所有节点向四周扩散 */
		for (int i = 0; i < sz; i++) {
			Node cur = q.poll();
			/* 划重点：这⾥判断是否到达终点 */
			if (cur is target)
				return step;
			/* 将 cur 的相邻节点加⼊队列 */
			for (Node x : cur.adj())
				if (x not in visited) {
				q.offer(x);
				visited.add(x);
			}
		}
		/* 划重点：更新步数在这⾥ */
		step++;
	}
}

```

队列``` q``` 就不说了，BFS 的核⼼数据结构；``` cur.adj() ```泛指 ```cur ```相邻的节点，比如说⼆维数组中， ```cur``` 上下左右四⾯的位置就是相邻节 点； ```visited ```的主要作⽤是防⽌⾛回头路，⼤部分时候都是必须的，但是 像⼀般的⼆叉树结构，没有子节点到父节点的指针，不会走回头路就不需要 ```visited``` 。

## 二、二叉树的最大和最小高度

先来个简单的问题实践⼀下 BFS 框架吧，判断⼀棵⼆叉树的最⼩⾼度，这 也是 LeetCode 第 111 题，看⼀下题⽬：

![](..\pictures\BFS\二叉树最小高度题目.jpg)

怎么套到 BFS 的框架⾥呢？⾸先明确⼀下起点 ```start``` 和终点 ```target``` 是什 么，怎么判断到达了终点？ 

**显然起点就是 ```root``` 根节点，终点就是最靠近根节点的那个「叶⼦节点」 嘛**，叶⼦节点就是两个⼦节点都是 null 的节点：

```java
if (cur.left == null && cur.right == null)
// 到达叶⼦节点	
```

那么，按照我们上述的框架稍加改造来写解法即可：

```java
public int minDepth(TreeNode root) {
    if (root == null) return 0;
    Queue<TreeNode> q = new LinkedList<>();
    q.offer(root);
    // root 本⾝就是⼀层，depth 初始化为 1
    int depth = 1;
    while (!q.isEmpty()) {
        int sz = q.size();
        /* 将当前队列中的所有节点向四周扩散 */
        for (int i = 0; i < sz; i++) {
            //poll是返回队头元素，且删除该元素
            TreeNode cur = q.poll();
            /* 判断是否到达终点
            *  这里找到叶子节点直接返回，
            *  可避免for循环结束后多算一次depth++
            */
            if (cur.left == null && cur.right == null)
                return depth;
            /* 将 cur 的相邻节点加⼊队列 */
            if (cur.left != null)
                q.offer(cur.left);
            if (cur.right != null)
                q.offer(cur.right);
        }
        /* 这⾥增加步数 */
        depth++;
    }
    return depth;
}
```

再来看一个类似的问题，也是LeeCode第104问题，二叉树最大深度

![](..\pictures\BFS\二叉树最大深度.png)

按照上面的框架，我们这里直接给出代码，需要主要的地方我在代码注释中都有提醒到：

```java
public int maxDepth(TreeNode root) {
    if (root == null) {
        return 0;
    }
    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    int ans = 1;
    while (!queue.isEmpty()) {
        int size = queue.size();
        for (int i = 0; i < size; i++) {
            TreeNode node = queue.poll();
            if (node.left != null) {
                queue.offer(node.left);
            }
            if (node.right != null) {
                queue.offer(node.right);
            }
        }
        ans++;
    }
    //遍历结束后，会多运行一次ans++，故这里 - 1再return
    return ans - 1;
}
```

⼆叉树是很简单的数据结构，我想上述两段代码你应该可以理解的吧，其实其他 复杂问题都是这个框架的变形，再探讨复杂问题之前，我们解答两个问题：

**1、为什么 BFS 可以找到最短距离，DFS 不⾏吗？**

⾸先，你看 BFS 的逻辑，```depth ```每增加⼀次，队列中的所有节点都向前迈 ⼀步，这保证了第⼀次到达终点的时候，⾛的步数是最少的。

DFS 不能找最短路径吗？其实也是可以的，但是时间复杂度相对⾼很多。 你想啊，DFS 实际上是靠递归的堆栈记录⾛过的路径，你要找到最短路 径，肯定得把⼆叉树中所有树杈都探索完才能对⽐出最短的路径有多长对不对？而BFS 借助队列做到⼀次⼀步「⻬头并进」，是可以在不遍历完整棵 树的条件下找到最短距离的。

形象点说，DFS 是线，BFS 是⾯；DFS 是单打独斗，BFS 是集体行动。这 个应该比较容易理解吧。

**2、既然 BFS 那么好，为啥 DFS 还要存在？**

BFS 可以找到最短距离，但是空间复杂度⾼，⽽ DFS 的空间复杂度较低。

还是拿刚才我们处理⼆叉树问题的例子，假设给你的这个⼆叉树是满⼆叉 树，节点数为``` N ```，对于 DFS 算法来说，空间复杂度无非就是递归堆栈，最坏情况下顶多就是树的高度，也就是 ```O(logN) ```。

但是你想想 BFS 算法，队列中每次都会储存着⼆叉树⼀层的节点，这样的 话最坏情况下空间复杂度应该是树的最底层节点的数量，也就是 ```N/2``` ，⽤ Big O 表⽰的话也就是```O(N)``` 。

由此观之，BFS 还是有代价的，⼀般来说在找最短路径的时候使用 BFS， 其他时候还是 DFS 使⽤得多⼀些（主要是递归代码好写）。

好了，现在你对 BFS 了解得⾜够多了，下⾯来⼀道难⼀点的题目，深化⼀ 下框架的理解吧。

## 三、解开密码锁最少次数

这道是LeeCode第752题：

![](..\pictures\BFS\解开密码锁最多次数.png)

题⽬中描述的就是我们⽣活中常见的那种密码锁，若果没有任何约束，最少的拨动次数很好算，就像我们平时开密码锁那样直奔密码拨就行了。

**第⼀步，我们不管所有的限制条件，不管 ```deadends``` 和 ```target``` 的限制，就 思考⼀个问题：如果让你设计⼀个算法，穷举所有可能的密码组合，你怎么 做？**

穷举呗，再简单⼀点，如果你只转⼀下锁，有⼏种可能？总共有 4 个位置， 每个位置可以向上转，也可以向下转，也就是有 8 种可能对吧

⽐如说从 ```"0000"``` 开始，转⼀次，可以穷举出``` "1000", "9000", "0100", "0900"... ```共 8 种密码。然后，再以这 8 种密码作为基础，对每个密码再转 ⼀下，穷举出所有可能... 

**仔细想想，这就可以抽象成⼀幅图，每个节点有 8 个相邻的节点**，⼜让你求最短距离，这不就是典型的 BFS 嘛，框架就可以派上⽤场了，先写出⼀个 「简陋」的 BFS 框架代码再说别的：

```java
// 将 s[j] 向上拨动⼀次
String plusOne(String s, int j) {
	char[] ch = s.toCharArray();
	if (ch[j] == '9')
		ch[j] = '0';
	else
		ch[j] += 1;
	return new String(ch);
}
// 将 s[i] 向下拨动⼀次
String minusOne(String s, int j) {
	char[] ch = s.toCharArray();
	if (ch[j] == '0')
		ch[j] = '9';
	else
		ch[j] -= 1;
	return new String(ch);
}

// BFS 框架，打印出所有可能的密码
void BFS(String target) {
	Queue<String> q = new LinkedList<>();
	q.offer("0000");
	while (!q.isEmpty()) {
		int sz = q.size();
		/* 将当前队列中的所有节点向周围扩散 */
		for (int i = 0; i < sz; i++) {
			String cur = q.poll();
			/* 判断是否到达终点 */
			System.out.println(cur);
			/* 将⼀个节点的相邻节点加⼊队列 */
			for (int j = 0; j < 4; j++) {
				String up = plusOne(cur, j);
				String down = minusOne(cur, j);
				q.offer(up);
				q.offer(down);
			}
		}
		/* 在这⾥增加步数 */
	}
	return;
}
```

PS：这段代码当然有很多问题，但是我们做算法题肯定不是⼀蹴而就的， 而是从简陋到完美的。不要完美主义，咱要慢慢来，好不。

**这段 BFS 代码已经能够穷举所有可能的密码组合了，但是显然不能完成题 ⽬，有如下问题需要解决：**

1、会⾛回头路。比如说我们从``` "0000" ```拨到 ```"1000"``` ，但是等从队列拿出 ```"1000"``` 时，还会拨出⼀个 ```"0000"``` ，这样的话会产⽣死循环。 

2、没有终止条件，按照题⽬要求，我们找到 ```target```就应该结束并返回拨 动的次数。

3、没有对 ```deadends``` 的处理，按道理这些「死亡密码」是不能出现的，也 就是说你遇到这些密码的时候需要跳过。

如果你能够看懂上面那段代码，真得给你⿎掌，只要按照 BFS 框架在对应 的位置稍作修改即可修复这些问题：

```java
int openLock(String[] deadends, String target) {
// 记录需要跳过的死亡密码
	Set<String> deads = new HashSet<>();
	for (String s : deadends) deads.add(s);
		// 记录已经穷举过的密码，防⽌⾛回头路
		Set<String> visited = new HashSet<>();
		Queue<String> q = new LinkedList<>();
		// 从起点开始启动⼴度优先搜索
		int step = 0;
		q.offer("0000");
		visited.add("0000");
    
		while (!q.isEmpty()) {
			int sz = q.size();
			/* 将当前队列中的所有节点向周围扩散 */
			for (int i = 0; i < sz; i++) {
				String cur = q.poll();
                
				/* 判断是否到达终点 */
				if (deads.contains(cur))
					continue;
				if (cur.equals(target))
					return step;
				
                /* 将⼀个节点的未遍历相邻节点加⼊队列 */
				for (int j = 0; j < 4; j++) {
					String up = plusOne(cur, j);
					if (!visited.contains(up)) {
						q.offer(up);
						visited.add(up);
					}
					String down = minusOne(cur, j);
					if (!visited.contains(down)) {
						q.offer(down);
						visited.add(down);
					}
				}
			}
			/* 在这⾥增加步数 */
			step++;
	}
	// 如果穷举完都没找到⽬标密码，那就是找不到了
	return -1;
}

```

⾄此，我们就解决这道题⽬了。有⼀个比较小的优化：可以不需要 ```dead``` 这个哈希集合，可以直接将这些元素初始化到``` visited ```集合中，效果是一样的，可能更加优雅⼀些。

