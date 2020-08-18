---
title: 'Homebrew - 最好用的 macOS 包管理器'
date: '2020-08-15'
---

Homebrew 是 macOS 上的一个包管理器，可以用命令行安装、卸载以及更新各种软件包，类似于 `centos` 的 `yum` 。

### 安装配置

如果按照官网的方法安装 Homebrew 的话，会提示如下的错误：

![](../images/img_01.png)

从错误信息来看是网络连接错误，但是我无论开全局代理还是更改 DNS 都没法解决这个问题。

下面的安装方法是我从[一篇博文](https://www.cnblogs.com/huanhao/p/installbrew.html)中看到的，亲测可行。让我们开始吧！

#### 第 1 步：创建 Homebrew 目录

打开终端，执行下面的命令，进行创建 `Homebrew `文件夹：

```bash
sudo mkdir /usr/local/Homebrew
```

#### 第 2 步：同步 brew 库

```bash
sudo git clone https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git /usr/local/Homebrew
```

#### 第 3 步：添加环境变量

```bash
sudo ln -s /usr/local/Homebrew/bin/brew /usr/local/bin/brew
```

#### 第 4 步：同步 core 库

```bash
sudo mkdir -p /usr/local/Homebrew/Library/Taps/homebrew/homebrew-core

sudo git clone https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git /usr/local/Homebrew/Library/Taps/homebrew/homebrew-core
```

#### 第 5 步：同步 cask 库

```bash
sudo mkdir -p /usr/local/Homebrew/Library/Taps/homebrew/homebrew-cask
sudo git clone https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-cask.git /usr/local/Homebrew/Library/Taps/homebrew/homebrew-cask
```

#### 第 6 步：提权

```bash
sudo chown -R $(whoami) /usr/local/Cellar
sudo chown -R $(whoami) /usr/local/Homebrew
```

#### 第 7 步：查看版本号

```bash
brew -v
```

### 使用方法

```bash
# 安装软件
$ brew install yarn

# 更新软件
$ brew upgrade yarn

# 卸载软件
$ brew remove yarn

# 列出当前安装的软件
$ brew list
```

### 参考资料

- huanhao, 《安装brew的正确姿势》，https://www.cnblogs.com/huanhao/p/installbrew.html, 2020-04-08 更新
- chinaxieshuai, 《MAC上安装brew》，https://segmentfault.com/a/1190000021373786, 2019-12-23更新

