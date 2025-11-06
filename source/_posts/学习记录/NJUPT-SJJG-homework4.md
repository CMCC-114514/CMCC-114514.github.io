---
title: 南邮数据结构作业 - 第四章
abbrlink: 86c33f9b
date: 2025-11-06 22:23:46
description: 稀疏矩阵的转置算法
categories: 学习记录
tags:

---

#### 给出如下稀疏矩阵，请设计一种高效的转置算法,根据设计思想,采用C/C++语言实现,给出注释;并给出运行及结果截图。

![](/images/86c33f9b/p1.png)

- **算法思想**

假设三元组表按照行优先存储稀疏矩阵的非零元素，稀疏矩阵A进行转置后的结果存储到稀疏矩阵B中。
实现快速转置算法需要借助两个一维数组**num**和**sum**，这两个数组的长度都为n（矩阵A的列数）

数组**num**存储矩阵每一列的非零元素个数，通过三元组表统计原矩阵每一列的非零元素个数，
数组的下标i就代表原矩阵的第i列，对应的元素num[i]就代表非零元素个数

数组**sum**的元素 **sum[i]** 统计稀疏矩阵A中列号从**0**到**i-1**的非零元素总个数，
**sum[i]** 的值决定了原矩阵中第i列的第一个非零元素在三元组表中的位置

因此只需要对稀疏矩阵A的行三元组表进行一次扫描就可以快速转置

- **代码实现**

**三元组和三元组表定义**

稀疏矩阵可以转化为三元组表来压缩存储。三元组表记录稀疏矩阵中非零元素的三个特征：行坐标、列坐标和元素值。

```c
typedef struct Term
{
    int col, row;        //行坐标row和列坐标col
    elemtype value;        //元素值
}term;

typedef struct SparseMatrix
{
    int m, n, t;        //原矩阵的行数m和列数n，t为非零元素个数
    term table[maxsize];    //三元组表主体
}sparsematrix;
```

**算法的代码实现**

```c
sparsematrix transpose(sparsematrix a)
{
    int num[C] = { 0 };        //定义辅助数组num和sum，长度均为稀疏矩阵a的列数
    int sum[C] = { 0 };

    for (int i = 0; i < a.t; i++)
    {
        //数组num存储矩阵每一列的非零元素个数
        //通过三元组表a统计原矩阵每一列的非零元素个数
        //数组的下标i就代表原矩阵的第i列，对应的元素num[i]就代表非零元素个数
        num[a.table[i].col]++;        
    }

    for (int i = 1; i < C; i++)
    {
        //数组sum存储矩阵a中列号从0到i-1的所有非零元素的个数
        //比如sum[3]就存储了列号从0到2的所有非零元素的个数
        //sum[i]的值决定了原矩阵中第i列的第一个非零元素在三元组表中的位置
        //比如sum[2]=4，说明第2列第一个非零元素在三元组表中的下标是4
        sum[i] = sum[i - 1] + num[i - 1];
    }

    sparsematrix b;
    b.m = C;
    b.n = R;
    b.t = 7;

    for (int i = 0; i < a.t; i++)
    {
        int index = sum[a.table[i].col]++;
        b.table[index].col = a.table[i].row;
        b.table[index].row = a.table[i].col;
        b.table[index].value = a.table[i].value;
    }

    return b;

}
```

**main函数**

```c
#include <stdio.h>
#include <stdlib.h>
typedef int elemtype;
#define maxsize 100

#define C 6        //矩阵的行数R和列数C
#define R 6

int main()
{
    sparsematrix s;        //定义三元组表s

    s.m = R;            //设置行数和列数，非零元素个数为7
    s.n = C;
    s.t = 7;

    int m[R][C] = { 0 };    //定义稀疏矩阵m并赋值
    m[0][0] = -5;
    m[0][1] = -2;
    m[1][3] = -6;
    m[3][1] = -3;
    m[4][0] = -7;
    m[4][3] = -4;
    m[5][2] = -1;

    int k = 0;
    for (int i = 0; i < 6; i++)        //将稀疏矩阵存储到三元组表中
    {
        for (int j = 0; j < 6; j++)
        {
            if (m[i][j] != 0)
            {
                s.table[k].row = i;
                s.table[k].col = j;
                s.table[k].value = m[i][j];
                k++;
            }
        }
    }

    //输出转置前的三元组表
    printf("转置前：\n");
    for (int i = 0; i < s.t; i++)
    {
        printf("%d %d %d\n", s.table[i].row, s.table[i].col, s.table[i].value);
    }

    s = transpose(s);
    printf("\n转置后：\n");
    for (int i = 0; i < s.t; i++)
    {
        printf("%d %d %d\n", s.table[i].row, s.table[i].col, s.table[i].value);
    }
}
```

- **运行结果**

```bash
转置前：
0 0 -5
0 1 -2
1 3 -6
3 1 -3
4 0 -7
4 3 -4
5 2 -1

转置后：
0 0 -5
0 4 -7
1 0 -2
1 3 -3
2 5 -1
3 1 -6
3 4 -4

E:\Buildings\c\数据结构与算法作业\第四章作业\x64\Debug\第四章作业.exe (进程 34352)已退出，代码为 0 (0x0)。
要在调试停止时自动关闭控制台，请启用“工具”->“选项”->“调试”->“调试停止时自动关闭控制台”。
按任意键关闭此窗口. . .
```
