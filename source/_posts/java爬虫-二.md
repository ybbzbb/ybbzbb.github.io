---
title: java爬虫(二)-一个非常简单的爬虫
tags:
  - 爬虫
  - java
categories:
  - 爬虫
id: crawler002
date: 2019-07-01 10:49:29
---


# java爬虫(二)-一个非常简单的爬(qing)虫(qiu)

## 一.说明
爬虫说简单点就是封装http请求然后分析请求结果，本质上是没有什么含量的技术。这里大家也最好去看一下关于**http协议**的相关文档，这里就不加说明了。
目前影响爬虫的主要两个因素：
1. **带宽** (没有带宽追求量那就是扯淡，技术上很难在这方面提升)
2. **反抓爬机制** (涉及到代理、模拟登录、自动变动请求频次等 - **技术重点**)

本章代码位置 [github](https://github.com/ybbzbb/crawl-preview),我们项目的步骤是
1.  发起请求
2.  分析请求结果
3.  保存结果-打印

## 二.分析请求
想获取一个连接的中的信息时，首先要知道的内容连接地址(有的内容是**ajax异步**请求出来的)、请求方式、请求参数、请求头等信息。比如 https://s.weibo.com/weibo?q=%2523%E6%8A%BD%E5%A5%96%2523 这个微博的搜索*抽奖*的页面。进入页面 **F12** 进入开发中模式，选择到 **netWork** 栏目。
{% asset_img f12.png %}

查看 **XHR** 、**DOC** 等小目录中的请求，点击具体的请求查看 **Response** 中返回的数据是否是我们想要爬取的内容。

{% asset_img response.png %}

进入一个在线的“干净”的请求工具中测试请求中的一些参数( [连接](http://coolaf.com/) ) ，与Headers 栏目的参数进行比对尝试（后面详细介绍）。

## 三.分析页面
我们使用的是 [jsoup](https://try.jsoup.org/) 分析页面，这是一款很优秀的html 解析工具。我们在抽奖页面中右键查看源码，将源码复制到[测试页面](https://try.jsoup.org/)中

{% asset_img analysis.png %}

界面的解析语法查看 [解析](https://jsoup.org/apidocs/index.html?org/jsoup/select/Selector.html)
后面我专门出一个blog 写如何使用。


## 四.实现
进入springboot[地址](https://start.spring.io/), 设置参数创建项目进行导出，解压项目。
{% asset_img create.png %}
添加 jar 包
```xml
<!-- http 请求 -->
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpclient</artifactId>
    <version>4.5.9</version>
</dependency>
<!-- 解析html 文件 -->
<dependency>
    <groupId>org.jsoup</groupId>
    <artifactId>jsoup</artifactId>
    <version>1.12.1</version>
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

```java
package io.github.ybbzbb.crawl.course01;

import io.github.ybbzbb.crawl.downloader.HttpClient;
import io.github.ybbzbb.crawl.downloader.Page;
import io.github.ybbzbb.crawl.downloader.Request;
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.select.Elements;
import org.w3c.dom.xpath.XPathEvaluator;

import java.util.List;

/**
 * @Description 简单页面信息请求+分析
 */
public class SimpleRequestTest {

    public static void main(String[] args) throws Exception {
        //请求获取面
        Request request = new Request("https://s.weibo.com/weibo?q=%23%E6%8A%BD%E5%A5%96%23");
        Page page = HttpClient.doRequest(request);

        Document parse = Jsoup.parse(page.getRaw());

        //解析为list
        Elements select = parse.select("div.card-wrap[action-type=\"feed_list_item\"]");

        //获取 打印 标题
        select.forEach( e -> {
            Elements data = e.select("p[node-type=\"feed_list_content\"]");
            if (data == null || data.isEmpty()) {
                return;
            }
            System.out.println(data.first().text());
        });
    }
}
```


