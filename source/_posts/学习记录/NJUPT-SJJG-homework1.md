---
title: 南邮数据结构作业1
description: 将单链表分解为奇数下标列表和偶数下标列表
categories: 学习记录
tags:
  - 学习记录
abbrlink: f93c4f64
date: 2025-11-01 18:52:09
---

- **要求**
  设计算法，将一个带头结点的单链表A分解为两个带头结点的单链表A和B，
  使得A表中含有原表中序号为奇数的元素，而B表中含有原表中序号为偶数的元素
  且保持其相对顺序不变
* **算法思想**
  要求是将单链表中序号奇数和偶数的元素分到两个单链表中，
  所以创建两个带头结点的单链表M和N，其中M储存原来序号为奇数的元素，N储存原来序号为偶数的元素
  然后遍历原链表A，当序号为奇数的时候，将对应的元素存储到M，序号为偶数的时候存储到N
  添加元素的位置均为链表的末尾
  
  要考虑元素之间相对顺序不变，所以还要另外定义两个int变量m和n代表链表M和N的下标
  每添加一个元素，下标就加1

**结点定义**

```c
typedef struct node
{
 ElemType element; //结点的数据域
 struct node* link; //结点的指针域
}Node;
```

**带头结点的单链表定义**，head作为头节点

```c
typedef struct headerList
{
 Node* head;
 int n; //单链表的长度n
}HeaderList;
```

**初始化运算**：生成空表

```c
int Init(HeaderList* h)
{
 h->head = (Node*)malloc(sizeof(Node)); //生成表头结点
 if (!h->head)            //表头结点为空返回false
    return 0;

 h->head->link = NULL;
 h->n = 0;
 return 1;
}
```

**查找运算**：根据下标i查找元素，并通过x返回值

```c
int Find(HeaderList L, int i, ElemType* x)
{
 Node* p;
 int j;
 if (i < 0 || i > L.n-1)        //对i进行越界检查
     return 0;

 p = L.head->link;
 for (j = 0; j < i; j++)            //从头结点开始查找下标i的元素
     p = p->link;

 *x = p->element;                //通过x返回元素值（传地址）
 return 1;
}
```

**插入运算**：根据下标i插入元素x

```c
int Insert(HeaderList* h, int i, ElemType x)
{
 Node* p, * q;
 int j;
 if (i<-1 || i>h->n - 1)
     return 0;

 p = h->head;                      //从头结点开始找a(i)元素所在的结点p
 for (j = 0; j <= i; j++)
     p = p->link;

 q = (Node*)malloc(sizeof(Node));  //生成新结点q

 q->element = x;                    //将元素x赋值到新结点q
 q->link = p->link;              //将新结点q插在p之后
 p->link = q;
 h->n++;
 return 1;
}
```

**输出运算**：输出链表的所有元素

```c
int Output(HeaderList* L)
{
 Node* p;
 if (!L->n)                        //判空运算
     return 0;

 p = L->head->link;
 while (p)                        //依次输出每个元素
 {
     printf("%d ", p->element);
     p = p->link;
 }

 return 1;
}
```

**主程序步骤**
1.先生成原链表A和待存储链表M和N，以及对应的下标m和n
  (M储存原来序号为奇数的元素，N储存原来序号为偶数的元素)
2.使用查找运算遍历链表A，用变量x存储返回值
3.用if语句根据下标的奇偶性将a插入链表M和N中
4.当链表A遍历完成后，输出链表A、M和N，验证程序是否有错误

```c
int main()
{
 HeaderList A, M, N;
 int i, m = 0, n = 0;
 int x;

 Init(&A);        //初始化链表
 Init(&M);
 Init(&N);

 for (i = 0; i < 9; i++)        //给链表A插入0~9
     Insert(&A, i - 1, i);
 printf("原List:");    //输出链表A
 Output(&A);

 for (i = 0; i < 9; i++)
 {
     Find(A, i, &x);            //用查找运算遍历链表A，用x存储返回值
     if (i % 2 == 1)
     {
         Insert(&M, m - 1, x);    //下标为奇数时，将元素添加至链表M
         m++;    //m表示链表M的下标
     }
     else
     {
         Insert(&N, n - 1, x);    //下标为偶数时，加元素添加至链表N
         n++;    //n表示链表N的下标
     }
 }

 printf("\nList（奇数下标）:");
 Output(&M);
 printf("\nList（偶数下标）:");
 Output(&N);
}
```

**输出结果**

```bash
原List:0 1 2 3 4 5 6 7 8
List（奇数下标）:1 3 5 7
List（偶数下标）:0 2 4 6 8
E:\Buildings\c\数据结构与算法作业\第二章作业\x64\Debug\第二章作业.exe (进程 12624)已退出，代码为 0 (0x0)。
要在调试停止时自动关闭控制台，请启用“工具”->“选项”->“调试”->“调试停止时自动关闭控制台”。
按任意键关闭此窗口. . .
```
