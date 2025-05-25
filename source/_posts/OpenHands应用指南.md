---
title: OpenHands应用指南
tags:
  - openhands
  - OpenDevin
  - Ai
  - 应用指南
  - 开发平台
  - 编程
  - StackOverflow
categories:
  - 学习笔记
  - 应用指南
index_img: /img/hands.png
banner_img: /img/post_banner.jpg
banner_mask_alpha: 0.5
sticky: false
comment: valine
date: 2025-05-25 09:27:38
---

<!-- 这里是模板内容 -->
欢迎使用 OpenHands（前身为 OpenDevin），这是一个由 AI 提供支持的软件开发代理平台。

OpenHands 代理可以做任何人类开发人员可以做的事情：修改代码、运行命令、浏览 Web、 调用 API，是的，甚至可以从 StackOverflow 复制代码片段。好的，这是一篇根据您提供的资料生成的 OpenHands 应用指南 Markdown 文档。

<!-- more -->
本文档将指导您如何开始使用 OpenHands，包括安装运行、连接语言模型（LLM）并配置 API 密钥，以及一些基本的使用方法和最佳实践。

## 1. 开始使用

### 系统要求

运行 OpenHands 推荐的系统配置包括：
*   支持 Docker Desktop 的 macOS
*   Linux
*   支持 WSL 和 Docker Desktop 的 Windows

建议使用配备现代处理器和至少 **4GB RAM** 的系统。

### 前置条件

确保您的系统已安装 Docker Desktop 并配置正确。

*   **macOS**: 安装 Docker Desktop 并确保在 `Settings > Advanced` 中启用了 `Allow the default Docker socket to be used`。
*   **Linux**: 安装 Docker Desktop。
*   **Windows**: 安装 WSL (版本 2) 和 Docker Desktop。确保 Docker Desktop 的 WSL 2 基于引擎已启用，并且启用了与默认 WSL 发行版的集成。注意，Windows 用户需要在 WSL 终端中运行 Docker 命令来启动应用。

### 启动应用

通过 Docker 运行 OpenHands 是最简单的方式。

首先拉取运行时镜像：
```bash
docker pull docker.all-hands.dev/all-hands-ai/runtime:0.39-nikolaik
```

然后运行 OpenHands Docker 命令：
```bash
docker run -it --rm --pull=always \
-e SANDBOX_RUNTIME_CONTAINER_IMAGE=docker.all-hands.dev/all-hands-ai/runtime:0.39-nikolaik \
-e LOG_ALL_EVENTS=true \
-v /var/run/docker.sock:/var/run/docker.sock \
-v ~/.openhands-state:/.openhands-state \
-p 3000:3000 \
--add-host host.docker.internal:host-gateway \
--name openhands-app \
docker.all-hands.dev/all-hands-ai/openhands:0.39
```
*   `--rm` 会在容器退出时自动移除容器 [未在源中，常见 Docker 用法]。
*   `-v ~/.openhands-state:/.openhands-state` 将本地状态目录挂载到容器内，用于持久化设置。
*   `-p 3000:3000` 将容器的 3000 端口映射到主机的 3000 端口，用于访问 UI。
*   `--add-host host.docker.internal:host-gateway` 允许容器通过 `host.docker.internal` 访问主机网络，这在使用本地服务的 LLM 时很有用。

启动后，您可以通过浏览器访问 `http://localhost:3000` 来使用 OpenHands。

> **警告**: 如果您在公共网络上运行，请参考强化 Docker 安装指南，通过限制网络绑定和其他安全措施来保护您的部署。默认配置是为了本地开发方便而设计的。可以通过设置 `SANDBOX_RUNTIME_BINDING_ADDRESS=127.0.0.1` 和修改 `-p` 端口绑定来限制访问。

您也可以将 OpenHands 连接到本地文件系统，运行脚本化的无头模式（Headless mode），通过交互式命令行界面（CLI Mode）进行交互，或通过 GitHub Action 在带标签的 issue 上运行。

## 2. 连接语言模型（LLM）与配置 API 密钥

OpenHands 使用 LiteLLM 库连接并调用各种 LLM。为了正常工作，它需要一个强大的模型。

### 配置方法

配置 LLM 模型和 API 密钥可以通过以下方式完成：

1.  **通过 UI 设置**:
    *   首次启动时会看到设置弹窗，或者可以点击 UI 中的设置按钮（齿轮图标）进入设置页面。
    *   在 **LLM** 标签页下：
        *   选择 **LLM Provider**（提供商）。
        *   选择 **LLM Model**（模型）。
        *   输入对应的 **API Key**。
    *   如果所需模型不在列表中，可以勾选 **Advanced**（高级）选项。
    *   在高级设置中，使用 **Custom Model** 文本框手动输入模型名称，通常需要提供商前缀，例如 `openai/<model-name>` 或 `azure/<deployment-name>`。
    *   如果提供商要求，还可以指定 **Base URL**（基本 URL）。

