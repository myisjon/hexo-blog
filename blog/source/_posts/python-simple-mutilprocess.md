---
title: python multiprocessing 使用例子 -- 1
date: 2018-5-30 18:59:59
categories:
  - python
  - basic libs
tags:
  - python
  - mutilprocess
---

python 受到本身的GIL(Global Interpreter Lock)的影响,单进程无法充分利用多核CPU的计算资源,这是一个性能上非常浪费的事情,但是Python并没有就不能使用多核CPU的计算资源,可以使用多进程的方式来弥补GIL的问题,虽然GIL限制了单进程使用多核,但是它的存在使得单线程的情况更快,关于GIL可以看看[CPython官网的wiki解释](https://wiki.python.org/moin/GlobalInterpreterLock)。本文对Python的多进程的使用给出简单的使用例子

<!-- more -->

# Python得多进程库multiprocessing -- 1

## 使用Process创建进程

Process 是创建进程的类,它不仅可以在父进程中创建子进程,还可以在子进程中继续创建进程(孙子进程),可以通过对deamon的设置将父进程作为守护进程。下面给出使用例子:

```python
import os
import time
from multiprocessing import Process

def process_func():
    print('run child process')
    time.sleep(3)
    print('this process os pid: ', os.getpid(), 'parent process pid: ',os.getppid())

def main():
    print('start father process')
    process_1 = Process(target=process_func)
    process_1.daemon = True
    process_1.start()
    print('create child process pid: ', process_1.pid)

    process_1.join()
    process_1.terminate()


if __name__ == '__main__':
    main()
```

## 使用主进程管理子进程的状态

> 只要主进程存活,自动拉起子进程,如果通过kill 杀掉父进程，会直接让子进程归为pid为1的根进程

```python
import os
import time
from multiprocessing import Process, current_process

def process_func():
    (name, pid)= (current_process().name, current_process().pid)
    while True:
        print('parent process pid:', os.getppid(), 'process name: ', name, 'process pid', pid, 'status:', current_process().is_alive())
        time.sleep(1)

def main():
    print('start main process')
    process_1 = Process(target=process_func, name='process_1')
    process_1.deamon = True
    process_1.start()
    print('create child process_1 process pid: ', process_1.pid)

    process_2 = Process(target=process_func, name='process_2')
    process_2.deamon = True
    process_2.start()
    print('create child process_2 process pid: ', process_2.pid)

    while True:
        if process_1.is_alive() and process_2.is_alive():
            print('kill child pid', process_1.pid, 'name:', process_1.name)
            process_1.terminate()
            process_1.join()
        elif process_2.is_alive() and not process_1.is_alive():
            print('kill child pid', process_2.pid, 'name:', process_2.name)
            process_2.terminate()
            process_2.join()
        elif not process_1.is_alive():
            process_1 = Process(target=process_func, name='process_1')
            process_1.start()
            print('start process name:', process_1.name, ', pid:', process_1.pid)
        elif not process_2.is_alive():
            process_2 = Process(target=process_func, name='process_2')
            process_2.start()
            print('start process name:', process_2.name, ', pid:', process_2.pid)

        print('child live status, process_1: {}, process_2: {}'.format(process_1.is_alive(), process_2.is_alive()))

        time.sleep(10)

    process_1.join()
    process_2.join()

    process_1.terminate()
    process_2.terminate()

if __name__ == '__main__':
    main()
```

本文先给出基本Python multiprocessing 的process的例子,后面的文章将会给出多进程通信的例子
