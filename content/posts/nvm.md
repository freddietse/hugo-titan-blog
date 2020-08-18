---
title: "NVM – Node.js 版本管理器"
date: "2020-08-15"
---

### 安装配置

第 1 步：下载 [nvm-windows](https://github.com/coreybutler/nvm-windows/releases) ( 推荐选择 `nvm-setup.zip` )。

第 2 步：安装成功后，在安装根目录打开 `settings.txt` 文件，添加如下内容：

```bash
...
arch: 64
proxy: none
node_mirror:npm.taobao.org/mirrors/node/
npm_mirror:npm.taobao.org/mirrors/npm/
```

### 使用方法

```bash
# 安装最新版 Node.js
$ nvm install node

# 安装固定版本 Node.js
$ nvm install 12.18.0

# 查看 Node.js 版本列表
$ nvm list

# 切换 Node.js 版本
$ nvm use 12.18.0
```