2.  **通过环境变量**:
    *   在运行 OpenHands 的 Docker 命令时，可以使用 `-e` 参数设置环境变量来配置 LLM。
    *   常用的环境变量包括：
        *   `LLM_MODEL`：指定要使用的 LLM 模型。
        *   `LLM_API_KEY`：设置 API 密钥。
        *   `LLM_API_VERSION`：主要用于 Azure OpenAI，需要设置 API 版本。
        *   `LLM_BASE_URL`：设置 API 的基本 URL，用于 OpenAI 兼容端点、代理或本地模型。

3.  **通过 `config.toml` 文件**:
    *   在开发模式下运行 OpenHands 时，可以在 `config.toml` 文件中配置 LLM 设置。例如，在 `[llm]` 部分设置 `model` 和 `ollama_base_url`。

### 支持的提供商和配置示例

OpenHands 通过 LiteLLM 支持连接到多种 LLM 提供商：

*   **Azure OpenAI**: 需要在 Docker 命令中设置 `LLM_API_VERSION` 环境变量。在 UI 中选择 `Azure` 提供商，设置 `<deployment-name>` 为 Custom Model（前缀 `azure/`），输入 Azure API Base URL 和 API Key。
*   **Google Gemini/Vertex**: Gemini 可在 UI 中选择 `Gemini` 提供商，选择或输入模型名称（前缀 `gemini/`），输入 API Key。Vertex AI 需要设置 `GOOGLE_APPLICATION_CREDENTIALS`, `VERTEXAI_PROJECT`, `VERTEXAI_LOCATION` 环境变量，并在 UI 中选择 `VertexAI` 提供商，选择或输入模型名称（前缀 `vertex_ai/`）。
*   **Groq**: 可在 UI 中选择 `Groq` 提供商，选择或输入模型名称（前缀 `groq/`），输入 API Key。也可作为 OpenAI 兼容端点使用：在 UI 高级设置中，Custom Model 设置为 `openai/<model-name>`，Base URL 设置为 `https://api.groq.com/openai/v1`，并输入 Groq API Key。
*   **本地 LLMs**: 通常需要先使用 LMStudio、SGLang 或 vLLM 等工具在本地服务模型。然后在 OpenHands UI 高级设置中配置 Custom Model (如 `lm_studio/<model-name>` 或 `openai/<served-model-name>`) 和 Base URL (如 `http://host.docker.internal:1234/v1` 或 `http://host.docker.internal:8000`)，并输入 API Key (如 `dummy` 或服务时设置的 key)。请注意，使用本地 LLM 可能功能受限，强烈推荐使用 GPU 服务模型以获得最佳体验。
*   **LiteLLM Proxy**: 如果您设置了 LiteLLM Proxy 服务器，可以在 UI 高级设置中配置 Custom Model (前缀 `litellm_proxy/`)，Base URL 为您的 LiteLLM proxy URL，并输入 LiteLLM proxy API Key。支持的模型取决于您的代理配置。
*   **OpenAI**: 在 UI 中选择 `OpenAI` 提供商，选择或输入模型名称（前缀 `openai/`），输入 API Key。也可使用 OpenAI 兼容端点或代理：在 UI 高级设置中，Custom Model 设置为 `openai/<model-name>`，Base URL 为代理 URL，输入 API Key。
*   **OpenRouter**: 在 UI 中选择 `OpenRouter` 提供商，选择或输入模型名称（前缀 `openrouter/`），输入 API Key。

### 获取 API 密钥

大多数语言模型都需要 API 密钥才能访问，并且通常会产生费用。建议从提供商的官方网站创建账户并生成 API 密钥。为了控制成本，建议设置使用限制并监控用量。例如，Anthropic 和 OpenAI 都提供了生成 API 密钥的流程。

### 模型推荐

根据 SWE-bench 数据集的评估和社区反馈，以下是推荐且经验证可与 OpenHands 配合良好的模型：
*   `anthropic/claude-sonnet-4-20250514` (**推荐**)
*   `openai/o4-mini`
*   `gemini/gemini-2.5-pro`
*   `deepseek/deepseek-chat`
*   `all-hands/openhands-lm-32b-v0.1` (通过 OpenRouter 可用)

请注意，OpenHands 的能力很大程度上取决于所使用的 LLM 模型。当前大多数本地和开源模型可能不如这些推荐模型强大，可能导致响应慢、质量差或错误。

### API 重试和速率限制

LLM 提供商通常有速率限制，有时很低，可能需要重试。OpenHands 在收到速率限制错误 (429 错误码) 时会自动重试请求。您可以通过设置环境变量或在开发模式下的 `config.toml` 文件中自定义重试次数和等待时间。

## 3. 交互模式

OpenHands 支持多种交互模式：

