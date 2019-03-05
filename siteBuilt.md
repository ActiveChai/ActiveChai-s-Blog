---
title: 在 CentOS 系统服务器上利用 Hexo 和 Nginx 搭建个人博客
date: 2019-02-24 22:26:27
tags: [Hexo, Next, Nginx, SEO, Git, Node.js, CentOS, HTTPS]
categories:
- 博客搭建
---
本文介绍的是在云服务器系统 CentOS 上利用 Hexo 框架搭建自己的博客网站。不管你是不是小白，跟着教程走就对了，我也是这样过来的。我会讲的很详细，当然在部署过程中会有许多坑，我也会把注意点和解决方案列出。下面就开始了。<!-- more -->

# 准备工作

## 云服务器

阿里云上的学生服务器有两款，轻量应用服务器和 ECS，价格都是 ￥9.50，我选择的是前者，系统选择了 CentOS 7.3。

两者明显区别是：

- 轻量应用服务器的带宽 5M，每月 1000G 流量，操作较简单，还多了多个集成的应用镜像；
- ECS服务器的带宽 1M，流量无限，操作比较复杂，其它方面的配置基本相同。

如果仅作为博客的服务器，我认为前者较好，流量肯定够用，带宽又高一些。

## 个人域名

首先，域名不是必须的，有两种方案。

1. 在阿里云上购买域名并进行备案。备案时间大概十几天。
2. 利用 Github Pages 部署。

本文采用第一种方案，若想利用 Github Pages 部署，请参考其它教程。

## 安装 Git

```s
sudo yum install git-core
```

## 安装 Node.js

### 安装 nvm（Node版本管理器）

```s
curl https://raw.github.com/creationix/nvm/v0.33.11/install.sh | sh
```

或者

```s
wget -qO- https://raw.github.com/creationix/nvm/v0.33.11/install.sh | sh
```

### 重启终端并执行下列命令安装 Node.js

```s
nvm install stable
```

测试安装是否成功

```s
node -v
```

v11.10.0

```s
npm -v
```

6.7.0

# 安装 Hexo

```s
npm install -g hexo-cli
```

## 安装 Hexo 完成后，请执行下列命令，Hexo 将会在指定文件夹中新建所需要的文件。

```s
hexo init <folder>
```

```s
cd <folder>
```

```s
npm install
```

## 新建完成后，指定文件夹的目录如下：

 ```s
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
 ```

## _config.yml

网站的配置信息，您可以在此配置大部分的参数。

## 