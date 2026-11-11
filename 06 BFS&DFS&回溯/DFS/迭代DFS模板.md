```cpp
1. visited 全部初始化为 false（未访问）
2. visited[start] = true;  st.push(start);
3. while (!st.empty()):
4.     u = st.top(); st.pop();
5.     for each neighbor v of u:
6.         if (v 越界/是墙/已访问) continue;
7.         visited[v] = true;
8.         st.push(v);
```


