---
title: 线性表
date: 2022-01-26 22:35:02
tags: 数据结构
categories: 考研
---

![](./linear_list_def.png)

#### 线性表的定义

线性表是具有**相同数据类型**的 n (n>=0) 个**数据元素**的**有限**序列。若用 L 命名线性表，则其一般表示为

$L=(a_1, a_2, ... ,a_i ,a_{i+1}, ..., a_n)$


#### 线性表的基本操作

- InitList(&n); 初始化表，构造一个空的线性表
- Length(L); 获得线性表 L 的长度
- LocateElem(L, e); 按值查找，在表 L 中找到数据元素 e
- GetElem(L, i); 按位查找，获取表 L 的第 i 个数据元素
- ListInsert(&L, i, e); 插入，向表 L 的 i 位置插入数据元素 e
- ListDelete(&L, i, &e); 删除，删除表 L 中 i 位置的元素，并将删除的值保存在 e 中
- PrintList(L); 输出，按前后顺序输出表 L 中的数据元素
- DestroyList(&L); 销毁，销毁线性表 L，并释放 L 所占用的内存空间


#### 顺序表的定义

线性表的**顺序存储**称为**顺序表**。


#### 重点

- 线性表中元素的序位是从 1 开始的，数组元素的下标是从 0 开始的
- 顺序表需要分配连续的内存空间，动态分配时，一旦空间沾满，就另外开辟一块更大的连续空间，用来替换原来的存储空间。比如，原表长 n，现已经存满，需要再扩充 m 个单位，则需要开辟连续的 m + n 空间，然后将原来的 n 个元素复制到新申请的空间中


#### 综合题

1. 从顺序表中删除具有最小值的元素（假设唯一）并由函数返回被删元素的值。空出的位置由最后一个元素填补，若顺序表为空则显示出错信息并退出运行。

```C
bool Del_Min(sqList &L, ElementType &e) {
    if (L.length == 0) {
        return false;
    }

    int index = 0;
    int min = L.data[0];
    for(int i = 1; i < L.length; i++) {
        if (L.data[i] < min) {
            index = i;
            e = min;
        }
    }
    L.data[index] = L.data[L.length - 1];
    L.length --;
}
```

2. 设计一个高效算法，将顺序表 L 的所有元素逆置，要求空间复杂度为 O(1)

```C
void Reverse(sqList &L) {
    int head = 0;
    int tail = L.length - 1;
    while (head < tail) {
        int temp = L.data[head];
        L.data[head] = L.data[tail];
        L.data[tail] = temp;
        head ++;
        tail --;
    }
}
```