---
layout: post
title: CF应用是怎样打包的？
tags: cloudfoundry
---

> 本文翻译自 [how applications are staged][1]


![app push flow diagram][2]

1. 在命令行中，开发者进入其应用所在目录，
使用cf命令行工具(cf command line tool)发出一个push命令。
2. cf命令行工具就会告诉 CC(Cloud Controller)，让其为这个应用创建记录。
3. CC存储应用元信息（比如：应用名称，用户指定的实例数量，以及buildpack）。
4. cf命令行工具上传应用文件。
5. CC将应用文件存储在blobstore中。
6. cf命令行工具发出一个启动应用命令。
7. 因为应用还没有被打包，那么，CC就从DEA(Droplet Execution Agent)池中选择一个DEA实例，这个DEA会按照buildpack中的指令来打包应用。
8. 同时，正在进行打包工作的这个DEA会不断的输出打包过程产生的信息，以便开发者分析应用打包过程中可能出现的问题。
9. DEA将打包后的应用压缩成一个压缩包，叫做 **droplet**，并存储在blobstore中。
这个打包后的应用会被缓存，并且会在下次打包这个应用时使用。
10. DEA还会向CC报告：已完成应用打包。
11. CC从DEA池中选择一个或多个DEAs来运行打包后的应用。
12. 最后，运行中的DEAs向CC报告应用的状态。

[1]: http://docs.cloudfoundry.org/concepts/how-applications-are-staged.html
[2]: {{site.url}}/assets/2014/03/05/app_push_flow_diagram.png
