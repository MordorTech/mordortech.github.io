# 各类要点

1. 采用相对（可以把一个拉成有序的，再对另一个进行操作）/绝对位置来记录

2. 通过推式子把无关的移到一边，有关的移到另一边，在考虑其性质（单调性:单调队列/栈）

3. 看起来非常结论性的题目可以找规律

4. 构造%q后的序列的时候常把所得的商设为0或1

5. 补集思想

6. 树上边权异或，链异或，可转化为点，每个点的权值为周围所有边的异或权值，对链异或只需异或端点即可

7. 区间异或相关——可持久化trie

8. 某些信息可以存在树上，通过lca求取答案

9. 快写速度比`printf()`快几百倍

10. $\sum_{k=0}^nC(n,k)\times2^k=3^n$

11. I. 序列多次区间询问的题目，可以离线询问，对右端点做扫描线。

    II. 看到字符集不全（比如字符集为 `a` 至 `u` ）的字符串题，考虑用状压或爆搜。

    III. 看到多次询问图上两点路径的问题，考虑使用生成树。

    IV. 看到异或，选择拆位。

    V. 看到序列两位交换，考虑逆序对。

    VI. 看到语文阅读题，选择性暂时略过，看到难码的，先码部分分。

    VII. 看到子树，考虑树上差分和 `dfs`序。

    VIII. 看到难做的，考虑倍增或根号分治（小于 $\sqrt n$ 的用暴力，大于 $\sqrt n$ 的用奇怪的方法维护）

    IX. 看到最大的最小、最小的最大，或者最小第 x 天（不）可以，最多 x 天等一切满足单调性的，可以往二分去想

12. 板子

    ```cpp
    #include <cstdio>
    #include <iostream>
    #include <algorithm>
    #include <cstring>
    #include <ctime>
    #define For(i, l, r) for (int i = (int)(l); i <= (int)(r); ++i)
    #define Rep(i, r, l) for (int i = (int)(r); i >= (int)(l); --i)
    #define Pb push_back
    #define Mp make_pair
    #define Fi first
    #define Se second
    #define Gc getchar
    #define randint() ((rand() << 16) | rand())
    #define randLL() ((randint() << 16) | randint())
    
    using namespace std;
    typedef long long LL;
    const int MOD = 1000000007;
    const int INF = 0x3f3f3f3f;
    const int N = ;
    
    struct Edge {
        int to, nxt;
    }e[N << 1];
    int cnt, lst[N];
    
    inline void upd(int &x, int y) {x += y; x -= x >= MOD ? MOD : 0;}
    inline int dec(int x, int y) {x -= y; x += x < 0 ? MOD : 0; return x;}
    inline int add(int x, int y) {x += y; x -= x >= MOD ? MOD : 0; return x;}
    inline int mul(int x, int y) {return 1LL * x * y % MOD;};
    
    inline int qui_pow(int x, int y) {
        int ret = 1;
        for(; y; y >>= 1) {
            if (y & 1) ret = mul(ret, x);
            x = mul(x, x);
        }
        return ret;
    }
    
    inline LL read() {
        LL x = 0, f = 1;
        char ch = Gc();
        while (!isdigit(ch) && ch != '-') ch = Gc();
        if (ch == '-') f = -1, ch = Gc();
        while (isdigit(ch)) {x = (x << 1) + (x << 3) + (ch ^ 48); ch = Gc();}
        return x * f;
    }
    
    inline void wt(LL x) {
    	if (!x) return;
    	wt(x / 10);
    	putchar(x % 10 + 48);
    }
    
    inline void write(LL x) {
    	if (x > 0) wt(x);
    	else if (x < 0) {putchar('-'); wt(-x);}
    	else putchar('0');
    }
    
    inline void link(int u, int v) {
        e[++cnt].to = v;
        e[cnt].nxt = lst[u];
        lst[u] = cnt;
    }
    
    int main() {
    
    	return 0;
    }
    ```

# 模板

1. 主席树

   ```cpp
   #include <cstdio>
   #include <algorithm>
   #define N 200010
   
   using namespace std;
   int p, a[N], b[N];
   
   struct Node {
   	Node *ls, *rs; int sum;
   	Node(Node *l = NULL, Node *r = NULL, int x = 0) {
   		ls = l; rs = r; sum = x;
   	}
   } *rt[N];
   
   inline void build(Node *&rt, int l, int r) {
   	rt = new Node(NULL, NULL, 0);
   	if (l == r) return;
   	int mid = l + r >> 1;
   	build(rt -> ls, l, mid);
   	build(rt -> rs, mid + 1, r);
   }
   
   inline Node *modify(Node *rt, int l, int r) {
   	Node *now = new Node(rt -> ls, rt -> rs, rt -> sum + 1);
   	if (l == r) return now;
   	int mid = l + r >> 1;
   	if (p <= mid) now -> ls = modify(now -> ls, l, mid);
   	else now -> rs = modify(now -> rs, mid + 1, r);
   	return now;
   }
   
   inline int query(Node *L, Node *R, int l, int r, int k) {
   	if (l == r) return l;
   	int mid = l + r >> 1, x = R -> ls -> sum - L -> ls -> sum;
   	if (x >= k) return query(L -> ls, R -> ls, l, mid, k);
   	else return query(L -> rs, R -> rs, mid + 1, r, k - x);
   }
   
   int main() {
   	int n, m;
   	scanf("%d%d", &n, &m);
   	for (int i = 1; i <= n; ++i) {
   		scanf("%d", &a[i]);
   		b[i] = a[i];
   	}
   	sort(b + 1, b + n + 1);
   	int nn = unique(b + 1, b + n + 1) - b - 1;
   	build(rt[0], 1, nn);
   	for (register int i = 1; i <= n; ++i) {
   		p = lower_bound(b + 1, b + nn + 1, a[i]) - b;
   		rt[i] = modify(rt[i - 1], 1, nn);
   	}
   	while (m--) {
   		int l, r, k;
   		scanf("%d%d%d", &l, &r, &k);
   		printf("%d\n", b[query(rt[l - 1], rt[r], 1, nn, k)]);
   	}
   	return 0;
   }
   ```

