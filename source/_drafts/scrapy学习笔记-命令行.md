---
title: 一、scrapy学习笔记(命令行)
tags:
  - scrapy 
  - python
  - 爬虫
categories:
  - scrapy
id: scrapy001
keywords: 'python爬虫,scrapy爬虫'
---

# scrapy学习笔记(命令行)

scrapy 的命令通过 **scrapy** 进行使用，主要分为两种 ： **commands** or **Scrapy commands** 。两种命令，前面是全局的，后面是属于子命令。

## 配置设置 configuration settings

> scrapy 在 **scrapy.cfg** 文件中加载配置文件
1. /etc/scrapy.cfg or c:\scrapy\scrapy.cfg (system-wide 全局设置)。
2. ~/.config/scrapy.cfg ($XDG_CONFIG_HOME) and ~/.scrapy.cfg ($HOME) for global (user-wide) settings, and 用户方位的全局设置。
3. scrapy.cfg inside a scrapy project’s root (see next section). 项目根目录，项目生效。

配置参数使用覆盖的方式，优先级最高是 项目配置 > 用户配置 > 全局配置

## 项目结构

默认的情况下，scrapy 项目的文件结构:

```
scrapy.cfg
myproject/
    __init__.py
    items.py
    middlewares.py
    pipelines.py
    settings.py
    spiders/
        __init__.py
        spider1.py
        spider2.py
        ...
```

**scrapy.cfg** 文件在的位置，是项目的根目录。项目定义了 爬虫的模块名称
```
[settings]
default = myproject.settings
```
**注：** scrapy 是可以配置多个项目(网站)的，比如 亚马逊、JD、淘宝的爬虫都在一个项目内，那么配置可能就是：  
```
[settings]
default = amazon.settings
project1 = jd.settings
project2 = taobao.settings
```

## 项目之间共享根目录
一个 **scrapy.cfg** 的配置，可以被多个项目共享 ，每个项目都有自己的配置。
这样的情况下，就需要给不同的项目命名。(如上)
默认情况，scrapy 命令工具使用默认是指，使用 **SCRAPY_PROJECT ** 来切换配置。

```
$ scrapy settings --get BOT_NAME
Project 1 Bot
$ export SCRAPY_PROJECT=project2
$ scrapy settings --get BOT_NAME
Project 2 Bot
```

## scrapy 工具箱
可以先运行不要任何参数的 **scrapy** ,会展示出来一些 scrapy 的提示。（在项目内输入格式不一样）
```
$ scrapy
Scrapy 1.8.0 - project: hornett_crawl

Usage:
  scrapy <command> [options] [args]

Available commands:
  bench         Run quick benchmark test
  check         Check spider contracts
  crawl         Run a spider
  edit          Edit spider
  fetch         Fetch a URL using the Scrapy downloader
  genspider     Generate new spider using pre-defined templates
  list          List available spiders
  parse         Parse URL (using its spider) and print the results
  runspider     Run a self-contained spider (without creating a project)
  settings      Get settings values
  shell         Interactive scraping console
  startproject  Create new project
  version       Print Scrapy version
  view          Open URL in browser, as seen by Scrapy

Use "scrapy <command> -h" to see more info about a command
```

### 创建项目
使用 scrapy 的第一件事情就是创建项目
```
scrapy startproject myproject [project_dir]
```

**project_dir** 指定项目创建位置。

### 控制项目
可以在项目内部进行控制项目，比如: 创建一个新的爬虫 spider
```
scrapy genspider mydomain mydomain.com
```
一些命令必须在项目内部运行。

## scrapy 可用的命令
可以使用下面的命令查看某一个命令的说明

```
scrapy <command> -h
```
如果要查看所有，就是用

```
scrapy -h
```

Global commands: 全局命令

     startproject
     genspider
     settings
     runspider
     shell
     fetch
     view
     version

Project-only commands: 项目内部命令
     crawl
     check
     list
     edit
     parse
     bench

### startproject 创建项目
    语法: scrapy startproject <project_name> [project_dir]
    是否需要项目: no
创建一个 Scrapy 项目，以 **project_name** 命名。 如果 **project_dir** 不存在，就在运行指令目录创建项目。

### genspider spider 创建
    语法: scrapy genspider [-t template] <name> <domain>
    是否需要项目: no
在项目 **spiders** 文件夹下面新建一个spider 文件， 如果是在项目内 **<name>** 是爬虫的名称，**<domain>** 用来设置 **allowed_domains** 和 **start_urls** 

栗子：
```
$ scrapy genspider -l
Available templates:
  basic
  crawl
  csvfeed
  xmlfeed

$ scrapy genspider example example.com
Created spider 'example' using template 'basic'

$ scrapy genspider -t crawl scrapyorg scrapy.org
Created spider 'scrapyorg' using template 'crawl'
```

### crawl 启动爬虫
    语法: scrapy crawl <spider>
    是否需要项目: yes
启动爬虫
栗子:
```
$ scrapy crawl myspider
[ ... myspider starts crawling ... ]
```

### check
    语法: scrapy check [-l] <spider>
    是否需要项目: yes
运行检查
栗子:
```
$ scrapy check -l
first_spider
  * parse
  * parse_item
second_spider
  * parse
  * parse_item

$ scrapy check
[FAILED] first_spider:parse_item
>>> 'RetailPricex' field is missing

[FAILED] first_spider:parse
>>> Returned 92 requests, expected 0..4
```

### list
    语法: scrapy check [-l] <spider>
    是否需要项目: yes

列出当前项目可用的爬虫
栗子:
```
$ scrapy list
spider1
spider2
```




## 环境参数说明

|类型|关键词|说明|
|:-- | :---: | :------ |
|系统参数|SCRAPY_PROJECT|设置默认scrapy 项目|

