---
title: 搭建Git代码仓库：代码版本控制与托管指南
tags:
  - git
  - github
  - gitea
  - TortoiseGit
  - gitee
  - 
categories:
  - 学习笔记
  - 使用教程
index_img: /img/gitea01.png
banner_img: /img/gitea.png
banner_mask_alpha: 0.6
sticky: 
comment: valine
date: 2025-05-25 15:12:01
---

<!-- 这里是模板内容 -->

# Git、Gitea 和 TortoiseGit：代码版本控制与托管指南

本文将介绍 Git 分布式版本控制系统，轻量级的自建 Git 服务 Gitea，以及 Git 的 Windows GUI 客户端 TortoiseGit。我们将探讨它们各自的功能、如何进行安装，以及如何将它们结合起来，为您的代码提供版本控制和集中托管。

## 1. Git：分布式版本控制的核心

**Git** 是一个开源的分布式版本控制软件，由 Linus Torvalds 设计开发，最初用于管理 Linux 内核开发。Git 的主要工作是创建和保存您项目的快照，并与之后的快照进行对比。它能够有效、高速地处理从很小到非常大的项目版本管理。

### Git 的工作区、暂存区和版本库

理解 Git 的关键在于了解其三种文件状态及其交互方式:

*   **工作目录 (Working Directory / workspace)**：这是您在本地计算机上看到的项目文件。您实际操作文件（查看、编辑、删除、创建）的地方就是工作目录。所有对文件的更改首先发生在这里。工作目录中的文件可能有**未跟踪 (Untracked)**（新创建，未被 Git 记录）或**已修改 (Modified)**（已被 Git 跟踪但更改未提交）状态。
*   **暂存区 (Staging Area / 索引 Index)**：这是一个临时存储区域，用于保存即将提交到本地仓库的更改。您可以选择性地将工作目录中的更改添加到暂存区，以便一次提交多个文件的更改。`git add <filename>` 命令用于将指定文件添加到暂存区，而 `git add .` 命令则用于将当前目录下的所有更改添加到暂存区。文件在暂存区时处于**已暂存 (Staged)** 状态。
*   **本地仓库 (Local Repository / 版本库)**：这是一个隐藏在 `.git` 目录中的数据库，用于存储项目的所有提交历史记录。每次提交更改时，Git 会将暂存区的内容保存到本地仓库中。`git commit -m "commit message"` 命令用于将暂存区中的更改提交到本地仓库。文件提交到本地仓库后，状态通常返回**已跟踪 (Tracked)**。
*   **远程仓库 (remote repository)**：代码托管在远程服务器上的仓库副本。

### Git 文件状态转换流程

文件状态的转换通常遵循以下流程：

1.  **未跟踪 (Untracked)**：新创建的文件最初是未跟踪的，它们存在于工作目录中，但未被 Git 跟踪。使用 `touch newfile.txt` 创建文件后，`git status` 会显示其为未跟踪。
2.  **已跟踪 (Tracked)**：通过 `git add` 命令将未跟踪的文件添加到暂存区后，文件变为已跟踪状态。`git add newfile.txt` 命令可以将文件添加到暂存区，此时 `git status` 会显示该文件已暂存。
3.  **已修改 (Modified)**：对已跟踪的文件进行更改后，这些更改会显示为已修改状态，但这些更改还未添加到暂存区。例如，使用 `echo "Hello, World!" > newfile.txt` 修改文件后，`git status` 会显示文件已修改。
4.  **已暂存 (Staged)**：使用 `git add` 命令将修改过的文件添加到暂存区后，文件进入已暂存状态，等待提交。
5.  **已提交 (Committed)**：使用 `git commit` 命令将暂存区的更改提交到本地仓库后，这些更改被记录下来，文件状态返回为已跟踪状态。例如，`git commit -m "Added newfile.txt"` 提交后，`git status` 会显示工作目录干净。

### Git 基本操作命令

这里列出一些 Git 的常用命令:

