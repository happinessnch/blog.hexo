---
title: 图论 Lowest Common Ancestor
date: 2016-05-26 21:11:19
tags: algorithm
categories: algorithm
---
## 在线倍增法 
如求A，B两点的LCA，先计算出各个结点的深度depth[]，然后，通过递推公式求出各个结点的2次方倍的祖先ancestor[]，假设depth[A] > depth[B]，则找到depth[ancestor[A][i]] == depth[B]也就是A的某一祖先与B深度相同，然后，u = ancestor[A][i]，通过ancestor[u][i] 与ancestor[B][i]比较找出LCA。

ancestor[a][b] 表示与a的距离为2^b的祖先，则ancestor[a][0]表示为a的父亲。如 a->b->c->d->e，a为根， 则ancestor[e][2] 为a。

递推公式：ancestor[a][b] = ancestor[ancestor[a][b - 1]][b - 1]

### Template
```
#define NODE_SIZE 10010  
#define ANC_SIZE 15  
// ANC_SIZE = Log(2, NODE_SIZE)  
int ancestors[NODE_SIZE][ANC_SIZE];
int depth[NODE_SIZE];

vector<int> childs[NODE_SIZE];

int LCAncestor(int u, int v)
{
	if (depth[u] < depth[v]) 
	{ swap(u, v); }

	for (int i = ANC_SIZE; i >= 0; --i) 
	{
		if (depth[ancestors[u][i]] >= depth[v]) 
		{
			u = ancestors[u][i];
			if (depth[u] == depth[v]) 
			{ break; }
		}
	}

	if (u == v) 
	{ return u; }

	for (int i = ANC_SIZE - 1; i >= 0; --i) 
	{
		if (ancestors[u][i] != ancestors[v][i]) 
		{
			u = ancestors[u][i];
			v = ancestors[v][i];
		}
	}

	return ancestors[u][0];
}

void GetDepth(int rt, int dep) 
{
	depth[rt] = dep;
	for (int i = 0; i < childs[rt].size(); ++i) 
	{
		GetDepth(childs[rt][i], dep + 1);
	}
}


GetDepth(root, 1);
for (int i = 1; i < ANC_SIZE; ++i) 
{
	for (int j = 1; j <= n; ++j) 
	{
		ancestors[j][i] = ancestors[ancestors[j][i - 1]][i - 1];
	}
}

printf("%d\n", LCAncestor(u, v));
```
---

## 离线Tarjan算法
对于一棵树求A，B两点的最小公共祖先。从根节点DFS，在回溯时将当前点加入集合，如搜索到A时判断B是否已在集合中，如B已在集合中则合集中最浅的结点为A，B的最小公共祖先；如B不在集合中，则继续搜索B。

离线算法Tarjan：在O(n + q)时间内，一次性计算出所有请求的LCA，但结果是无序的，得到结果的次序与请求发出的次序不相同。（n 为结点数，q为询问数）

### Template
```cplusplus
#define NODE_SIZE 1000  
  
int ans[NODE_SIZE];  
vector<int> querys[NODE_SIZE];  
vector<int> childs[NODE_SIZE];  
  
bool visited[NODE_SIZE];  
int parents[NODE_SIZE];  
  
int FindSet(int x) 
{  
    if (parents[x] == x) 
    {   return x;   }  
    
    return parents[x] = FindSet(parents[x]);  
}  

void UnionSet(int rt, int cd) 
{  
    parents[FindSet(cd)] = FindSet(rt);  
}  
  
void TarjanLCA(int rt) 
{  
    for (int i = 0 ; i < childs[rt].size(); ++i) 
    {  
        TarjanLCA(childs[rt][i]);  
        UnionSet(rt, childs[rt][i]);  
    }  
    
    visited[rt] = true;  
      
    for (int i = 0 ; i < querys[rt].size() ; ++i) 
    {  
        if (visited[querys[rt][i]]) 
        {  
           ;// The LCA(rt, querys[rt][i]) is "FindSet(querys[rt][i])"  
        }  
    }  
}  
```
