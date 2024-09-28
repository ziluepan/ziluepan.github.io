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


---

## 构建Docker镜像

本文介绍了一段GitHub Actions工作流代码，它用于自动构建并推送Docker镜像到DockerHub。该工作流在GitHub仓库中配置，并在触发时执行以下步骤：


1. 设置QEMU以支持多架构构建。
2. 设置Docker Buildx以便使用高级构建功能。
3. 登录到DockerHub。
4. 构建并推送Docker镜像到DockerHub。


### 工作流代码示例

以下是工作流的完整代码：
 ``` workflow
name: DeployDockerHub

on:
  workflow_dispatch:

jobs:
  docker:
    runs-on: ubuntu-latest
    steps:
      - name: Set up QEMU
        uses: docker/setup-qemu-action@v1

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1

      - name: Login to DockerHub
        uses: docker/login-action@v1
        with:
          username: ${{ secrets.DOCKERHUB_USER }}
          password: ${{ secrets.DOCKERHUB_PASS }}

      - name: Build and push Docker image
        id: docker_build
        uses: docker/build-push-action@v2
        with:
          push: true
          tags: ${{ secrets.DOCKERHUB_USER }}/${{ secrets.DOCKER_IMAGE_NAME }}:latest

``` 



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

```workflow
name: Redeploy Cloudflare

on:
  workflow_dispatch:
  schedule:
    - cron: '*/30 * * * * *'

jobs:
  Redeploy:
    name: Redeploy Cloudflare

    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4.0.0
        with:
          fetch-depth: 1

      - name: Renew README.md
        run: |
            DATE=$(date "+%Y/%m/%d %H:%M:%S")
            echo "${DATE}" > README.md
            git checkout --orphan tmp_work
            git branch -d main
            echo "DATE=${DATE}" >> $GITHUB_ENV

      - name: Upload to repository
        uses: stefanzweifel/git-auto-commit-action@v4.16.0
        with:
          commit_message: Auto deploy by Github Actions, ${{ env.DATE }}
          create_branch: true
          branch: main
          push_options: --force

      - name: Delete old workflow runs
        uses: MajorScruffy/delete-old-workflow-runs@v0.3.0
        env:
          GITHUB_TOKEN: ${{ github.token }}
        with:
          repository: ${{ github.repository }}
          older-than-seconds: 3600

```




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


- 转载来源网络