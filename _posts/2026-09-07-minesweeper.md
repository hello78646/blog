---
layout: post
title: 扫雷游戏：C 语言基础知识的综合运用（学习模块一）
date: 2026-09-07 09:00:00 +0800
categories: [c-basics]
tags: [扫雷, 二维数组, 函数嵌套, 分支循环, 项目实战]
---

> 本文是我的 **C 语言基础 · 学习模块一**：用自己写的扫雷小项目，把零散的知识点串成一张完整的知识框架网。

## 一、核心概念

扫雷（Minesweeper）是一个非常适合"检验 C 语言基本功"的小项目。它不大，却几乎把 C 语言基础阶段的核心知识点都用了一遍：

- **分支**：判断输入是否合法、踩到雷没、这个格子是不是已经翻过；
- **循环**：初始化棋盘、打印棋盘、布置雷、一遍遍排雷；
- **函数的调用与嵌套**：`test()` → `game()` → `SetMine / DispalyBoard / FindMine` → `get_mine_count()`，一层套一层；
- **数组 + 线性代数的矩阵思维**：用**二维数组**表示一个 9×9 的棋盘矩阵，"计算某个格子周围有几颗雷"本质上就是**对 3×3 子矩阵求和**。

也就是说，扫雷不是一个孤立的小玩具，而是一根把"分支、循环、函数、数组、矩阵"焊在一起的引线。搞懂它，C 语言基础阶段的框架就立住了。

## 二、项目结构与函数调用关系（嵌套）

我的项目分成 3 个源文件，职责清晰：

| 文件 | 职责 |
|------|------|
| `test.c` | 主入口：菜单、游戏流程控制（调 `menu`、`game`） |
| `game.h` | 头文件：宏定义（行/列/雷数）、函数声明 |
| `game.c` | 游戏逻辑：初始化、布置雷、打印、排雷 |

函数调用是**自顶向下、层层嵌套**的：

```text
test()  主流程（do-while 菜单循环 + switch 分支进来游戏）
 ├─ menu()                 打印菜单
 └─ game()                 一局游戏
     ├─ InitBoard()        初始化雷区 / 展示盘（双重 for 循环）
     ├─ SetMine()          随机布置 10 颗雷（while 循环）
     ├─ DispalyBoard()     打印棋盘（双重 for 循环）
     └─ FindMine()         玩家排雷主循环（while + if 分支）
         └─ get_mine_count()   求 3×3 子矩阵的雷数和（静态函数）
```

这就是"**函数调用嵌套**"最朴素、也最清楚的样子：`game()` 内部再调用更小的函数，每个函数只做一件事。

## 三、我的第一版代码

### game.h（头文件，定义棋盘规模与函数接口）

```c
#pragma once
#include <stdio.h>
#include <stdlib.h>
#define ROW 9
#define COL 9
#define ROWS ROW+2
#define COLS COL+2
#define EASY_COUNT 10
void test();//测试函数
void InitBoard(char board[ROWS][COLS], int rows, int cols);//初始化棋盘
void DispalyBoard(char board[ROWS][COLS], int row, int col);//打印棋盘
void SetMine(char mine[ROWS][COLS], int row, int col);//布置雷
void FindMine(char mine[ROWS][COLS], char show[ROWS][COLS], int row, int col);//查找雷
int  get_mine_count(char mine[ROWS][COLS], int x, int y);//获取指定位置周围雷的数量
#include <time.h>
```

### game.c（核心逻辑）

