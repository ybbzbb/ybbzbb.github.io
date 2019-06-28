---
title: java爬虫(二)-一个非常简单的爬虫
tags: 
    - 爬虫
    - java
categories:
    - 爬虫
id: crawler002
---

# java爬虫(二)-一个非常简单的爬虫

## 1.说明
爬虫说简单点就是封装http请求然后分析请求结果，本质上是没有什么含量的技术。这里大家也最好去看一下关于**http协议**的相关文档，这里就不加说明了。
目前影响爬虫的主要两个因素：
1. **带宽** (没有带宽追求量那就是扯淡，技术上很难在这方面提升，后期会加上流量监控)
2. **反抓爬机制** (涉及到代理、模拟登录、自动变动请求频次等 - **重点**)

本章代码位置 [github](https://github.com/ybbzbb/crawl-preview),我们项目的步骤是
1.  [发起请求]() 
2.  [分析请求结果]
3.  保存结果(打印)

## 新建项目
进入springboot[地址](https://start.spring.io/), 设置参数创建项目进行导出，解压项目。
{% asset_img create.png %}
添加 jar 包
```xml
<!-- json -->
<!-- http 请求 -->
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpclient</artifactId>
    <version>4.5.9</version>
</dependency>

<!-- json -->
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.8.5</version>
</dependency>

<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.4</version>
    <scope>provided</scope>
</dependency>
```
具体我就不说明了，大家可以去找一下关于搭建项目的文档。

## 来请求一个页面吧
我们以 微博的一个搜索页面举例 [抽奖](https://s.weibo.com/weibo?q=%23%E6%8A%BD%E5%A5%96%23) ,我们用浏览器打开。
```text
https://s.weibo.com/weibo?q=%23%E6%8A%BD%E5%A5%96%23
```
通过观察是**不需要登录**的，请求的方式是 **GET** 返回的是HTML格式的代码，那我们就开始一个简单的请求。



