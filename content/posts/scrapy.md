---
title: "Scrapy 完全指南"
date: "2020-08-26"
---

Scrapy 是一个用 Python 编写的开源网络爬虫框架。

<!--more-->

### 快速开始

#### 安装 Scrapy

Windows 上安装 Scrapy 最好的方式是使用 Anaconda ，而不是 `pip`，这样可以避免大多数安装问题。

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

