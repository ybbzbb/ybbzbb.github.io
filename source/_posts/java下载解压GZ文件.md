---
title: java下载解压GZ文件
tags:
  - gz
  - zip
  - java
categories:
  - java杂技
keywords: 'java,下载,解压,gz,zip,GZ,ZIP'
id: javaacrobatics001
date: 2019-07-25 18:44:15
---


# java下载解压GZ文件

>今天遇到一个业务，将线上的 **gz** 压缩包下载下来，然后获取里面的 **JSON** 数据处理一下放入 **MongoDB** 中。


```
public static void main(String[] args) {
    String uri = "http://localhost:4000/a.json.gz";

    URLConnection conn = null;

    try {
        URL url = new URL(uri);
        conn = url.openConnection();
    } catch (IOException e) {
        e.printStackTrace();
    }

    StringBuffer sb = null;
    try (final InputStream inStream = conn.getInputStream();
            final GZIPInputStream gzip = new GZIPInputStream(inStream);
            final InputStreamReader reader = new InputStreamReader(gzip);
            final BufferedReader in = new BufferedReader(reader);) {

        sb = new StringBuffer();
        String read;
        while ((read = in.readLine()) != null ){
            sb.append(read);
        }
    } catch (IOException e) {
        e.printStackTrace();
    }

    System.out.println(sb.toString());
}
```

注意:
1. 有的时候下载解析之后会有很多的空格使用 **alibaba.fastjson.JSONObject** 判断的时候，判断不是 **JSON** 格式，这里需要提前清空一下所有的空格
```
final String data = sb.toString().replaceAll("\\s","");
```

2. 下载数据使用 **alibaba.fastjson.JSONObject** 判断是否为 **JSON** 过程中，需要区分集合和对象

3. 可以用node.js 或者 nginx 起一个简单的服务，将文件放入本地进行测试。
