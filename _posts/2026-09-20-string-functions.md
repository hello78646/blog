---
layout: post
title: 字符函数和字符串函数：C 语言字符串处理学习笔记（学习模块 · 字符串）
date: 2026-09-20 09:00:00 +0800
categories: [c-basics]
tags: [字符串, strlen, strcpy, strcmp, strcat, strstr, strtok]
---

> 这是我学习 C 语言字符函数和字符串函数的笔记（原自手机笔记整理），内容为我对它们的理解，以**使用要点 + 边界条件**为主线整理。代码和层级经过了整理，观点保持原样。

## 一、字符分类函数

用于判断一个字符属于哪种类型：**是真返回非 0，是假返回 0**。

> 例：`isalpha()` 判断字母、`isdigit()` 判断数字、`isspace()` 判断空白等，需 `#include <ctype.h>`。

## 二、字符转换函数

```c
int tolower (int c);  // 将传进去的大写字母转小写
int toupper (int c);  // 将传进去的小写字母转大写
```

## 三、strlen 的使用和模拟实现

计算字符串的长度。

- 返回值是 `size_t`，**是无符号的**，运算时应强制类型转换为 `int`
- 需 `#include <string.h>`

> @练习：附上 strlen 的模拟实现。

## 四、strcpy 的使用和模拟实现

```c
strcpy(char *destination, const char *source);
```

字符串拷贝（把源字符串拷贝到目标空间）。使用要点：

- **源字符串必须以 `\0` 结束**
- 会将源字符串中的 `\0` **一起拷贝**到目标空间
- 目标空间必须**足够大**，确保能放得下源字符串
- 目标空间必须**可修改**
- 返回值是 `destination` 空间的**起始地址**

> @练习：附上 strcpy 的模拟实现。

## 五、strcat 的使用和模拟实现

```c
strcat(char *destination, const char *source);  // 需 #include <string.h>
```

字符串追加拼接。使用要点：

- 返回类型是 `char *`，值是目标空间的**起始地址**
- 源字符串必须有 `\0`
- 目标字符串中也得有 `\0`，否则无法知道追加从哪开始；**先替换掉 `\0` 追加，之后再补 `\0`**
- 目标空间必须足够大、可修改

**注意（自我追加的坑）：** 模拟实现时如果自我追加，会进入**无限循环**——因为目标空间里的 `\0` 被覆盖了。但是**库的 `strcat` 可以实现自我追加**。

> @练习：附上 strcat 的模拟实现。

## 六、strcmp 的使用和模拟实现

```c
int strcmp(const char *str1, const char *str2);
```

字符串比较：把两个字符串**对应位置上的字符按 ASCII 码值**进行比较。

- `>0`：表示 `str1 > str2`
- `<0`：表示 `str1 < str2`
- `=0`：表示相等

> @练习：附上 strcmp 的模拟实现。

## 七、strncpy 的使用

```c
strncpy(char *dest, char *src, size_t num);
```

可指定**替换/拷贝的个数**。

## 八、strncat 的使用

```c
strncat(char *dest, char *src, size_t num);
```

可指定**追加的个数**。

## 九、strncmp 的使用

```c
strncmp(char *dest, char *src, size_t num);
```

可指定**比较的个数**。

## 十、strstr 的使用和模拟实现

子串查找：在一个字符串中找另一个子字符串。

- 在 `str1` 中找 `str2`，返回子字符串的**起始地址**
- 找不到就返回 `NULL`

> @练习：附上 strstr 的模拟实现。

## 十一、strtok 的使用

```c
char *strtok(char *str, const char *sep);   // 需 #include <string.h>
```

按分隔符切割字符串。

- `strtok` 函数中应该有**静态变量 `static`**，用来保存信息
- 它**保存了 `NULL` 的位置**（第一次传入完整字符串，之后传 `NULL` 继续切分）

## 十二、strerror 的使用

```c
char *strerror(int errornum);
```

把参数部分**错误码对应的错误信息的字符串地址**返回。

- C 语言中错误地调用库函数时，会将对应的错误码存放在**全局变量 `errno`** 里
- 想用 `errno` 获得错误码，要 `#include <errno.h>`
- **拓展：** `perror(自定义信息)` 函数 = `printf` + `strerror`