*   **GUI Mode**: 通过 web 界面 (http://localhost:3000) 与 OpenHands 交互。这是最直观的方式，提供聊天面板、文件变化、内嵌 VS Code、终端、Jupyter 等功能视图。LLM 配置主要通过 UI 设置完成。
*   **CLI Mode**: 提供一个交互式命令行界面，可直接在终端中与 OpenHands 交互。可以使用命令启动对话、查看状态、修改设置等。LLM 设置可以通过 `/settings` 命令或环境变量、`config.toml` 文件进行管理。
*   **Headless Mode**: 允许您使用单个命令运行 OpenHands，无需启动 web 应用。这适用于编写脚本和自动化任务。配置通常通过环境变量或 `config.toml` 文件完成。

## 4. 核心功能概览

OpenHands GUI 提供了多个面板帮助您与 AI 代理协作：

*   **Chat Panel**: 显示用户与 OpenHands 之间的对话，OpenHands 会在此解释其行动。
*   **Changes**: 显示 OpenHands 执行的文件更改。
*   **VS Code**: 内嵌的 VS Code 编辑器，用于浏览、修改、上传和下载文件。
*   **Terminal**: OpenHands 和用户都可以运行终端命令的空间。
*   **Jupyter**: 显示 OpenHands 执行的所有 Python 命令，特别适合数据可视化任务。
*   **App**: 显示 OpenHands 运行应用程序时的 web 服务器，用户可以与正在运行的应用交互。
*   **Browser**: OpenHands 用来浏览网站的非交互式浏览器。

## 5. 提示词最佳实践

提供清晰有效的提示词是获得准确有用响应的关键。

*   **好的提示词的特点**:
    *   **具体 (Concrete)**: 清晰描述要添加什么功能或修复什么错误。
    *   **位置明确 (Location-specific)**: 如果知道，请指定代码库中需要修改的位置。
    *   **范围适当 (Appropriately scoped)**: 专注于单个功能，通常不超过 100 行代码。

*   **有效提示词的技巧**:
    *   尽可能具体地说明期望的结果或要解决的问题。
    *   提供上下文，包括相关文件路径和行号（如果可用）。
    *   将大型任务分解为更小、更易管理的提示词。
    *   包含相关的错误消息或日志。
    *   如果不明朗，请指定编程语言或框架。

通过保持提示词的精确性和信息量，OpenHands 可以更好地协助您。

## 6. 仓库定制与微代理（Microagents）

您可以通过在仓库根目录创建 `.openhands` 目录来自定义 OpenHands 与您的仓库的交互方式。

### 微代理（Microagents）

微代理是增强 OpenHands 领域特定知识的专用提示词，提供专家指导、自动化常见任务并确保项目实践的一致性。

*   **微代理类型**:
    *   **通用微代理 (General Microagents)**: 为 OpenHands 提供关于仓库的一般性指导。这些微代理总是作为上下文的一部分加载。无需 frontmatter。放置在 `.openhands/microagents/repo.md`。
    *   **关键字触发微代理 (Keyword-Triggered Microagents)**: 当提示词中包含特定关键字时激活的指导。只有当提示词包含触发词时才会加载。需要 frontmatter 来定义触发词。放置在 `.openhands/microagents/<name>.md`。
    *   **组织和用户微代理 (Organization and User Microagents)**: 适用于组织或用户拥有的所有仓库的微代理。可以在组织或用户的 `.openhands` 仓库中创建 `microagents` 目录来放置这些微代理。

*   要定制 OpenHands 的行为，在您的仓库根目录创建 `.openhands/microagents/` 目录，并在其中添加 `<microagent_name>.md` 文件。加载的微代理会占用上下文窗口空间，与用户消息一起为 OpenHands 提供任务和环境信息。

### 设置脚本与 Pre-commit 脚本

*   **设置脚本 (`.openhands/setup.sh`)**: 添加此文件，每次 OpenHands 开始处理您的仓库时都会运行。这是安装依赖、设置环境变量等设置任务的理想位置。
*   **Pre-commit 脚本 (`.openhands/pre-commit.sh`)**: 添加此文件以创建自定义 git pre-commit hook，在每次提交前运行。可用于强制执行代码质量标准、运行测试等。

## 7. OpenHands Cloud

OpenHands Cloud 是 All Hands AI 提供的托管云版本。您可以通过 app.all-hands.dev 访问，需要使用 GitHub 或 GitLab 账户登录。

Cloud 版本提供以下功能：
*   **Cloud UI**: Web 界面，用于交互。
*   **Cloud API**: REST API，允许程序化交互。可以通过 Cloud UI 的设置页面生成 API 密钥。
*   **Cloud Issue Resolver**: 自动化代码修复和提供智能协助，尤其是在 GitHub 的 issues 和 pull requests 上。需要通过 GitHub/GitLab 集成授予仓库访问权限。

Cloud 版本是开始使用 OpenHands 的最简单方式，新用户提供免费额度。

## 8. 故障排除与社区

*   如果您遇到问题，可以查阅故障排除指南。
*   常见的故障包括 API 密钥无法识别、组织访问被拒绝等，通常需要检查密钥是否正确、是否过期、权限是否正确，以及 SSO 是否启用等。
*   使用 CLI 模式时，如果遇到权限问题，请确保您的工作区目录受信任且环境变量设置正确。使用高级设置可以进行更深入的 LLM 配置。

OpenHands 是一个社区驱动的项目。您可以通过以下渠道与社区互动：
*   加入 Slack 工作区（讨论研究、架构和未来开发）。
*   加入 Discord 服务器（社区运营，用于一般讨论、问题和反馈）。
*   阅读或提交 GitHub Issues。

要了解更多信息和高级配置选项，请查阅官方文档。

```