```c
#include "game.h"

void InitBoard(char board[ROWS][COLS], int rows, int cols, int c)//初始化棋盘
{
    for (int i = 0; i < rows; i++)
    {
        for (int j = 0; j < cols; j++)
        {
            board[i][j] = c;
        }
    }
}

void DispalyBoard(char board[ROWS][COLS], int row, int col)//打印棋盘
{
    printf("--------扫雷游戏------\n");
    //先打印列号
    for（int n = 0; n <= col; n++)
    {
        printf("%d ", n);
    }
    printf("\n");
    //再打印行号和棋盘内容
    for (int i = 1; i <= row; i++)
    {
        printf("%d ", i);
        for (int j = 1; j <= col; j++)
        {
            printf("%c ", board[i][j]);
        }
        printf("\n");
    }
    printf("\n");
}

void SetMine(char mine[ROWS][COLS], int row, int col)//布置雷
{
    int count = 10;
    while (count)
    {   //对于行和列的随机数生成，注意范围是1~9
        int x = rand() % row + 1;
        int y = rand() % col + 1;
        if (mine[x][y] == '0')
        {
            mine[x][y] = '1';
            count--;
        }
    }
}

static int get_mine_count(char mine[ROWS][COLS], int x, int y)//计算周围雷数量
{
    int w = 0;
    w = mine[x - 1][y - 1] + mine[x - 1][y] + mine[x - 1][y + 1] + mine[x][y - 1] +
        mine[x][y + 1] + mine[x + 1][y - 1] + mine[x + 1][y] + mine[x + 1][y + 1] - 8 * '0';
    return w;
}

void FindMine(char mine[ROWS][COLS], char show[ROWS][COLS], int row, int col)
{
    int x = 0, y = 0;
    while (win < row * col - EASY_COUNT)
    {
        printf("请输入要排查的行列：");
        scanf("%d %d", &x, &y);
        if (x >= 1 && x <= row && y >= 1 && y <= col)//判断输入是否合法
        {
            if (mine[x][y] == '1')//如果是雷
            {
                printf("很遗憾，你踩到雷了！\n");
                DispalyBoard(mine, row, col);//显示雷的位置
                break;
            }
            else//如果不是雷
            {
                //为了防止重复排查同一个格子的bug
                if (show[x][y] == '*')
                {
                    //计算周围雷的数量
                    int count = get_mine_count(mine, x, y);
                    //将周围雷的数量显示在show棋盘上
                    show[x][y] = count + '0';
                    DispalyBoard(show, row, col);
                    win++;//排查成功的格子数加1
                }
                else
                {
                    printf("该位置已经排查过，请重新输入！\n");
                }
            }
        }
        else
        {
            printf("输入不合法，请重新输入！\n");
        }
    }
    if (win == row * col - EASY_COUNT)
    {
        printf("恭喜你，排雷成功！\n");
        DispalyBoard(mine, row, col);//显示雷的位置
    }
}
```

### test.c（主流程）

```c
#include "game.h"

void menu（）
{
    printf（"**************************");
    printf（"**********1.play**********");
    printf（"**************************");
    printf（"**********0.exit**********");
    printf（"**************************");
}

void game()
{
    char mine[ROWS][COLS] = { 0 };
    char show[ROWS][COLS] = { 0 };

    //初始化棋盘
    InitBoard(mine, ROWS, COLS, '0');
    InitBoard(show, ROWS, COLS, '*');

    //布置雷
    SetMine(mine, row , col);

    //打印棋盘
    DispalyBoard(show, row , col);//只打印中间9*9的区域
}

void test（）
{
    int input;
    srand((unsigned int)time(NULL));
    do
    {
        menu();
        printf("请输入：");
        scanf("%d", &input);
        switch（input）
        {
            case 1:
                // play game
                game();
                break;
            case 0:
                // exit
                break;
            default:
                printf("无效输入！\n");
                break;
        }
    }while（input != 0）；
}
```

## 四、知识点是怎么被"焊"在一起的

### 1. 分支（if / else / switch）
- `test()` 里的 `switch(input)`：根据玩家的输入 `1 / 0 / 其他` 走不同分支；
- `FindMine()` 里的三层 `if`：先判"输入合法吗"→ 再判"是不是雷"→ 再判"是否已翻过"。层层把关，是典型的分支嵌套。

### 2. 循环（for / while / do-while）
- `for`：给矩阵**逐格初始化**（`InitBoard`）和**逐行逐列打印**（`DispalyBoard`）；
- `while(count)`：布置雷时**循环到 10 颗不重不漏地放完**；
- 主流程用 `do-while`：保证**菜单至少显示一次**，并循环到输入 0 才退出——`do-while` 的"先执行后判断"在这里最合适。

### 3. 函数的调用与嵌套
`test()` → `game()` → `FindMine()` → `get_mine_count()`，自上而下分层。好处是每一层只管一件事，接口清晰、可以单独复现和测试。头文件 `game.h` 就是这份"接口清单"。

### 4. 数组 + 线性代数的矩阵思维（本项目的精髓）
- **`board[ROWS][COLS]` 就是一张矩阵**，`ROWS=ROW+2` 是为了在 9×9 外**多围一圈**，这样算边界格子时 `x-1 / x+1 / y-1 / y+1` **不会越界**——用"加光环"避免了矩阵边界的特判；
- **"周围有几颗雷" = 3×3 子矩阵求和**：取 `(x-1..x+1, y-1..y+1)` 这 9 个元素的和，再减去中心把自己排除即可（代码里是统计 8 个邻居）；
- 用 `char` 存 `'0'`（无雷）/ `'1'`（有雷），求周围雷数时 `... - 8 * '0'` 这个小技巧：ASCII 中 `'0'=48`，把 8 个字符的和减去 `8×48`，剩下的就是雷的个数——用**字符编码**直接当整数算，一行搞定，很妙。

