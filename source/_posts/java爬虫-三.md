---
title: java爬虫(三)-让爬虫加上翅膀多线程
tags:
  - 爬虫
  - java
categories:
  - java爬虫-微博
keywords: 'java爬虫,微博爬虫,spider,crawl'
id: crawler003
date: 2019-08-12 15:03:24
---


# java爬虫(三)-让爬虫加上翅膀多线程


> 假设**一个连接设定为一个任务** ，现在有100个爬取任务，每一个任务中包括,任务获取、下载、分析、存储，预计消耗2~5s的时间，这里用一个简单的程序来模拟这个过程。
爬虫如果要提高效率(速度+准确性)，这里就需要使用多线程来加快速度。
ps: 这里是简化了**webmgic**中的**Spider**类

[代码](https://github.com/ybbzbb/crawl-preview/tree/master/src/test/java/io/github/ybbzbb/crawl/course02)
``` java
import java.util.ArrayDeque;
import java.util.Deque;
import java.util.NoSuchElementException;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.atomic.AtomicInteger;

public class Spider {

    //任务的数量
    private final static int TASK_NUMBER = 100;
    //线程的数据量
    private static int POOL_NUMBER = 50;

    public static void main(String[] args) {
        for (int i = 0; i < 3; i++) {
            Spider s = new Spider();

            Deque<Integer> tasks = s.tasksInit();
            long begin = System.currentTimeMillis();
            s.run(tasks , POOL_NUMBER);
            long end = System.currentTimeMillis();
            System.out.println("多线程执行时间 " + (end - begin));

            tasks = s.tasksInit();
            begin = System.currentTimeMillis();
            s.singleExecute(tasks);
            end = System.currentTimeMillis();
            System.out.println("单线程执行时间 " + (end - begin));

            System.out.println("===============================>>");
        }
    }

    private final static int STAT_INIT = 0;
    private final static int STAT_RUNNING = 1;
    private final static int STAT_STOPPED = 2;
    private AtomicInteger stat = new AtomicInteger(STAT_INIT);

    private AtomicInteger threadAlive = new AtomicInteger();

    public void run(Deque<Integer> tasks , int poolNumber) {

        
        final ExecutorService executorService = Executors.newFixedThreadPool(poolNumber);
        stat.set(STAT_RUNNING);

        while (stat.get() == STAT_RUNNING){
            Integer task = getTask(tasks);

            if (task == null){
                if (threadAlive.get() == 0) {
                    stat.set(STAT_STOPPED);
                }
                continue;
            }

            threadAlive.incrementAndGet();
            executorService.execute(new Runnable() {
                @Override
                public void run() {
                    execute(task);
                    threadAlive.decrementAndGet();
                }
            });

        }

        executorService.shutdown();
    }

    private void singleExecute(Deque<Integer> tasks){
        Integer task = getTask(tasks);
        while (task != null){
            execute(task);
            task = getTask(tasks);
        }
    }

    private Integer getTask(Deque<Integer> tasks){
        Integer task = null;
        try {
            task = tasks.pop();
        }catch (NoSuchElementException e){}
        return task;
    }

    private void execute(Integer task){
        long random = ((int)(Math.random()*3) + 2) * 100;

        try {
            Thread.sleep(random);
        } catch (InterruptedException e) {}
    }

    private Deque<Integer> tasksInit(){
        Deque<Integer> tasks = new ArrayDeque<>(TASK_NUMBER);

        for (int i = 1; i <= TASK_NUMBER; i++) {
            tasks.add(i);
        }

        return tasks;
    }

}
```
运行的结果

```log
多线程执行时间 810
单线程执行时间 29844
===============================>>
多线程执行时间 818
单线程执行时间 30854
===============================>>
多线程执行时间 817
单线程执行时间 31049
```
跑出这个数据的电脑配置是 16G内存、4核、固态硬盘、I7处理器
多线程的平均执行结果是 810ms 左右，而单线程的执行结果是 3Wms左右的时间。
如何使用多线程让系统到达最高的抓爬速率，是很难的。这里要考虑到:
1. 软件的可靠性
2. 硬件的性能
3. 针对硬件的配置

软件的可靠性，是文章系列的重点，同时也会说明一些针对于硬件的配置。








