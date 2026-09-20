---
layout: post
title: C 语言内存函数：memcpy / memmove / memset / memcmp 学习笔记（学习模块 · 内存操作）
date: 2026-09-20 10:00:00 +0800
categories: [c-basics]
tags: [内存函数, memcpy, memmove, memset, memcmp]
---

> 这是我学习 C 语言内存函数的笔记（原自手机笔记整理），内容为我对它们的理解。代码和层级经过了整理，观点保持原样。

## 1. memcpy 的使用和模拟实现

内存拷贝。

```c
void *memcpy(void *dest, const void *src, size_t num);
```

- 与 `strncpy` 略不同，区别在于**拷贝的数据类型**（word 层面）
- **`memcpy` 处理的是不重叠的两块内存数据的拷贝**；如果内存重叠，拷贝要用 **`memmove`**

> @练习：附上 memcpy 的模拟实现。

## 2. memmove

```c
void *memmove(void *dest, const void *src, size_t num);
```

- 也是内存拷贝，处理**源与目标内存区域重叠**的情况
- 拷贝的单元是**字节**

## 3. memset 的使用

内存设置。

```c
void *memset(void *ptr, int value, size_t num);
```

- 将内存中的值**以字节为单位**进行设置
- `ptr`：指向的是被设置的数据
- `num`：数据的大小（**字节数**）

## 4. memcmp

按字节比较内存。

```c
int memcmp(const void *ptr1, const void *ptr2, size_t num);
```

- 比较从 `ptr1` 和 `ptr2` 指针指向的位置开始，向后的 `num` 个字节
- **根据内存中的字节数据来比较**
- 返回值：与 `strcmp` 类似（根据内存字节内容判断大小关系）