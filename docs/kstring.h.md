# kstring.h 使用教程

`kstring.h` 是一个简单且高效的字符串处理库。它提供了一些基本的字符串操作函数，可以用于处理动态字符串。以下是 `kstring.h` 库的详细使用教程，涵盖了如何初始化、操作和释放 `kstring_t` 结构。

## 1. 初始化 kstring_t

在使用 `kstring_t` 之前，需要先初始化它。可以使用以下两种方法之一进行初始化：

```c
kstring_t str = {0, 0, NULL}; // (l, m, s)
```

或者

```c
kstring_t str;
str.l = str.m = 0; // l为字符数量，m为开辟空间( std::string )
str.s = NULL; // s为char* 存放数据
```

## 2. 调整字符串缓冲区大小

使用 `ks_resize` 函数可以调整 `kstring_t` 的缓冲区大小。

```c
if (ks_resize(&str, new_size) != 0) {
    // 处理分配失败的情况
}
```

## 3. 追加字符串

### 追加以 null 结尾的字符串

使用 `kputs` 函数将一个以 null 结尾的字符串追加到 `kstring_t`。

```c
kputs("Hello", &str);
```

### 追加指定长度的字符串

使用 `kputsn` 函数将一个指定长度的字符串追加到 `kstring_t`。

```c
kputsn("Hello", 5, &str); // 追加 "Hello",5指的是追加的字符数量
```

### 追加单个字符

使用 `kputc` 函数将一个字符追加到 `kstring_t`。

```c
kputc('A', &str);
```

### 追加整数

使用 `kputw` 函数将一个整数作为字符串追加到 `kstring_t`。

```c
kputw(123, &str); //仅为int类型
```

## 4. 格式化字符串

使用 `ksprintf` 函数可以将格式化字符串追加到 `kstring_t`。

```c
ksprintf(&str, "Number: %d", 123);
```

## 5. 字符串查找和分割

### 分割字符串

使用 `ksplit` 函数可以将字符串按指定的分隔符分割，并返回分割后的部分的偏移数组。

```c
int n; // n用于存储offsets的个数
int *offsets = ksplit(&str, ' ', &n); // (kstring_t的地址，分割的字符，n用于存储offsets的个数)
for (int i = 0; i < n; ++i) {
    printf("Part %d: %s\n", i, str.s + offsets[i]);
}
free(offsets);
```

### 查找子字符串

使用 `kstrstr` 函数可以在字符串中查找子字符串。

```c
char *result = kstrstr(str.s, "World", NULL); //（str.s为被搜寻的字符串，"World"为pattern，NULL存放预处理结果）
if (result) {
    printf("Found: %s\n", result);
}
```

## 6. 读取一行

使用 `kgetline` 函数可以从文件中读取一行，并将其追加到 `kstring_t`。

```c
FILE *fp = fopen("file.txt", "r");
if (fp) {
    while (kgetline(&str, (kgets_func*)fgets, fp) >= 0) {
        printf("Line: %s\n", str.s);
    }
    fclose(fp);
}
```

## 7. 获取字符串和长度

### 获取字符串

使用 `ks_str` 函数可以获取 `kstring_t` 中的字符串指针。

```c
char *s = ks_str(&str);
```

### 获取字符串长度

使用 `ks_len` 函数可以获取 `kstring_t` 中字符串的长度。

```c
size_t len = ks_len(&str);
```

## 8. 释放 kstring_t

在不再需要 `kstring_t` 时，应该释放其内存。

```c
free(str.s);
```

如果需要将 `kstring_t` 的底层缓冲区转移给其他所有者，可以使用 `ks_release` 函数。

```c
char *s = ks_release(&str);
// 现在 s 由其他所有者管理，str 为空
free(s); // 由新所有者负责释放
```

## 示例代码

以下是一个完整的示例代码，展示了如何使用 `kstring.h` 库：

```c
#include "kstring.h"
#include <stdio.h>

int main() {
    kstring_t str = {0, 0, NULL};

    kputs("Hello", &str);
    kputc(' ', &str);
    kputs("World!", &str);
    ksprintf(&str, " Number: %d", 123);

    printf("%s\n", ks_str(&str));  // 输出: Hello World! Number: 123

    // 分割字符串
    int n;
    int *offsets = ksplit(&str, ' ', &n);
    for (int i = 0; i < n; ++i) {
        printf("Part %d: %s\n", i, str.s + offsets[i]);
    }
    free(offsets);

    free(str.s);  // 释放内存
    return 0;
}
```