2. 模拟退火

   ```cpp
   #include <cstdio>
   #include <algorithm>
   #include <cmath>
   #include <ctime>
   #include <iostream>
   #define N 25
   #define C 55
   
   using namespace std;
   const int dx[2] = {1, 0};
   const int dy[2] = {0, 1};
   const double dlt = 0.99995;
   const double T_end = 1e-15;
   
   struct G{
   	int map[N][N], q;
   }ans;
   
   int p[C], n, m, c;
   double T;
   
   inline void getq(G &now) {
   	int cnt = 0;
   	for (int i = 1; i <= n; ++i) {
   		for (int j = 1; j <= m; ++j) {
   			for (int d = 0; d < 2; ++d) {
   				int x = i + dx[d], y = j + dy[d];
   				if (x == n + 1 || y == m + 1) continue;
   				if (now.map[i][j] != now.map[x][y]) cnt++;
   			}
   		}
   	}
   	now.q = cnt;
   }
   
   inline void update(G &sta) {
   	int x = abs(rand()) % n + 1, y = abs(rand()) % m + 1;
   	int x1 = abs(rand()) % n + 1, y1 = abs(rand()) % m + 1;
   	G tans = sta;
   	swap(tans.map[x][y], tans.map[x1][y1]);
   	getq(tans);
   	if (tans.q <= sta.q) sta = tans;
   	else if (exp((sta.q - tans.q) / T) > fabs((double)(rand() % 1000) / 1000)) sta = tans;
   	if (tans.q < ans.q) ans = tans;
   }
   
   inline void SA() {
   	T = 1;
   	G nowans = ans;
   	while (T > T_end) {
   		update(nowans);
   		T *= dlt;
   	}
   }
   
   int main() {
   	srand(time(0));
   	scanf("%d%d%d", &n, &m, &c);
   	for (int i = 1; i <= c; ++i)
   		scanf("%d", &p[i]);
   	int now = 1, cnt = 0;
   	for (int i = 1; i <= n; ++i) {
   		for (int j = 1; j <= m; ++j) {
   			ans.map[i][j] = now;
   			cnt++;
   			if (cnt == p[now]) cnt = 0, now++;
   		}
   	}
   	int T = 3;
   	getq(ans);
   	while (T--) SA();
   	for (int i = 1; i <= n; ++i)
   		for (int j = 1; j <= m; ++j)
   			printf("%d%c", ans.map[i][j], " \n"[j == m]);
   	return 0;
   }
   ```

3. 线形基

   ```cpp
   #include <cstdio>
   #include <algorithm>
   #define L 55
   
   using namespace std;
   typedef long long LL;
   
   LL a[L], p[L], n, ans;
   
   int main() {
   	scanf("%lld", &n);
   	for (LL i = 1; i <= n; ++i) {
   		scanf("%lld", &a[i]);
   		for (LL j = L; j >= 0; --j) {
   			if (!(a[i] >> j)) continue;
   			if (!p[j]) {p[j] = a[i]; break;}
   			a[i] ^= p[j];
   		}
   	}
   	for (LL i = L; i >= 0; --i) ans = max(ans, ans ^ p[i]);
   	printf("%lld\n", ans);
   	return 0;
   }
   ```

4. KMP

   ```cpp
   #include <cstdio>
   #include <cstring>
   #define N 1000010
   
   using namespace std;
   char s[N],t[N];
   int next[N];
   
   int main() {
   	scanf("%s%s", s, t);
   	int m = strlen(s), n = strlen(t);
   	next[0] = -1;
   	for (int i = 1; i < n; i++) {
   		int k = next[i - 1];
   		while (k > -1 && t[k + 1] != t[i])
   			k = next[k];
   		if (t[k + 1] == t[i]) k++;
   		next[i] = k;
   	}
   	int k = 0;
   	for (int i = 0; i < m; i++) {
   		while (k > -1 && s[i] != t[k + 1])
   			k = next[k];
   		if (s[i] == t[k + 1]) k++;
   		if (k == n - 1) {
   			k = next[k];
   			printf("%d\n", i - n + 2);
   		}
   	}
   	for (int i = 0; i < n; i++)
   		printf("%d ", next[i] + 1);
   	return 0;
   }
   ```

