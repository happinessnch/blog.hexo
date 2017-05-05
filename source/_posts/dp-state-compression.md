---
title: DP状态压缩  
date: 2017-05-05 21:49:26
tags: algorithm
---

例：炮兵阵地　

问题描述：在一个n*m的图中排放士兵，每一个士兵的攻击范围是横竖2个单元格，也就是一个宽高都为3的十字型，任何一个士兵都不允许站在其他士兵的攻击范围之内；另外，地图上‘H’表示山也不允许放置士兵，问最多可以放置多少个兵士。

详细描述：http://poj.org/problem?id=1185

状态转移方程：dp[i][s1][s2]=max(dp[i-1][s2][s3] + soldier_num[s1], dp[i][s1][s2]);

dp[i][s1][s2]表示：在第i层状态为s1，第i-1层状态为s2时，前i层能摆放的最大士兵数。

求解过程：遍历第i-2层的所有状态，找出其中不与第i层s1状态和第i-1层s2状态相冲突的所有状态，再从中找出最大士兵数，加上第i层s1状态最多可放置的士兵数，求得的结果则为dp[i][s1][s2]的值。

```
#include<stdio.h>  
#include<string.h>  
#define HEIGHT_SIZE 101  
#define MAX_SIZE 70  
#define nmax(a, b) (a > b ? a : b)  
  
int map_state[HEIGHT_SIZE];  
int soldier_state[MAX_SIZE];  
int soldier_num[MAX_SIZE];  
int dp[HEIGHT_SIZE][MAX_SIZE][MAX_SIZE];  
  
int n, m;  
int state_num;  
  
int get_num(int num) {  
  int cnt = 0;  
  while (num) {  
    num &= (num - 1);  
    cnt++;  
  }  
  return cnt;  
}  
  
void init() {  
  memset(map_state, 0, sizeof(map_state));  
  memset(soldier_state, 0, sizeof(soldier_state));  
  memset(soldier_num, 0, sizeof(soldier_num));  
  memset(dp, 0, sizeof(dp));  
  
  scanf("%d %d", &n, &m);  
  if (!n) { printf("0\n"); return;}  
  char buf[20];  
  
  for (int i = 0  ; i < n ; ++i) {  
    scanf("%s", buf);  
    for (int j = 0 ; j < m ; ++j) {  
      map_state[i] <<= 1;  
      map_state[i] += buf[j] == 'H' ? 1 : 0;  
    }  
  }  
  
  int lim = 1<<m;  
  state_num = 0;  
  for (int i = 0 ; i < lim ; ++i) {  
    if (i & i << 1 || i & i << 2) {  
      continue;  
    }  
    soldier_state[state_num] = i;  
    soldier_num[state_num++] = get_num(i);  
  }  
}  
  
int get_max() {  
  int _max = 0;  
  for (int i = 0 ; i < state_num ; ++i) {  
    for (int j = 0  ; j < state_num ; ++j) {  
      _max = nmax(dp[n - 1][i][j], _max);   
    }  
  }  
  return _max;  
}  
  
int solve_dp() {  
  for (int i = 0 ; i < state_num ; ++i) {  
    if (soldier_state[i] & map_state[0]) {  
      continue;  
    }  
    dp[0][i][0] = soldier_num[i];  
  }  
  if (n < 2) {  
    return get_max();  
  }  
  for (int i = 0 ; i < state_num ; ++i) {  
    if (soldier_state[i] & map_state[1]) {  
      continue;  
    }  
    for (int j = 0 ; j < state_num ; ++j) {  
      if (soldier_state[j] & soldier_state[i] ||  
          soldier_state[j] & map_state[0]) {  
        continue;  
      }  
      dp[1][i][j] = nmax(dp[0][j][0] + soldier_num[i], dp[1][i][j]);  
    }  
  }　　　　// 求得0,1层的最大士兵数  
  for (int i = 2 ; i < n ; ++i) {  
    for (int s1 = 0 ; s1 < state_num ; ++s1) {  
      if (map_state[i] & soldier_state[s1]) {  
        continue;  
      }  
      for (int s2 = 0 ; s2 < state_num ; ++s2) {  
        if (map_state[i - 1] & soldier_state[s2] ||  
            soldier_state[s2] & soldier_state[s1]) {  
          continue;  
        }  
        for (int s3 = 0 ; s3 < state_num ; ++s3) {  
          if (map_state[i - 2] & soldier_state[s3] ||  
              soldier_state[s2] & soldier_state[s3] ||  
              soldier_state[s1] & soldier_state[s3]) {  
            continue;  
          }  
          dp[i][s1][s2] = nmax(dp[i - 1][s2][s3] + soldier_num[s1], dp[i][s1][s2]);  
        }  
      }  
    }  
  }   
  return get_max();  
}  

int main() { 
  init();  
  if (n != 0) printf("%d\n", solve_dp());  
  return 0;  
}  
```
