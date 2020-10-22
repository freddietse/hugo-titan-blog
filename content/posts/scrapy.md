---
title: "Scrapy 完全指南"
date: "2020-08-26"
---

Scrapy 是一个用 Python 编写的开源网络爬虫框架。

<!--more-->

### 快速开始

#### 安装 Scrapy

安装 Scrapy 最好的方式是使用 Anaconda ，而不是 `pip`，这样可以避免大多数安装问题。

a. 安装 [Anaconda](https://www.anaconda.com/products/individual)

b. 运行 Anaconda Prompt (anaconda3)

c. 输入如下命令行安装 Scrapy：

```bash
$ conda install -c conda-forge scrapy
```

#### 创建项目

```bash
$ scrapy startproject tutorial
```

上面的命令行将会创建一个 `tutorial` 目录，包含如下内容：

```bash
tutorial/
    scrapy.cfg            # deploy configuration file

    tutorial/             # project's Python module, you'll import your code from here
        __init__.py

        items.py          # project items definition file

        middlewares.py    # project middlewares file

        pipelines.py      # project pipelines file

        settings.py       # project settings file

        spiders/          # a directory where you'll later put your spiders
            __init__.py
```

#### 运行项目

```bash
$ scrapy crawl quotes
```

### 导出 JSON 文件

```bash
$ scrapy crawl your_spider_name -o data.json
```

如果需要在 JSON 文件中显示中文，只要在 `setting.py ` 文件中设置：

```python
FEED_EXPORT_ENCODING = 'utf-8'
```