5. dijkstra

   ```cpp
   #include <cstdio>
   #include <queue>
   #include <cstring>
   #define M 200005
   #define N 100005
   
   using namespace std;
   struct Edge{
   	int to, nxt, len;
   }e[M];
   struct Node{
   	int dis, id;
   	bool operator < (const Node &x) const {
   		return x.dis < dis;
   	}
   };
   int cnt, lst[N], vis[N], dis[N];
   
   priority_queue <Node> heap;
   inline void add(int u, int v, int w) {
   	e[++cnt].to = v;
   	e[cnt].nxt = lst[u];
   	e[cnt].len = w;
   	lst[u] = cnt;
   }
   
   inline void dijkstra(int s) {
   	dis[s] = 0;
   	heap.push((Node){0, s});
   	while (!heap.empty()) {
   		Node now = heap.top();
   		heap.pop();
   		if (vis[now.id]) continue;
   		vis[now.id] = 1;
   		for (int i = lst[now.id]; i; i = e[i].nxt) {
   			if (dis[e[i].to] > dis[now.id] + e[i].len) {
   				dis[e[i].to] = dis[now.id] + e[i].len;
   				heap.push((Node){dis[e[i].to], e[i].to});
   			}
   		}
   	}
   }
   
   int main() {
   	int n, m, s, u, v, w;
   	scanf("%d%d%d", &n, &m, &s);
   	for (int i = 1; i <= m; ++i) {
   		scanf("%d%d%d", &u, &v, &w);
   		add(u, v, w);
   	}
   	memset(dis, 0x3f, sizeof dis);
   	dijkstra(s);
   	for (int i = 1; i < n; ++i) {
   		printf("%d ", dis[i]);
   	}
   	printf("%d\n", dis[n]);
   	return 0;
   }
   ```

6. dinic

   ```cpp
   #include <cstdio>
   #include <iostream>
   #include <algorithm>
   #include <cstring>
   #include <queue>
   #define For(i, l, r) for (int i = (int)(l); i <= (int)(r); ++i)
   #define Rep(i, r, l) for (int i = (int)(r); i >= (int)(l); --i)
   #define Pb push_back
   #define Mp make_pair
   #define Fi first
   #define Se second
   #define Gc getchar
   
   using namespace std;
   typedef long long LL;
   const int MOD = 1000000007;
   const int INF = 0x3f3f3f3f;
   const int N = 10010, M = 100010;
   
   struct Edge {
       int to, nxt, w;
   } e[M << 1];
   int cnt = 1, lst[N], cur[N], dep[N], n, m, s, t;
   queue<int> q;
   
   inline LL read() {
       LL x = 0, f = 1;
       char ch = Gc();
       while (!isdigit(ch) && ch != '-') ch = Gc();
       if (ch == '-')
           f = -1, ch = Gc();
       while (isdigit(ch)) {
           x = (x << 1) + (x << 3) + (ch ^ 48);
           ch = Gc();
       }
       return x * f;
   }
   
   inline void link(int u, int v, int w) {
       e[++cnt].to = v;
       e[cnt].nxt = lst[u];
       e[cnt].w = w;
       lst[u] = cnt;
       e[++cnt].to = u;
       e[cnt].nxt = lst[v];
       e[cnt].w = 0;
       lst[v] = cnt;
   }
   
   inline int bfs() {
       memset(dep, 0, sizeof dep);
       while (!q.empty()) q.pop();
       q.push(s);
       dep[s] = 1;
       For(i, 1, n) cur[i] = lst[i];
       while (!q.empty()) {
           int x = q.front();
           q.pop();
           for (int i = lst[x]; i; i = e[i].nxt) {
               if (e[i].w && !dep[e[i].to]) {
                   q.push(e[i].to);
                   dep[e[i].to] = dep[x] + 1;
                   if (e[i].to == t)
                       return 1;
               }
           }
       }
       return 0;
   }
   
   inline int dfs(int x, int nowflow) {
       if (x == t)
           return nowflow;
       int rest = nowflow, k;
       for (int i = cur[x]; i; i = e[i].nxt) {
           cur[x] = i;
           if (e[i].w && dep[e[i].to] == dep[x] + 1) {
               k = dfs(e[i].to, min(rest, e[i].w));
               if (!k) continue;
               e[i].w -= k;
               e[i ^ 1].w += k;
               rest -= k;
               if (!rest) break;
           }
       }
       return nowflow - rest;
   }
   
   inline LL dinic() {
       int flow = 0;
       LL ans = 0;
       while (bfs())
           while (flow = dfs(s, INF)) ans += flow;
       return ans;
   }
   
   int main() {
       n = read(), m = read(), s = read(), t = read();
       For(i, 1, m) {
           int u = read(), v = read(), w = read();
           link(u, v, w);
       }
       printf("%lld\n", dinic());
       return 0;
   }
   ```

---
`2019, MordorTech Developed by the Fxkkks. All rights reserved.`