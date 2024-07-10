## kseq.h 使用教程

`kseq.h` 是一个高效的 FASTA/FASTQ 文件读取库，由 Attractive Chaos 编写并发布在 MIT 许可证下。它是基于 kstream 结构的，可以处理常见的生物信息学文件格式。以下是使用 `kseq.h` 库的基本教程，涵盖了初始化、读取和销毁序列的基本步骤。

### 导入 KSEQ 库

首先，确保你包含了 `kseq.h` 文件：

```c
#include "kseq.h"
#include <stdio.h>
#include <unistd.h>
```

### 示例代码

以下是一个完整的示例程序，演示了 `kseq.h` 的基本用法：

```c
#include "klib/kseq.h"
#include <stdio.h>
#include <unistd.h>

KSEQ_INIT(int, read)

char* filename = "/path/to/test.fasta";

int main(int argc, char* argv[]) {
    fprintf(stderr, "Reading sequences from file %s ... \n", filename);

    FILE* f_pointer = fopen(filename, "r");
    if (f_pointer == NULL) {
        fprintf(stderr, "Error: file %s cannot open. Program will exit\n", filename);
        exit(1);
    }

    kseq_t* file_t = kseq_init(fileno(f_pointer));
    //file_t对象不能复用

    // 读取序列
    while (kseq_read(file_t) >= 0) {
        fprintf(stdout, "name: %s\n", file_t->name.s);
        if (file_t->comment.l) fprintf(stdout, "comment: %s\n", file_t->comment.s);
        fprintf(stdout, "seq: %s\n", file_t->seq.s);
        if (file_t->qual.l) fprintf(stdout, "qual: %s\n", file_t->qual.s);
    }

    // 销毁 kseq 结构
    kseq_destroy(file_t);
    fclose(f_pointer);  // 关闭文件
    return 0;
}
```
### 宏和函数详细解释

- `KSEQ_INIT(type_t, __read)`: 初始化 kseq 结构，`type_t` 是文件类型，`__read` 是读取函数。
- `kseq_init(type_t fd)`: 初始化 kseq_t 结构。
- `kseq_destroy(kseq_t *ks)`: 销毁 kseq_t 结构并释放内存。
- `kseq_read(kseq_t *seq)`: 读取下一个 FASTA/FASTQ 序列。返回值为读取的序列长度，若到达文件结尾则返回 -1。
-
### 读取gz文件
如果需要读取gz文件，首先要自行安装zlib库，然后参考以下代码
~~~c
// to compile: gcc this_prog.c -lz
#include <zlib.h>
#include <stdio.h>
#include "kseq.h"

// 初始化 kseq，用于读取 gzFile 类型的文件
KSEQ_INIT(gzFile, gzread)

int main(int argc, char *argv[])
{
    gzFile fp; // 文件指针
    kseq_t *seq; // 序列结构体
    int l; // 用于存储读取序列的长度

    // 检查命令行参数，确保提供了输入文件
    if (argc == 1) {
        fprintf(stderr, "Usage: %s <in.fasta>\n", argv[0]);
        return 1;
    }

    // 打开 Gzip 压缩的 FASTA 文件
    fp = gzopen(argv[1], "r");
    if (fp == NULL) {
        fprintf(stderr, "Error opening file %s\n", argv[1]);
        return 1;
    }

    // 初始化 kseq 结构体
    seq = kseq_init(fp);

    // 逐行读取序列
    while ((l = kseq_read(seq)) >= 0) {
        printf("name: %s\n", seq->name.s); // 打印序列名称
        if (seq->comment.l) printf("comment: %s\n", seq->comment.s); // 打印序列注释（如果存在）
        printf("seq: %s\n", seq->seq.s); // 打印序列
        if (seq->qual.l) printf("qual: %s\n", seq->qual.s); // 打印质量分数（如果存在）
    }

    printf("return value: %d\n", l); // 打印最后一次读取的返回值

    // 释放 kseq 结构体
    kseq_destroy(seq);

    // 关闭文件
    gzclose(fp);

    return 0;
}

~~~
### 参考

- [Klib GitHub](https://github.com/attractivechaos/klib)
- [FASTQ Wikipedia](https://en.wikipedia.org/wiki/FASTQ_format)
- [GitHub - malabz/kseq: kseq: a generic stream buffer & FASTA/FASTQ file parser by @lh3](https://github.com/malabz/kseq)