*   `git init`: 初始化仓库。
*   `git clone <repository>`: 拷贝一份远程仓库，也就是下载一个项目。克隆本地仓库时，命令格式为 `git clone <source repository> <destination repository>`，目标目录必须未创建或为空。
*   `git status`: 查看仓库当前的状态，显示有变更的文件。`git status -s` 可获得简短状态输出。
*   `git add <file>`: 添加文件到暂存区。
*   `git add .`: 添加当前目录下所有更改过的文件至暂存区。
*   `git commit -m "message"`: 提交暂存区到本地仓库。
*   `git commit -am "message"`: 将 `add` 和 `commit` 合为一步，直接提交全部已跟踪文件的修改。注意，新建的未跟踪文件不会被提交。
*   `git push`: 将本地库中的最新信息发送给远程库。例如 `git push origin master` 将当前分支推送到远程 `master` 分支。
*   `git pull`: 从远程获取最新版本到本地，并自动合并。`git pull` 相当于 `git fetch` + `git merge`。
*   `git fetch`: 从远程获取最新版本到本地，不会自动合并。
*   `git merge`: 用于从指定的 commit 合并到当前分支。
*   `git checkout`: 分支切换。也可用于从暂存区域复制文件到工作目录，丢弃本地修改 (`git checkout -- <files>`)。
*   `git diff`: 比较工作区与暂存区的不同。还有多种用法，如比较暂存区与指定提交版本的不同 (`git diff --cached [<commit>]`)，工作区与指定提交版本的不同 (`git diff <commit>`)，或两个提交版本之间的不同 (`git diff <commit>..<commit>`)。
*   `git log`: 查看历史提交记录。
*   `git reset`: 回退版本。`git reset --hard HEAD` 将当前版本重置为 HEAD。也可用于撤销最后一次 `git add`。
*   `git rm <file>`: 将文件从暂存区和工作区中删除。使用 `--cached` 只从暂存区中删除。
*   `git mv <source> <destination>`: 移动或重命名工作区文件。

要使用 Git，您需要在本地计算机上安装 Git 软件。具体的安装步骤未在源中详细说明，但通常需要从 Git 官方网站下载适用于您操作系统的安装程序。

## 2. Gitea：您的轻量级自建 Git 服务器

**Gitea** 是一个轻量级的 DevOps 平台软件。它能够帮助团队和开发者高效轻松地处理软件生命周期中的工作，包括 Git 托管、代码审查、团队协作、软件包注册和 CI/CD。Gitea 与 GitHub、Bitbucket 和 GitLab 等比较类似。

Gitea 的首要目标是创建一个**极易安装、运行快速、安装和使用体验良好**的自建 Git 服务。它采用 Go 作为后端语言，只需生成一个可执行程序即可，支持 Linux, macOS 和 Windows 等多平台及主流架构。

Gitea 的主要功能特性包括:

*   **代码托管**：支持创建和管理仓库、浏览历史、代码文件、审查和合并代码提交、管理协作者和分支等。
*   **轻量级和快速**：设计目标之一是轻量级和快速响应，性能出色，适用于资源有限的环境。
*   **易于部署和维护**：轻松部署在各种服务器上，不需要复杂的配置和依赖。
*   **安全性**：提供用户权限管理、访问控制列表等功能。
*   **代码评审**：支持 Pull Request workflow 和 AGit workflow，评审人可以在线浏览代码并提交意见。
*   **CI/CD**：Gitea Actions 支持 CI/CD 功能，兼容 GitHub Actions。
*   **项目管理**：通过看板和工单跟踪项目需求、功能和 bug。
*   **制品库**：支持超过 20 种不同的软件包管理。
*   **开源社区支持**：基于 MIT 许可证的开源项目，拥有活跃社区。
*   **多语言支持**：提供多种语言界面。

### 使用 Docker 安装 Gitea

通过 Docker 安装 Gitea 是一个推荐的方式。Gitea 在其 Docker Hub 组织内提供自动更新的 Docker 镜像。通常建议使用 `docker-compose` 进行设置。

