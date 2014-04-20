---
layout: post
title: 在LightTable中使用RubyRepl
tags: light-table ruby
published: false
---

## 写在前面

本文介绍如何在[LightTable][light-table]中使用[RubyRepl][ruby-repl]。

如果读者还不了解LightTable，可以先去感受下LT（建议读完tutorial）。
另外，[the-ide-as-data][the-ide-as-data]介绍了该编辑器背后的思想。

当前版本的LT并不原生支持ruby，所以有了RubyRepl。

以下内容，基于读者对LT有入门的了解，比如说，

- 如何调出Commands（默认的是ctrl+space快捷键）。
- 如何在Commands中查找所需的设置。
- 如何自定义User Behaviors。

## 安装RubyRepl

使用LT plugin-manager查找RubyRepl，直接点击安装即可。


## 配置RubyRepl

以下配置是为了支持rbenv。

首先设置User Behaviors，添加 `:editor.ruby [:lt.objs.langs.ruby/use-rbenv]` 配置。

然后安装 `eventmachine`, `json`, `method_source`。

Done。


## 使用RubyRepl

使用 cmd+enter 执行整个文件。

在Commands中，用`Ruby Instarepl: Toggle live mode`开启/关闭live mode.



[light-table]: http://www.lighttable.com/ "a editor writen by ClojureScript"
[the-ide-as-data]: http://www.chris-granger.com/2013/01/24/the-ide-as-data/ "LT - the ide as data"
[ruby-repl]: https://github.com/existentialmutt/lt-ruby "ruby repl for LT"
