---
title: github_actions
description: 转载来源于网络
date: 2024-09-28 00:00:00+0000
slug: github_actions
categories:
    - github
tags:
    - workflow
weight: 6 
---

Github action通用代码 \| 科技小岛![加载头像](https://jsd.onmicrosoft.cn/gh/qwer-search/blog-img@main/img/202407081601052.jpg)网页[![科技小岛](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)科技小岛](https://wwww.kejiland.com/ "科技小岛")项目[![哪吒探针](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)哪吒探针](https://nezha.gvkoyeb.eu.org/ "哪吒探针")[科技小岛](/)[PAGE\_NAME](javascript:anzhiyu.scrollToDest(0, 500)) [文章](javascript:void(0);)* [文章](/archives/)
* [分类](/categories/)
* [标签](/tags/)
 [友链](javascript:void(0);)* [友人帐](/link/)
* [留言板](/comments/)
 [关于](javascript:void(0);)* [关于本人](/about/)
 [搜索](javascript:void(0); "搜索🔍")* [![微信](https://npm.elemecdn.com/anzhiyu-blog@1.1.6/img/post/common/qrcode-weichat.png)](https://npm.elemecdn.com/anzhiyu-blog@1.1.6/img/post/common/qrcode-weichat.png)微信
* [![支付宝](https://npm.elemecdn.com/anzhiyu-blog@1.1.6/img/post/common/qrcode-alipay.png)](https://npm.elemecdn.com/anzhiyu-blog@1.1.6/img/post/common/qrcode-alipay.png)支付宝
互动 最新评论正在加载中...音乐灵魂的碰撞💥文章归档* [九月 20241篇](/archives/2024/09/)
* [八月 20243篇](/archives/2024/08/)
* [七月 202412篇](/archives/2024/07/)


---

[0](javascript:void(0);)原创[网络隧道](/categories/%E7%BD%91%E7%BB%9C%E9%9A%A7%E9%81%93/) [Github](/tags/Github/)# Github action通用代码

发表于2024\-07\-27更新于2024\-09\-28字数总计:1\.2k阅读时长:4分钟阅读量: 中国![](https://jsd.onmicrosoft.cn/gh/qwer-search/blog-img@main/img/202407272256764.png)[网络隧道](/categories/%E7%BD%91%E7%BB%9C%E9%9A%A7%E9%81%93/)[Github](/tags/Github/)# Github action通用代码

拾荒开拓者2024\-07\-272024\-09\-28## 构建Docker镜像

本文介绍了一段GitHub Actions工作流代码，它用于自动构建并推送Docker镜像到DockerHub。该工作流在GitHub仓库中配置，并在触发时执行以下步骤：


1. 设置QEMU以支持多架构构建。
2. 设置Docker Buildx以便使用高级构建功能。
3. 登录到DockerHub。
4. 构建并推送Docker镜像到DockerHub。


### 工作流代码示例

以下是工作流的完整代码：




| ``` 123456789101112131415161718192021222324252627 ``` | ``` name: DeployDockerHubon:  workflow_dispatch:jobs:  docker:    runs-on: ubuntu-latest    steps:      - name: Set up QEMU        uses: docker/setup-qemu-action@v1      - name: Set up Docker Buildx        uses: docker/setup-buildx-action@v1      - name: Login to DockerHub        uses: docker/login-action@v1        with:          username: ${{ secrets.DOCKERHUB_USER }}          password: ${{ secrets.DOCKERHUB_PASS }}      - name: Build and push Docker image        id: docker_build        uses: docker/build-push-action@v2        with:          push: true          tags: ${{ secrets.DOCKERHUB_USER }}/${{ secrets.DOCKER_IMAGE_NAME }}:latest ``` |
| --- | --- |


### 配置说明

1. **工作流触发**：使用`workflow_dispatch`触发器，可以手动触发工作流。
2. **QEMU设置**：使用`docker/setup-qemu-action@v1`来设置QEMU，以支持多架构构建。
3. **Docker Buildx设置**：使用`docker/setup-buildx-action@v1`来设置Docker Buildx，以便使用其高级构建功能。
4. **DockerHub登录**：使用`docker/login-action@v1`来登录到DockerHub。用户名和密码通过GitHub Secrets传递，确保安全性。
5. **构建并推送Docker镜像**：使用`docker/build-push-action@v2`来构建并推送Docker镜像。镜像的标签通过GitHub Secrets传递，确保灵活性和安全性。


### 准备工作

在使用上述工作流之前，需要进行以下准备工作：


1. **创建GitHub Secrets**：


	* `DOCKERHUB_USER`：你的DockerHub用户名。
	* `DOCKERHUB_PASS`：你的DockerHub密码。
	* `DOCKER_IMAGE_NAME`：你希望推送到DockerHub的镜像名称（例如`koyeb-python`）。你可以在GitHub仓库的Settings \-\> Secrets页面添加这些Secrets。


### 使用步骤

1. **添加工作流文件**：在你的GitHub仓库中，创建一个`.github/workflows/deploy-to-dockerhub.yml`文件，并将上述代码粘贴到文件中。
2. **配置Secrets**：按照准备工作中的说明，在GitHub仓库的Settings \-\> Secrets页面添加必要的Secrets。
3. **手动触发工作流**：在GitHub仓库的Actions页面，找到这个工作流并手动触发它。


完成以上步骤后，GitHub Actions将自动构建你的Docker镜像并推送到DockerHub


## GitHub Actions定时推送

本文介绍了一段GitHub Actions工作流代码，它用于自动更新README文件并重新部署到Cloudflare。该工作流可以手动触发或按照计划任务定期触发。


### 工作流代码示例

以下是工作流的完整代码：




| ``` 1234567891011121314151617181920212223242526272829303132333435363738394041 ``` | ``` name: Redeploy Cloudflareon:  workflow_dispatch:  schedule:    - cron: '*/30 * * * * *'jobs:  Redeploy:    name: Redeploy Cloudflare    runs-on: ubuntu-latest    steps:      - uses: actions/checkout@v4.0.0        with:          fetch-depth: 1      - name: Renew README.md        run: |            DATE=$(date "+%Y/%m/%d %H:%M:%S")            echo "${DATE}" > README.md            git checkout --orphan tmp_work            git branch -d main            echo "DATE=${DATE}" >> $GITHUB_ENV      - name: Upload to repository        uses: stefanzweifel/git-auto-commit-action@v4.16.0        with:          commit_message: Auto deploy by Github Actions, ${{ env.DATE }}          create_branch: true          branch: main          push_options: --force      - name: Delete old workflow runs        uses: MajorScruffy/delete-old-workflow-runs@v0.3.0        env:          GITHUB_TOKEN: ${{ github.token }}        with:          repository: ${{ github.repository }}          older-than-seconds: 3600 ``` |
| --- | --- |


### 配置说明

1. **工作流触发**：
	* `workflow_dispatch`：允许手动触发工作流。
	* `schedule`：设置了一个计划任务，每30分钟触发一次（`*/30 * * * * *`表示每30分钟执行一次）。
2. **工作流步骤**：
	* **Checkout代码**：使用`actions/checkout@v4.0.0`将仓库的代码检出，`fetch-depth: 1`表示只获取最新的提交。
	* **更新README.md**：获取当前日期和时间，并将其写入README.md文件。使用临时分支`tmp_work`，删除`main`分支，然后将日期写入环境变量。
	* **上传到仓库**：使用`stefanzweifel/git-auto-commit-action@v4.16.0`自动提交更新，并强制推送到`main`分支。
	* **删除旧的工作流运行**：使用`MajorScruffy/delete-old-workflow-runs@v0.3.0`删除超过1小时的旧工作流运行记录，以保持仓库整洁。


### 准备工作

在使用上述工作流之前，需要进行以下准备工作：


1. 配置GitHub仓库：


	* 确保你的GitHub仓库已经设置好，并且你有权限添加和配置GitHub Actions。
2. 设置GitHub Secrets：


	* `GITHUB_TOKEN`：GitHub Actions自动生成的token，无需手动设置，代码中已引用`${{ github.token }}`。


### 使用步骤

1. **添加工作流文件**：在你的GitHub仓库中，创建一个`.github/workflows/redeploy-cloudflare.yml`文件，并将上述代码粘贴到文件中。
2. **手动触发工作流**：在GitHub仓库的Actions页面，找到这个工作流并手动触发它，或者等待计划任务自动触发。


完成以上步骤后，GitHub Actions将自动更新README文件，并强制推送到`main`分支，同时删除超过1小时的旧工作流运行记录。


### 注意事项

* 请确保计划任务的cron表达式符合你的需求。当前设置为每30分钟触发一次，如果你希望更改频率，可以调整cron表达式。
* 强制推送会覆盖`main`分支上的任何更改，请谨慎使用。如果你的仓库有其他协作者，建议提前沟通。


[![头像](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7 "头像")![头像](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7 "头像")](/ "头像")拾荒开拓者自己动手 丰衣足食[原创](https://wwww.kejiland.com/post/6d866e60.html "该文章为原创文章，注意版权协议")Github action通用代码使用手机访问这篇文章本博客所有文章除特别声明外，均采用 [CC BY\-NC\-SA 4\.0](https://creativecommons.org/licenses/by-nc-sa/4.0/) 许可协议。转载请注明来自 [科技小岛](https://wwww.kejiland.com)！ [Github2](/tags/Github/)[![cover of previous post](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)上一篇Glitch和Back4app部署Argo节点](/post/c91e89a3.html)[![cover of next post](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)下一篇python脚本合并订阅](/post/2391350b.html)喜欢这篇文章的人也看了[![cover](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 2024\-08\-01python脚本合并订阅](/post/2391350b.html "python脚本合并订阅")

---

 评论[匿名评论](javascript:void(0))[隐私政策](/privacy)✅ 你无需删除空行，直接评论以获取最佳展示效果![avatar](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)这有关于**产品、设计、开发**相关的问题和看法，还有**文章翻译**和**分享**。相信你可以在这里找到对你有用的**知识**和**教程**。[# 拾荒开拓者

自己动手 丰衣足食](/)文章目录1. [构建Docker镜像](#%E6%9E%84%E5%BB%BADocker%E9%95%9C%E5%83%8F)
	1. [工作流代码示例](#%E5%B7%A5%E4%BD%9C%E6%B5%81%E4%BB%A3%E7%A0%81%E7%A4%BA%E4%BE%8B)
	2. [配置说明](#%E9%85%8D%E7%BD%AE%E8%AF%B4%E6%98%8E)
	3. [准备工作](#%E5%87%86%E5%A4%87%E5%B7%A5%E4%BD%9C)
	4. [使用步骤](#%E4%BD%BF%E7%94%A8%E6%AD%A5%E9%AA%A4)
2. [GitHub Actions定时推送](#GitHub-Actions%E5%AE%9A%E6%97%B6%E6%8E%A8%E9%80%81)
	1. [工作流代码示例](#%E5%B7%A5%E4%BD%9C%E6%B5%81%E4%BB%A3%E7%A0%81%E7%A4%BA%E4%BE%8B-1)
	2. [配置说明](#%E9%85%8D%E7%BD%AE%E8%AF%B4%E6%98%8E-1)
	3. [准备工作](#%E5%87%86%E5%A4%87%E5%B7%A5%E4%BD%9C-1)
	4. [使用步骤](#%E4%BD%BF%E7%94%A8%E6%AD%A5%E9%AA%A4-1)
	5. [注意事项](#%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9)
最近发布[![免费域名可托管Cloudflare](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)](/post/a84174db.html "免费域名可托管Cloudflare")[免费域名可托管Cloudflare](/post/a84174db.html "免费域名可托管Cloudflare")2024\-09\-02[![Cloudflare R2部署图床](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)](/post/d2d21f30.html "Cloudflare R2部署图床")[Cloudflare R2部署图床](/post/d2d21f30.html "Cloudflare R2部署图床")2024\-08\-25[![telegraph-Image：搭建专属开源图床](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)](/post/75e63229.html "telegraph-Image：搭建专属开源图床")[telegraph\-Image：搭建专属开源图床](/post/75e63229.html "telegraph-Image：搭建专属开源图床")2024\-08\-18[![python脚本合并订阅](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)](/post/2391350b.html "python脚本合并订阅")[python脚本合并订阅](/post/2391350b.html "python脚本合并订阅")2024\-08\-01[![Github action通用代码](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)](/post/6d866e60.html "Github action通用代码")[Github action通用代码](/post/6d866e60.html "Github action通用代码")2024\-07\-27©2020 \- 2024 By 拾荒开拓者[文章16](/archives/ "archive")[标签13](/tags/ "tag")[分类2](/categories/ "category")功能[显示模式](javascript:void(0); "显示模式")网页[![科技小岛](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)科技小岛](https://wwww.kejiland.com/ "科技小岛")项目[![哪吒探针](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)哪吒探针](https://nezha.gvkoyeb.eu.org/ "哪吒探针") [文章](javascript:void(0);)* [文章](/archives/)
* [分类](/categories/)
* [标签](/tags/)
 [友链](javascript:void(0);)* [友人帐](/link/)
* [留言板](/comments/)
 [关于](javascript:void(0);)* [关于本人](/about/)
标签[Argo节点1](/tags/Argo%E8%8A%82%E7%82%B9/)[Cloudflare Tunnel1](/tags/Cloudflare-Tunnel/)[DNS1](/tags/DNS/)[Github2](/tags/Github/)[Koyeb部署哪吒1](/tags/Koyeb%E9%83%A8%E7%BD%B2%E5%93%AA%E5%90%92/)[Office1](/tags/Office/)[koyeb1](/tags/koyeb/)[lxmusic1](/tags/lxmusic/)[v2rayN1](/tags/v2rayN/)[免费域名1](/tags/%E5%85%8D%E8%B4%B9%E5%9F%9F%E5%90%8D/)[图床3](/tags/%E5%9B%BE%E5%BA%8A/)[影视1](/tags/%E5%BD%B1%E8%A7%86/)[直播源1](/tags/%E7%9B%B4%E6%92%AD%E6%BA%90/)

---

博客快捷键shift K关闭快捷键功能shift A打开/关闭中控台shift M播放/暂停音乐shift D深色/浅色显示模式shift S站内搜索shift R随机访问shift H返回首页shift F友链鱼塘shift L友链页面shift P关于本站shift I原版/本站右键菜单繁播放音乐搜索 数据库加载中

---

复制选中文本粘贴文本引用到评论新窗口打开复制链接地址复制此图片下载此图片新窗口打开图片站内搜索百度搜索播放音乐切换到上一首切换到下一首查看所有歌曲复制歌名随便逛逛[博客分类](/categories/)[文章标签](/tags/)[复制地址](javascript:void(0);)[关闭热评](javascript:void(0);)[深色模式](javascript:void(0);)[轉為繁體](javascript:void(0);)通知你好呀