以下是一个基于 `docker-compose.yml` 的基本设置示例，使用 SQLite3 数据库：

```yaml
version: "3"
networks:
  gitea:
    external: false
services:
  server:
    image: docker.gitea.com/gitea:1.23.8 # 推荐使用稳定版本标签
    container_name: gitea
    environment:
      - USER_UID=1000 # 需要与 /data 卷所有者的 UID/GID 匹配（主机卷）
      - USER_GID=1000 # 对于命名卷则不需要担心权限问题
    restart: always
    networks:
      - gitea
    volumes:
      - ./gitea:/data # 将主机当前目录下的 gitea 目录映射到容器的 /data
      # 或使用命名卷: - gitea:/data (需在顶层定义 volumes: gitea: driver: local)
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
    ports:
      - "3000:3000" # Web UI 端口映射
      - "222:22"   # SSH 端口映射
```

您可以根据需要修改端口映射，只需更改主机端口即可。

**使用 MySQL 或 PostgreSQL 数据库**

您可以修改 `docker-compose.yml` 以使用外部数据库，例如 MySQL 或 PostgreSQL。这通常涉及添加一个数据库服务，并在 Gitea 服务中配置相应的环境变量 (`GITEA__database__DB_TYPE`, `GITEA__database__HOST`, `GITEA__database__NAME`, `GITEA__database__USER`, `GITEA__database__PASSWD`) 和 `depends_on`。

**启动与安装**

在包含 `docker-compose.yml` 文件的目录中执行 `docker-compose up -d` 即可在后台启动 Gitea。使用 `docker-compose ps` 查看状态，`docker-compose logs` 查看日志。要关闭，执行 `docker-compose down`。

启动后，通过浏览器访问 `http://server-ip:3000` (或您配置的其他端口) 完成安装向导。如果在 `docker-compose` 中启动了数据库服务，数据库主机名应填写 `db`。

**配置**

Gitea 的许多设置可以通过环境变量配置。环境变量的形式通常为 `GITEA__SECTION_NAME__KEY_NAME`。您也可以手动生成 `SECRET_KEY` 和 `INTERNAL_TOKEN` 并作为环境变量设置。配置文件 `app.ini` 安装后会保存在 `/data/gitea/conf/` 目录中。

**SSH 容器直通**

如果需要在容器内运行 SSH，可能需要设置 SSH 容器直通。这涉及在主机上创建一个与容器内 Gitea 用户 UID/GID 相同的用户，挂载主机的 `.ssh` 目录到容器，创建主机 SSH 密钥对，并在主机上设置一个脚本将 SSH 请求转发到容器的 SSH 端口，同时修改主机的 `authorized_keys` 文件。将容器的 SSH 端口（22）映射到主机上的非标准端口（例如 2222）是一种方法，可以将端口映射到主机的 `localhost` (如 `127.0.0.1:2222:22`) 以便不对外暴露。

### 创建 Gitea 操作用户

如果需要将 Gitea 与 Jenkins 等工具对接以拉取代码，通常需要在 Gitea 中创建一个专门的用户。对于 http(s) 协议，需要用户名和密码；对于 ssh 协议，需要将本地 ssh 公钥提交到 Gitea 服务器。

创建用户的步骤（需要管理员权限）:

1.  进入 Gitea 管理后台 -> 帐户管理页面 (`<your gitea server>/admin/users`)。
2.  点击右上角“创建新帐户”按钮。
3.  填写用户名（例如 `devops-bot`），认证源选择本地。
4.  去掉“要求用户更改密码”的勾选，确保客户端可以使用设置的密码登录。
5.  用户创建后，可能需要再次编辑将其设置为管理员或授予相应仓库的访问权限。

为了让用户访问仓库，需要进行授权。一种方式是将用户添加到组织中的团队，并授予团队对仓库的权限（例如对所有仓库拥有管理权限）。

## 3. TortoiseGit：Windows 上的 Git GUI 客户端

**TortoiseGit** 是一个 Git 的图形化界面客户端，特别是在 Windows 系统上。它可以帮助不熟悉命令行或偏好 GUI 操作的用户更方便地使用 Git 功能。

