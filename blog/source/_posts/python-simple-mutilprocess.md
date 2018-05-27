---
title: python multiprocessing 使用例子
date: 2018-5-30 18:59:59
categories:
  - python
  - basic libs
tags:
  - python
---

python 受到本身的GIL(Global Interpreter Lock)的影响,单进程无法充分利用多核CPU的计算资源,这是一个性能上非常浪费的事情,但是Python并没有就不能使用多核CPU的计算资源,可以使用多进程的方式来弥补GIL的问题,虽然GIL限制了单进程使用多核,但是它的存在使得单线程的情况更快,关于GIL可以看看[CPython官网的wiki解释](https://wiki.python.org/moin/GlobalInterpreterLock)。本文对Python的多进程的使用给出简单的使用例子

<!-- more -->

# Python得多进程库multiprocessing

## 使用Process创建进程

Process 是创建进程的类,它不仅可以在父进程中创建子进程,还可以在子进程中继续创建进程(孙子进程),可以通过对deamon的设置将父进程作为守护进程。下面给出使用例子:

```python
import os
import time
from multiprocessing import Process

def process_func(pid_id):
    while True:
        print('this pid: ', pid_id, 'system pid: ',os.getpid())
        time.sleep(3)


def main():
    print('start father process')
    print('create child process pid: ', 1)
    process_1 = Process(target=process_func, args=(1, ))
    process_1.daemon = True
    process_1.start()

```
