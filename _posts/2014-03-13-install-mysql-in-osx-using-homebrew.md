---
layout: post
title: 在OSX下配置mysql
tags: [mysql]
published: false
---

#### 安装mysql

``` bash
homebrew install mysql
```

#### 开机自动启动

``` bash
$ ln -sfv /usr/local/opt/mysql/homebrew.mxcl.mysql.plist ~/Library/LaunchAgents
$ launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
```

#### 启动mysql server

``` bash
$ mysql.server start
```

#### 配置root密码

``` bash
$ mysql_secure_installation
```

#### 登录mysql

``` bash
$ mysql -u root -p
```

#### 卸载mysql

``` bash
$ brew remove mysql
$ brew cleanup
$ launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
$ rm -Rf ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
```
