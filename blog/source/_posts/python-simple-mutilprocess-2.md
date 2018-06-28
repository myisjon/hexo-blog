---
title: python-simple-mutilprocess 使用例子 -- 2
date: 2018-06-26 21:06:42
categories:
  - python
  - basic libs
tags:
  - python
  - mutilprocess
---

python 的线程或者协程只能使用单核CPU,对于现在多核CPU的性能无法充分利用,但Python并非只能使用单核的性能，可以Python的多进程充分利用多核CPU的情况,这里涉及到多进程,对于独立计算的程序来说,各个进程不需要进行数据共享倒是一件很美好的事情,大多数情况涉及多进程的程序都会产生数据共享的情况,这里就需要多进程的通信,有很多方法可以达到多进程数据共享的情况,可以利用外部内存管理工具做数据共享池或者外部内存数据库管理数据共享部分(如redis),甚至可以使用管道来进行数据的共享,但是这都需要额外消耗更多的资源达到数据共享,在一些情况下这都并不较优的选择,Python本身提供了多进程数据共享的模块,例如Pipes、Queues、Manager和子进程继承父进程共享内存块,本文中主要介绍Manager对多进程的共享的例子.
<!-- more -->

# Python得多进程库multiprocessing -- 2

Manager模块提供多种数据类型为Python的多进程做数据通信,例如: Queue, List, Dict等常用的类型,并且还支持自定的数据类型。但是Manager使用无名套接字socketpair作为各个进程的通信通道,在效率上相对socket高,比内存共享低,但是对于数据类型没有比较友好,我个人认为这是一个很大的优势.下面是一个简单的进程通信例子:

```python
import time
from multiprocessing import Process, Manager


def productor(q_queue):
    cnt = 0
    while True:
        cnt += 1

        if q_queue.qsize() > 5:
            # 丢弃最早的数据,并且停顿1秒生产
            print('give up value:', q_queue.get())
            time.sleep(1)

        # 生产数据
        q_queue.put(cnt)
        # 每隔0.5秒生产一个数据
        time.sleep(0.5)


def customer(q_queue):
    while True:

        if q_queue.empty():
            # 如果生产的库存数据为空,则停顿0.1秒,并且进入下一次消费判断
            time.sleep(0.1)
            continue

        # 消费数据
        print('custom: ', q_queue.get())

        # 每隔0.6秒消费一个数据
        time.sleep(0.6)


def main():
    # 声明Manager数据通道
    with Manager() as manger:
        # 指定数据通道类型
        q_queue = manger.Queue()

        # 创建生产者进程
        p_productor = Process(target=productor, args=(q_queue, ), name='productor')
        p_productor.deamon = True
        # 启动生产者进程
        p_productor.start()

        # 创建消费者进程
        p_customer = Process(target=customer, args=(q_queue, ), name='customer')
        p_customer.deamon = True
        # # 启动消费者进程
        p_customer.start()

        # 在主进程监督消费者和生产者的状态(每五秒检测一次)
        while True:
            print('=' * 79)
            print('= check q_queue:', 'size: ', q_queue.qsize())
            print('= producter process status:', p_productor.is_alive())
            print('= customer process status:', p_customer.is_alive())
            print('=' * 79)
            time.sleep(5)

        p_productor.join()
        p_customer.join()

        p_productor.terminate()
        p_customer.terminate()


if __name__ == '__main__':
    main()
```

例子中是又productor 生产数据,customer 消费数据,如果productor生产的数据无法存放库存中，则开始丢弃(丢弃最早生产的数据).
本例子是实现进程通信中的生产者/消费者模型.
