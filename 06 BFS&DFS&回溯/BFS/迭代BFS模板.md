```cpp
1. dist 全部初始化为 -1（未访问）
2. dist[start] = 0;  q.push(start);
3. while (!q.empty()):
4.     u = q.front(); q.pop();
5.     for each neighbor v of u:
6.         if (v 越界/是墙/已访问) continue;
7.         dist[v] = dist[u] + 1;
8.         q.push(v);
```

Q.为什么BFS找最短路而DFS不行？
BFS按层扩展，到达即最短，DFS优先往里钻，再回溯

BFS： 队列 Queue  **先进先出**  FIFO (First In First Out) 

排队买票，先来的先服务 
DFS：栈 Stack  **后进先出** LIFO (Last In First Out) 

 叠盘子，最后放的最先拿 

从而DFS适合递归，BFS一般只用循环
DFS也可以循环，那样更加稳定

记忆化DFS常常可以写成DP
