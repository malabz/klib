
# Klib 使用教程

`kvec.h` 是一个用于动态数组操作的库，提供了各种方便的宏来简化数组操作。以下是如何使用 `kvec.h` 库的详细教程，包括初始化、销毁、添加、访问、插入和删除元素的基本步骤。

## 导入 Klib 库

首先，确保你包含了 `kvec.h` 文件：

```c
#include "kvec.h"
```

## 初始化和销毁向量

在使用向量之前，需要先初始化它，并在使用完毕后销毁它以释放内存：

```c
kvec_t(int) array; 
kv_init(array);  // 初始化
kv_destroy(array);  // 销毁
```

## 添加元素

使用 `kv_push` 宏向向量中添加元素：

```c
kv_push(int, array, 10);  // 添加元素 10
```

## 访问元素

可以使用 `kv_A` 或 `kv_a` 宏来访问向量中的元素：

```c
int value = kv_A(array, 0);  // 访问第一个元素，静态访问，如果index超过边界，会报错
int dynamic_value = kv_a(int, array, 20);  // 动态访问，访问超越边界的index，会动态调整大小后访问第 20 个元素
```

## 调整大小

可以使用 `kv_resize` 宏调整向量的大小：

```c
kv_resize(int, array, 50);  // 调整向量大小为 50
```

## 反转元素
可以使用`kv_reverse`对指定位置之后的元素进行反转
~~~c
kv_reverse(int, my_vector, 2);  //从索引2开始调用 `kv_reverse` 宏反转元素。
~~~
## 插入元素

使用 `kv_insert` 宏可以在指定位置插入元素：

```c
kv_insert(int, array, 5, 100);  // 在索引 5 处插入元素 100
```

## 删除元素

使用 `kv_delete` 宏可以删除指定位置的元素：

```c
kv_delete(int, array, 3);  // 删除索引 3 处的元素
```

## 示例代码

以下是一个完整的示例代码，展示了如何使用 `kvec.h`：

```c
#include <stdio.h>
#include "kvec.h"

int main() {
    kvec_t(int) array;
    kv_init(array);

    // 添加元素
    for (int i = 0; i < 10; ++i) {
        kv_push(int, array, i);
    }

    // 插入元素
    kv_insert(int, array, 5, 100);

    // 删除元素
    kv_delete(int, array, 3);

    // 访问元素
    for (int i = 0; i < kv_size(array); ++i) {
        printf("%d ", kv_A(array, i));
    }
    printf("\n");

    // 销毁向量
    kv_destroy(array);
    return 0;
}
```

## 宏详细解释

- `kvec_t(type)`: 定义一个向量结构，包含元素数量 `n`、最大容量 `m` 和指向数组的指针 `a`。
- `kv_init(v)`: 初始化向量 `v`，将 `n` 和 `m` 设置为 0，`a` 设置为 NULL。
- `kv_destroy(v)`: 销毁向量 `v`，释放分配的内存。
- `kv_A(v, i)`: 访问向量 `v` 中索引 `i` 处的元素（不进行边界检查）。
- `kv_pop(v)`: 弹出并返回向量 `v` 的最后一个元素。
- `kv_size(v)`: 返回向量 `v` 中的元素数量 `n`。
- `kv_max(v)`: 返回向量 `v` 的最大容量 `m`。
- `kv_resize(type, v, s)`: 调整向量 `v` 的大小为 `s`。
- `kv_copy(type, v1, v0)`: 复制向量 `v0` 的内容到 `v1`。
- `kv_push(type, v, x)`: 向向量 `v` 添加元素 `x`，并在需要时动态调整大小。
- `kv_pushp(type, v)`: 为向量 `v` 添加一个新元素，并返回指向该元素的指针。
- `kv_a(type, v, i)`: 动态调整向量 `v` 的大小以确保能够访问索引 `i` 处的元素。
- `kv_insert(type, v, i, x)`: 在索引 `i` 处插入元素 `x`。
- `kv_delete(type, v, i)`: 删除索引 `i` 处的元素。


## 参考内容
[C Array vs. C++ Vector | Attractive Chaos](https://attractivechaos.wordpress.com/2008/09/19/c-array-vs-c-vector/)