源中并未提供 TortoiseGit 的详细安装步骤，但通常需要从官方网站下载安装程序并进行安装。

### TortoiseGit 基本操作

TortoiseGit 通过文件浏览器右键菜单提供 Git 操作。以下是使用 TortoiseGit 执行一些基本 Git 操作的示例:

*   **建立仓库**：可以通过 `git init` 方式 (在目录右键点击 `Git Create repository here`) 或 `git clone` 方式 (右键点击 `Git Clone`，填写远程仓库 URL 和本地目录) 来建立仓库。
*   **提交代码**：在新文件或修改过的文件上右键，选择添加到暂存区 (add to cache)，然后再次右键选择提交到版本库 (commit)。填写提交信息并勾选文件后点击 commit。提交到本地版本库后，可以右键点击 push 推送到远程仓库。
*   **更新代码**：右键点击 pull 从远程仓库更新代码。
*   **回滚版本**：右键点击 `show log` 查看日志，选中某个版本右键点击 `Reset master to this` 进行版本回滚。
*   **显示日志**：右键点击 `show log` 即可查看提交历史。
*   **创建分支**：右键点击 `Create Branch` 创建新分支。创建后可以切换到新分支。
*   **解决冲突**：当 push 或 pull 发生冲突时，文件会显示感叹号图标。右键点击 `Edit conflicts` 进入冲突编辑界面，手动合并代码后点击 `Mark as resolved`。
*   **忽略文件上传**：创建 `.gitignore` 文件，并在其中定义不希望上传的文件（例如 `target.txt`）。
*   **设置比较工具**：可以设置 Beyond Compare 等外部工具作为比较工具。

## 4. Git、Gitea 和 TortoiseGit 的相互应用

这三款工具结合使用可以构建一个完整的代码版本控制和托管环境：

1.  **Git 作为核心**：Git 是底层的版本控制系统。所有的版本跟踪、提交、分支、合并等操作都是基于 Git 完成的。
2.  **Gitea 作为远程仓库**：Gitea 提供了一个中心化的平台来托管您的 Git 仓库。这使得团队协作、代码共享、代码审查和持续集成/部署成为可能。您可以将本地 Git 仓库推送到 Gitea，也可以从 Gitea 克隆仓库到本地。
3.  **TortoiseGit 作为客户端**：TortoiseGit 提供了一个友好的图形界面，让您在 Windows 上更轻松地执行 Git 操作。您可以使用 TortoiseGit 来：
    *   从 Gitea 克隆一个仓库 (`Git Clone` 命令，填写 Gitea 仓库的 URL)。
    *   在本地工作目录中修改文件。
    *   使用右键菜单将更改的文件添加到暂存区 (`add`)。
    *   使用右键菜单将暂存区的更改提交到本地仓库 (`commit`)。
    *   使用右键菜单将本地仓库的提交推送到 Gitea 服务器 (`push`)。
    *   使用右键菜单从 Gitea 服务器拉取最新代码到本地 (`pull`)。
    *   通过 `show log` 查看本地或远程仓库的提交历史。
    *   创建和切换分支以进行并行开发。
    *   在进行 push 或 pull 时，如果出现冲突，使用 TortoiseGit 的工具解决冲突。

通过这种组合，Git 提供了强大的版本控制功能，Gitea 提供了集中的代码托管和协作平台，而 TortoiseGit 则通过易用的 GUI 简化了日常的 Git 操作。这使得个人或小型团队可以方便地搭建自己的代码管理系统。

## 5. 总结

Git、Gitea 和 TortoiseGit 各自扮演着不同的角色，但它们可以有效地结合起来，为您的项目提供 robust 的版本控制解决方案。Git 作为核心引擎，Gitea 提供远程托管和协作功能，而 TortoiseGit 则作为桌面客户端简化了 Windows 用户与 Git/Gitea 的交互。通过理解它们的工作原理和基本操作，您可以更好地管理您的代码，提高开发效率。
