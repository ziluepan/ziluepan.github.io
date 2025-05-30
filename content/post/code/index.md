---
title: codex
description: 转载来源于网络
date: 2024-09-23 00:00:00+0000
slug: codex
tags:
    - code
weight: 3 
---



#node.js

- nvm install <version>：安装指定版本的 Node.js。
- nvm use <version>：切换到指定版本的 Node.js。
- nvm ls：列出已安装的所有 Node.js 版本。Add commentMore actions
- nvm alias <name> <version>：给指定版本创建别名。
- nvm run <version> <script>：在指定版本下运行脚本。
- nvm current：显示当前正在使用的 Node.js 版本。
- nvm uninstall <version>：卸载指定版本的 Node.js。



通过nvm安装node
查看node.js最新的一批版本(版本号：偶数是绝对稳定版本 基数是不一定稳定版本)
-nvm list available


- 安装指定的版本：nvm install 10.23.0
- 查看安装的所有版本：nvm list 或 nvm ls
- 进入或切换版本：nvm use 18.15.0


- 安装成功后会成对应的文件夹，nvm文件夹下


- 检查是否安装成功： node -v 查看node的版本、npm -v 查看npm的版本