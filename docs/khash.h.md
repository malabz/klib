# KHASH 使用教程

`khash.h` 是一个通用的哈希表库，允许用户创建和管理哈希表。该库由 Attractive Chaos 编写并使用 MIT 许可证发布。以下是使用 `khash.h` 库的基本教程，涵盖了创建、操作和销毁哈希表的基本步骤。

## 导入 KHASH 库

首先，确保你已经包含了 `khash.h` 文件：

```c
#include "khash.h"
```

## 定义和初始化哈希表

使用 `KHASH_MAP_INIT_INT` 宏定义一个整数键映射的哈希表。这个宏将创建一个名为 `kh_int` 的哈希表类型，并提供必要的函数原型。

```c
KHASH_MAP_INIT_INT(32, char)
```

这里的 `32` 是哈希表的名称标识符，可以是任何合法的 C 语言标识符。它用于定义和引用哈希表的类型和操作函数。

定义后，可以使用 `kh_init` 初始化哈希表：

```c
khash_t(32) *h = kh_init(32);
```

## 插入元素

使用 `kh_put` 函数将键值对插入哈希表。该函数返回一个迭代器和一个额外的返回码（`ret`），指示插入的结果：

```c
int ret;
khiter_t k = kh_put(32, h, 5, &ret);
kh_value(h, k) = 10;  // 设置值
```

## 检查键是否存在

使用 `kh_get` 函数检查哈希表中是否存在指定的键。如果返回的迭代器等于 `kh_end(h)`，则表示该键不存在：

```c
k = kh_get(32, h, 10);
int is_missing = (k == kh_end(h));
```

## 删除元素

使用 `kh_del` 函数删除指定的键：

```c
k = kh_get(32, h, 5);
kh_del(32, h, k);
```

## 遍历哈希表

可以使用 `kh_foreach` 宏遍历哈希表中的所有元素：

```c
kh_foreach(h, key, val, {
    printf("key: %d, value: %d\n", key, val);
})
```

或者，使用以下代码手动遍历：

```c
for (k = kh_begin(h); k != kh_end(h); ++k) {
    if (kh_exist(h, k)) {
        printf("key: %d, value: %d\n", kh_key(h, k), kh_val(h, k));
    }
}
```

## 清空和销毁哈希表

使用 `kh_clear` 函数清空哈希表，但保留其内存：

```c
kh_clear(32, h);
```

使用 `kh_destroy` 函数销毁哈希表并释放所有分配的内存：

```c
kh_destroy(32, h);
```

## 完整示例

以下是一个完整的示例程序，演示了 `khash.h` 的基本用法：

```c
#include <stdio.h>
#include "khash.h"

KHASH_MAP_INIT_INT(32, char)

int main() {
    int ret, is_missing;
    khiter_t k;
    khash_t(32) *h = kh_init(32);

    // 插入元素
    k = kh_put(32, h, 5, &ret);
    kh_value(h, k) = 10;

    // 检查键是否存在
    k = kh_get(32, h, 10);
    is_missing = (k == kh_end(h));

    // 删除元素
    k = kh_get(32, h, 5);
    kh_del(32, h, k);

    // 遍历哈希表
    for (k = kh_begin(h); k != kh_end(h); ++k) {
        if (kh_exist(h, k)) {
            printf("key: %d, value: %d\n", kh_key(h, k), kh_val(h, k));
        }
    }

    // 销毁哈希表
    kh_destroy(32, h);
    return 0;
}
```

## 宏详细解释

- `khash_t(name)`: 定义哈希表类型。
- `kh_init(name)`: 初始化哈希表。
- `kh_destroy(name, h)`: 销毁哈希表并释放所有分配的内存。
- `kh_clear(name, h)`: 清空哈希表但保留其内存。
- `kh_resize(name, h, s)`: 调整哈希表大小。
- `kh_put(name, h, k, r)`: 插入键 `k` 到哈希表 `h` 中。
- `kh_get(name, h, k)`: 检查键 `k` 是否存在于哈希表 `h` 中。
- `kh_del(name, h, k)`: 删除哈希表 `h` 中的键 `k`。
- `kh_exist(h, x)`: 检查迭代器 `x` 是否有效。
- `kh_key(h, x)`: 获取键。
- `kh_val(h, x)`: 获取值。
- `kh_begin(h)`: 获取哈希表的起始迭代器。
- `kh_end(h)`: 获取哈希表的结束迭代器。
- `kh_size(h)`: 获取哈希表中元素的数量。
- `kh_n_buckets(h)`: 获取哈希表的容量。
- `kh_foreach(h, kvar, vvar, code)`: 遍历哈希表。

## 自定义键和值类型

在 `khash.h` 中，键和值可以是任何类型。通过使用 `KHASH_INIT` 宏，你可以定义具有自定义键和值类型的哈希表。

### 示例 1: int 作为 key，char* 作为 value

```c
#include "khash.h"
#include <stdio.h>
KHASH_MAP_INIT_INT(my_hash, char*)

int main() {
    khash_t(my_hash) *h = kh_init(my_hash);
    int ret;
    khiter_t k;

    // 插入元素
    k = kh_put(my_hash, h, 1, &ret);
    kh_value(h, k) = "value1";

    // 检查键是否存在
    k = kh_get(my_hash, h, 1);
    if (k != kh_end(h)) {
        printf("Key: %d, Value: %s\n", 1, kh_val(h, k));
    }

    // 删除元素
    k = kh_get(my_hash, h, 1);
    kh_del(my_hash, h, k);

    kh_destroy(my_hash, h);
    return 0;
}
```

### 示例 2: char* 作为 key，int 作为 value

```c
#include "khash.h"
#include <stdio.h>
KHASH_MAP_INIT_STR(str_hash, int)

int main() {
    khash_t(str_hash) *h = kh_init(str_hash);
    int ret;
    khiter_t k;

    // 插入元素
    k = kh_put(str_hash, h, "key1", &ret);
    kh_value(h, k) = 42;

    // 检查键是否存在
    k = kh_get(str_hash, h, "key1");
    if (k != kh_end(h)) {
        printf("Key: %s, Value: %d\n", "key1", kh_val(h, k));
    }

    // 删除元素
    k = kh_get(str_hash, h, "key1");
    kh_del(str_hash, h, k);

    kh_destroy(str_hash, h);
    return 0;
}
```
### 自定义示例

假设你有一个结构体类型的键和值：
~~~c
typedef struct {
    int id;
    char name[50];
} my_key_t;

typedef struct {
    float data;
} my_val_t;

// 简单的哈希函数和比较函数示例
unsigned my_hash_func(my_key_t key) {
    return key.id;
}

int my_hash_equal(my_key_t a, my_key_t b) {
    return a.id == b.id;
}

KHASH_INIT(my_custom_hash, my_key_t, my_val_t, 1, my_hash_func, my_hash_equal)

~~~
你可以像这样使用它们：
~~~c
khash_t(my_custom_hash) *h = kh_init(my_custom_hash);
my_key_t key = {1, "example"};
my_val_t val = {3.14};

int ret;
khiter_t k = kh_put(my_custom_hash, h, key, &ret);
kh_value(h, k) = val;

// 检查键是否存在
k = kh_get(my_custom_hash, h, key);
if (k != kh_end(h)) {
    printf("Key found: id=%d, name=%s, data=%.2f\n", key.id, key.name, kh_val(h, k).data);
}

kh_destroy(my_custom_hash, h);

~~~
# 参考
[Klib — a generic library in C](https://attractivechaos.github.io/klib/#Khash%3A%20generic%20hash%20table)