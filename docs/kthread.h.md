# `kt_for` 和 `kt_forpool` 使用教程

`kt_for` 和 `kt_forpool` 是 `klib` 中提供的两个用于并行任务处理的函数。它们允许你将任务分发到多个线程中执行，利用多核处理器的优势来提高性能。本文将介绍这两个函数的用法，并提供示例代码以展示其工作原理。

## `kt_for` 用法

`kt_for` 函数用于简单的并行任务调度。它将任务分成多个块，并将这些块分发到多个线程中执行。每个线程独立执行其分配的任务块。

### 函数定义

```c
void kt_for(int n_threads, void (*func)(void*, long, int), void *data, long n);
```

- `n_threads`：线程数量。
- `func`：任务函数，每个线程执行的具体任务。
- `data`：传递给任务函数的参数。
- `n`：任务数量。

### 示例代码

```c
#include "kthread.h"
#include <stdio.h>
#include <stdlib.h>
#include <vector>

const int ARRAY_SIZE = 1000;
const int NUM_THREADS = 4;

// 示例任务函数
void my_task_function(void* data, long i, int tid) {
    std::vector<int>& array = *reinterpret_cast<std::vector<int>*>(data);
    array[i] = array[i] * array[i];  // 示例任务：计算数组元素的平方
    printf("Thread %d processing index %ld\n", tid, i);
}

void test_kt_for() {
    std::vector<int> array(ARRAY_SIZE);
    for (int i = 0; i < ARRAY_SIZE; ++i) {
        array[i] = i + 1;
    }

    kt_for(NUM_THREADS, my_task_function, &array, ARRAY_SIZE);

    // 打印结果
    for (int i = 0; i < ARRAY_SIZE; ++i) {
        printf("array[%d] = %d\n", i, array[i]);
    }
}

int main() {
    test_kt_for();
    return 0;
}
```

## `kt_forpool` 用法

`kt_forpool` 是 `kt_for` 的一个增强版，适用于更复杂的任务调度和管理。它允许你创建一个线程池，并将任务分发到线程池中执行。与 `kt_for` 不同，`kt_forpool` 可以重复使用线程池，减少创建和销毁线程的开销。

### 函数定义

```c
void *kt_forpool_init(int n_threads);
void kt_forpool_destroy(void *_fp);
void kt_forpool(void *_fp, void (*func)(void*, long, int), void *data, long n);
```

- `kt_forpool_init`：初始化线程池。
- `kt_forpool_destroy`：销毁线程池。
- `kt_forpool`：将任务分发到线程池中执行。

### 示例代码

```c
#include "klib/kthread.h"
#include <stdio.h>
#include <stdlib.h>
#include <vector>

const int ARRAY_SIZE = 1000;
const int NUM_THREADS = 4;

// 示例任务函数
void my_task_function(void* data, long i, int tid) {
    std::vector<int>& array = *reinterpret_cast<std::vector<int>*>(data);
    array[i] = array[i] * array[i];  // 示例任务：计算数组元素的平方
    printf("Thread %d processing index %ld\n", tid, i);
}

void test_kt_forpool() {
    std::vector<int> array(ARRAY_SIZE);
    for (int i = 0; i < ARRAY_SIZE; ++i) {
        array[i] = i + 1;
    }

    // 初始化线程池
    void* thread_pool = kt_forpool_init(NUM_THREADS);

    // 分配任务
    kt_forpool(thread_pool, my_task_function, &array, ARRAY_SIZE);

    // 销毁线程池
    kt_forpool_destroy(thread_pool);

    // 打印结果
    for (int i = 0; i < ARRAY_SIZE; ++i) {
        printf("array[%d] = %d\n", i, array[i]);
    }
}

int main() {
    test_kt_forpool();
    return 0;
}
```

## 比较 `kt_for` 和 `openmp`
经过测验，得出的结论为，当每个线程执行的任务非常简单，openmp快于kt_for。每个线程的任务越复杂，kt_for越快

