---
layout: post
title: Data overflow, data starvation and how to solve this problem
subtitle: Programming basics.
gh-repo:
gh-badge:
tags: [Embedded, Programming]
comments: true
---
In programming if you are using local variable in two different loops. Then its important to find out if that local varibale overflowing or underflowing.
Following image is of sine wave simulatior running in one loop (Producer Loop) and local varibale reading samples of sine wave running in another (consumer loop).

![](https://dl.dropboxusercontent.com/s/eysn55m9hvt9bja/OverFlow_UnderFlow.png?dl=0)


Following screen, shot is example of data overflow. Where producer loop running at 50 ms and consumer loop at 25 ms, this resulted into duplication of samples.

![](https://dl.dropboxusercontent.com/s/zxo98kazlk090gs/OverFlow.png?dl=0)

Data underflow shown in following example. Where speed of consumer loop is half of producer loop which results into missing of samples.

![](https://dl.dropboxusercontent.com/s/fq9i9v3a0oh4zcc/UnderFlow.png?dl=0)

Data transfer between two non-synchronized parallel loops using local variables causes a race condition. This occurs when the Producer Loop is writing a value to a local variable while the Local Variable Consumer Loop is periodically reading out the value from the same local variable. Because the parallel loops are not synchronized, the value can be written before it has actually been read or vice versa resulting in data starvation or data overflow.

## Solution:
One way to solve this issue is by using queue, it is first in first out data type. Queues synchronize the data transfer between the two independent parallel loops and thus avoid loss or duplication of data.
Following image shows queue consumer loop and producer loop running at same speed. Which results in zero samples in queue.

![](https://dl.dropboxusercontent.com/s/9zjzbuq4qzqvn2b/Queue.png?dl=0)
