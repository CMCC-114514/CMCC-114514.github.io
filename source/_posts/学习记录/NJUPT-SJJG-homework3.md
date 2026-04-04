---
title: 南邮数据结构作业 - 第三章
description: 求解汉诺塔问题
categories: 学习记录
tags:
  - 学习记录
  - 数据结构
  - C语言
abbrlink: 70f710ab
date: 2025-11-05 22:19:35
---

#### 用递归和非递归算法分别求解汉诺塔问题

> 这里首先声明，作者只理解了递归算法，非递归算法是用ai生成的

- **汉诺塔问题的递归算法**

先分成两种情况：
1.如果这个塔只含有一个盘子，则直接将盘子移动到目标盘
2.如果这个塔含有n个盘子，那么将问题从“将最大的盘子n通过辅助柱移动到目标柱”
  转化为“将次大的盘子n-1通过辅助柱移动到目标柱”
然后对于情况二，算法步骤为：

将n-1个盘子从起始柱移动到辅助柱

将最大的盘子从起始柱移动到目标柱

将n-1个盘子从辅助柱移动到目标柱

- **递归算法的代码实现**

```c
void hanoi1(int n, char from, char to, char dest) {
    // 情况1：如果只有一个盘子，直接移动
    if (n == 1) {
        printf("盘子 1 从 %c 移动到 %c\n", from, to);
        return;
    }

    // 情况2的递归步骤：
    // 1. 将n-1个盘子从起始柱移动到辅助柱
    hanoi1(n - 1, from, dest, to);

    // 2. 将最大的盘子从起始柱移动到目标柱
    printf("盘子 %d 从 %c 移动到 %c\n", n, from, to);

    // 3. 将n-1个盘子从辅助柱移动到目标柱
    hanoi1(n - 1, dest, to, from);

}
```

- **汉诺塔问题的非递归算法**

汉诺塔问题的非递归算法需要借助堆栈来实现
用三个堆栈代表三个柱子，然后根据盘子数量的奇偶性来决定盘子第一步的移动方向
偶数个盘子：起始柱->辅助柱, 起始柱->目标柱, 辅助柱->目标柱
奇数个盘子：起始柱->目标柱, 起始柱->辅助柱, 目标柱->辅助柱

- **非递归算法的代码实现**

**定义堆栈**

```c
typedef int elemtype;
typedef struct stack{
    elemtype* element;
    int top;
    int maxsize;
} Stack;
```

**堆栈初始化**

```c
Stack* createStack(int maxsize) {
    Stack* stack = (Stack*)malloc(sizeof(Stack));
    stack->element = (int*)malloc(sizeof(int) * maxsize);
    stack->top = -1;
    stack->maxsize = maxsize;
    return stack;
}
```

**移动盘子的辅助函数**

```c
void moveDisk(Stack* stacks[], int fromIdx, int toIdx, char fromChar, char toChar, char auxChar) {
    char poleNames[3] = { fromChar, auxChar, toChar };

    // 检查从fromIdx移动到toIdx是否合法
    if (stacks[fromIdx]->top >= 0 &&
        (stacks[toIdx]->top < 0 ||
            stacks[fromIdx]->element[stacks[fromIdx]->top] < stacks[toIdx]->element[stacks[toIdx]->top])) {

        int disk = stacks[fromIdx]->element[stacks[fromIdx]->top--];
        stacks[toIdx]->element[++stacks[toIdx]->top] = disk;
        printf("将盘子 %d 从 %c 移动到 %c\n", disk, poleNames[fromIdx], poleNames[toIdx]);
    }
    // 检查从toIdx移动到fromIdx是否合法
    else if (stacks[toIdx]->top >= 0 &&
        (stacks[fromIdx]->top < 0 ||
            stacks[toIdx]->element[stacks[toIdx]->top] < stacks[fromIdx]->element[stacks[fromIdx]->top])) {

        int disk = stacks[toIdx]->element[stacks[toIdx]->top--];
        stacks[fromIdx]->element[++stacks[fromIdx]->top] = disk;
        printf("将盘子 %d 从 %c 移动到 %c\n", disk, poleNames[toIdx], poleNames[fromIdx]);
    }

}
```

**非递归算法实现**

```c
void hanoi2(int n, char from, char to, char aux) {
    if (n <= 0) return;

    // 使用三个堆栈来表示三个柱子
    Stack* stacks[3];
    stacks[0] = createStack(n);  // 起始柱A
    stacks[1] = createStack(n);  // 辅助柱B  
    stacks[2] = createStack(n);  // 目标柱C

    // 初始化起始柱，最大的盘子在底部
    for (int i = n; i >= 1; i--) {
        stacks[0]->element[++stacks[0]->top] = i;
    }

    // 总移动次数
    int totalMoves = (1 << n) - 1;  // 2^n - 1

    // 根据盘子数量的奇偶性决定第一步的移动方向
    char src = 0, dest, temp;

    if (n % 2 == 0) {
        // 偶数个盘子：A->B, A->C, B->C
        dest = 1;
        temp = 2;
    }
    else {
        // 奇数个盘子：A->C, A->B, C->B
        dest = 2;
        temp = 1;
    }

    for (int move = 1; move <= totalMoves; move++) {
        if (move % 3 == 1) {
            // 在src和dest之间移动
            moveDisk(stacks, src, dest, from, to, aux);
        }
        else if (move % 3 == 2) {
            // 在src和temp之间移动
            moveDisk(stacks, src, temp, from, to, aux);
        }
        else {
            // 在dest和temp之间移动
            moveDisk(stacks, dest, temp, from, to, aux);
        }
    }

}
```

**main函数**

```c
int main() {
    int n;

    printf("请输入汉诺塔的盘子数量：");
    scanf_s("%d", &n);

    printf("汉诺塔的移动步骤：\n");
    hanoi1(n, 'A', 'C', 'B');  // 递归算法：A是起始柱，C是目标柱，B是辅助柱
    //hanoi2(n, 'A', 'C', 'B');   // 非递归算法：A是起始柱，C是目标柱，B是辅助柱

    return 0;

}
```

- **运行结果（递归算法和非递归算法的结果相同）**

```bash
请输入汉诺塔的盘子数量：4
汉诺塔的移动步骤：
盘子 1 从 A 移动到 B
盘子 2 从 A 移动到 C
盘子 1 从 B 移动到 C
盘子 3 从 A 移动到 B
盘子 1 从 C 移动到 A
盘子 2 从 C 移动到 B
盘子 1 从 A 移动到 B
盘子 4 从 A 移动到 C
盘子 1 从 B 移动到 C
盘子 2 从 B 移动到 A
盘子 1 从 C 移动到 A
盘子 3 从 B 移动到 C
盘子 1 从 A 移动到 B
盘子 2 从 A 移动到 C
盘子 1 从 B 移动到 C

E:\Buildings\c\数据结构与算法作业\第三章作业\x64\Debug\第三章作业.exe (进程 36636)已退出，代码为 0 (0x0)。
要在调试停止时自动关闭控制台，请启用“工具”->“选项”->“调试”->“调试停止时自动关闭控制台”。
按任意键关闭此窗口. . .
```
