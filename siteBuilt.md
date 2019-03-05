---
title: 在 CentOS 系统服务器上利用 Hexo 和 Nginx 搭建个人博客
date: 2019-02-24 22:26:27
tags: [Hexo, Next, CentOS, Nginx, Git, Node.js]
categories:
- 网站建设
- 博客搭建
---
本文介绍的是在云服务器系统 CentOS 上利用 Hexo 框架搭建自己的博客网站。不管你是不是小白，跟着教程走就对了，我也是这样过来的。我会讲的很详细，当然在部署过程中会有许多坑，我也会把注意点和解决方案列出。<!-- more -->

# 准备工作

## 云服务器

阿里云上的学生服务器有两款，轻量应用服务器和 ECS 服务器，价格都是 ￥9.50，我选择的是前者，服务器系统选择了 CentOS 7.3。

两者明显区别是：

- 轻量应用服务器的带宽 5M，每月 1000G 流量，操作较简单，还多了多个集成的应用镜像；
- ECS服务器的带宽 1M，流量无限，操作比较复杂，其它方面的配置基本相同。

如果仅作为博客的服务器，我认为前者较好，流量肯定够用，带宽又高一些。

## 个人域名

首先，域名不是必须的，有两种方案。

1. 在阿里云上购买域名并进行备案。备案时间大概十几天。
2. 利用 Github Pages 部署。

本文采用第一种方案，若想利用 Github Pages 部署，请参考其它教程。

> 注意：若域名未添加主机记录，域名前加上 `www.` 的前缀将无法访问。
>
> 具体操作：在阿里云中找到并打开 `云解析 DNS` 选择你的域名，点击 `添加记录` ，记录类型一栏选择 `A` 类型，主机记录一栏填入 `www` ，解析线路选择 `默认`，记录值填你的服务器 `公网IP` 即可。

# 安装 Hexo 并初始化

## 安装

服务器需要先安装 Git 和 Node.js，然后安装 Hexo，安装请参照 [Hexo 官方文档](https://hexo.io/zh-cn/docs/)。

## 初始化

安装好 Hexo 后， 可继续参照 [Hexo 官方文档](https://hexo.io/zh-cn/docs/commands) 进行操作和学习。如果不想看文档，可进行以下操作：

### 新建站点目录

```s
hexo init <folder>
```

> 注意：`<folder>` 为自定义的站点文件夹名称，比如可以写为 `blog`。

### 生成静态文件

```s
hexo generate
```

> 注意：可简写为 `hexo g`

### 安装 npm

```s
npm install
```

> 新建完成后，指定文件夹的目录如下：
> ```s
 .
 ├── _config.yml
 ├── package.json
 ├── scaffolds
 ├── source
 |   ├── _drafts
 |   └── _posts
 └── themes
```

### 启动服务器

```s
hexo server
```

> 注意：可简写为 `hexo s`

### 验证

在浏览器中输入 `http://<你的IP地址>:4000`，即可看到第一次运行的状况，显示为一篇《Hello World》的文章，按 `Ctrl+C` 结束访问。

> 注意：服务器的 `4000` 端口需要开启，若未开启无法打开网站。
> 具体操作为：找到 `安全` 中的 `防火墙`，点击 `添加规则`，在 `端口范围` 一栏输入 `4000`，点击 `确定` 即可。
> 如果服务器绑定了域名，此时可以在浏览器中用域名访问。

# 安装并配置 Nginx

安装 Nginx 可以让网站随时都能访问，无需进行上述 `hexo server` 等操作。

## 安装 Nginx

```s
yum install -y nginx
```

## 启动 Nginx

```s
systemctl start nginx
```

上述步骤操作完成后，在浏览器中输入 `http://<你的IP地址>` 或你的网站域名，若看到 "Welcome to nginx on Fedora" 的页面，表示 Nginx 启动成功。

## 继续输入以下命令使Nginx开机自动启动：

```s
systemctl enable nginx
```

## 配置静态服务器访问路径

Nginx 需要配置静态资源的路径信息才能通过 url 正确访问到服务器上的静态资源，所以要将Nginx 的访问路径改为 Hexo 生成的静态资源的路径。

输入

```s
vi /etc/nginx/nginx.conf
```

1. 如果不熟悉 Linux 操作，接下来按 `i` 对 Nginx 的配置文件内容进行修改。
2. 将位于 `42行` 的 `root /usr/share/nginx/html` 修改为：`root /…/<你的文件夹名称>/public`，比如我修改为 `root /home/admin/blog/public`。
3. 将位于 `5行` 的 `user` 改为 `root`。
4. 按 `ESC`,再按 `:wq` 保存退出。

## 重启 Nginx

```s
nginx -s reload
```

此时在浏览器中再输入 `http://<你的IP地址>` 或你的网站域名，若网站显示上文的 Hexo 第一次运行的样子，即显示为一篇《Hello World》的文章，则说明配置成功。

# Hexo 配置

## 站点配置

参考 [Hexo 官方文档](https://hexo.io/zh-cn/docs/configuration)。

## 主题配置

Next 主题是 Hexo 的一个主题系列，我现在使用的 [NexT.Gemini](https://github.com/iissnan/hexo-theme-next) 就是其中之一。

基本使用配置参考 [Next 官方文档](https://theme-next.iissnan.com/getting-started.html)。

更多详细配置修改参考 [掘金 Jaybo](https://juejin.im/post/5a71ab9f518825735300ee6c)

写作参考 [掘金 Sun-xyu](https://juejin.im/post/5bcd2d395188255c3b7dc1db#heading-38)