> 矩阵思维的核心一句话：**棋盘 = 二维数组 = 一张矩阵，统计邻居 = 局部子矩阵求和。** 后面学线性代数里的行列式、矩阵运算时，思路上是完全相通的。

## 五、易错点（我踩过的坑 & 当前草稿待完善）

这份第一版草稿能跑通主要思路，但还藏着几个问题——正好都是"初学者最容易犯"的代表：

| 类别 | 问题 | 说明 / 修复方向 |
|------|------|----------------|
| 编译错误 | **全角括号** `（ ）` | `menu（）`、`printf（"..."）`、`switch（input）`、`while（input != 0）；` 用了中文全角括号，编译器无法识别，改成半角 `()` 即可 |
| 编译错误 | **函数声明与定义不一致** | `game.h` 里 `InitBoard` 声明为 2 个参数，`game.c` 却定义为 4 个参数（多了 `int c`），声明/定义必须统一 |
| 编译错误 | **未定义变量 `row` / `col`** | `game()` 里 `SetMine(mine, row , col)`、`DispalyBoard(show, row , col)` 的 `row`、`col` 没定义，应改用宏 `ROW`、`COL` |
| 编译错误 | **`win` 未声明** | `FindMine()` 里直接用了 `win`，它既没在函数内定义，也没在全局定义，需要先声明并初始化（如 `int win = 0;`） |
| 链接错误 | **函数名拼写 / 大小写不一致** | 调用了 `DisplayBoard(...)`，实际函数名是 `DispalyBoard`（`play` 拼成了 `paly`），大小写也对不上 |
| 逻辑 / 体验 | 缺少"空白自动展开" | 主流扫雷点开空白格会**递归/洪泛展开**整片无雷区，本版只能一个个手动翻，是最值得加一的增强 |
| 设计 | `get_mine_count` 在 `.c` 里是 `static`，又出现在头文件声明里 | `static` 函数只在源文件可见，头文件声明它意义不大，二选一保持一致 |

**修复思路（按顺序做就通了）：** 先把全角括号全部换成半角 → 统一 `InitBoard` 的参数 → 把 `row/col` 换成宏 `ROW/COL` → 给 `win` 补声明 → 统一函数名为 `DispalyBoard`。改完这五点，程序就能干净地编译跑起来。

## 六、时空复杂度

- **初始化 / 打印**：双重循环遍历 `ROW×COL`，每次 `O(row×col)`；
- **布置雷**：`while(count)` 平均放 10 颗雷，每颗随机定位 `O(1)`，整体约 `O(雷数)`，本例 10 可视为常数；
- **排雷**：每次玩家输入后 `get_mine_count` 只看 8 个邻居，`O(1)`；全过程最多翻 `row×col - 雷数` 个格子，总付出 `O(row×col)`。

整体上，扫雷的时空开销都集中在"遍历棋盘"这个二维数组上，`O(n²)` 的量级（这里 `n=row×col`）。

## 七、自测题（先别看答案）

1. 为什么棋盘要定义成 `ROW+2 × COL+2`，而不是直接用 `9×9`？
2. `get_mine_count` 里 `- 8 * '0'` 那一步，去掉它会得到什么结果？为什么？
3. `do-while` 换成 `while` 写菜单，效果有什么差别？
4. 如果要改成"难度可选（10/20/40 颗雷）"，改哪些地方最方便？

<details><summary>小提示</summary>
2. 去掉后得到的是 8 个字符（`'0'`/`'1'` 的 ASCII 码）的和，而非雷的个数；必须减掉 `8*48` 才能换算成整数。
</details>

## 八、为什么把它放在"模块一"

我正在把 C 语言基础按**模块**来学，我之后会继续细分。扫雷作为**模块一**，是因为它天然地锻炼了 C 语言基础阶段最重要的四种能力：

1. 逻辑：分支 + 循环的**组合与嵌套**；
2. 抽象：把流程拆成**层层调用的函数**；
3. 数据：用**二维数组（矩阵）**组织游戏状态；
4. 工程：**多文件分离**（`.h` 声明接口 + `.c` 实现），贴近真实项目写法。

啃透这一个 100 多行的项目，等于把 C 语言基础阶段的"钢筋骨架"浇筑了一遍。后续在这个基础上，我会继续补充 C 语言、数据结构、算法的更多模块。