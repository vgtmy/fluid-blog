---
title: n8n：从入门到进阶（本地自托管实战笔记）
date: 2025-07-04 15:00:00
tags:
  - n8n
  - 自动化
  - 工作流
  - API
  - 入门
  - 进阶
categories: 学习笔记
index_img: /img/n8n.jpg
banner_img: /img/default.png
banner_mask_alpha: 0.6
comment: valine   
---

一篇把 n8n 的版本选择、本地安装、核心概念、工作流搭建与进阶能力（AI / Webhook / 错误处理 / 开发者工具）串起来的速查笔记。

---

<!--more-->

---

# 🤖 n8n：从入门到进阶

> 本文根据你发布在 vgtmy.com 的网页内容整理还原（发布/更新：2025-07-04），用于找回丢失的 Markdown 源文件并方便后续维护。  
> 原文页内对 n8n 的定位、安装方式、界面与节点概念、进阶功能对比等都有较完整介绍。 citeturn0view0turn1view4

---

## 🧭 一、n8n 版本选择

在开始使用 n8n 之前，需要先选好运行方式：

- **n8n 云版本（官方托管）**
  - 优点：24/7 运行、无需维护服务器、开箱即用  
  - 缺点：需要付费  
- **本地自托管版本（社区版）**
  - 优点：免费（社区版）、可完全掌控数据与存储、更注重隐私/合规  
  - 缺点：需要一定的技术基础与维护能力  

> 本笔记重点围绕“本地自托管”展开，适合学习/实验与长期可控部署。 citeturn0view0

---

## ⚡ 二、n8n 本地快速安装（npm 方式）

### 🔹 1）前提条件：安装 Node.js

- 要求：**Node.js 18+**  
- 下载：去 Node.js 官网选择 **LTS** 版本更稳（Windows/macOS/Linux 都支持） citeturn0view0

安装完成后验证：

```bash
node -v
npm -v
```

能看到版本号即表示正常。

### 🔹 2）全局安装 n8n

```bash
npm install n8n -g
```

如果在 macOS/Linux 遇到权限问题（EACCES），可用 `sudo`：

```bash
sudo npm install n8n -g
```

> 更理想的方式是把 npm 的全局目录配置到用户目录，避免长期使用 sudo。 citeturn0view0

### 🔹 3）启动 n8n

```bash
n8n
# 或
n8n start
```

默认端口通常为 **5678**，浏览器访问：

- `http://localhost:5678`

首次访问需要设置管理员账户（Owner Account），请务必保存密码。 citeturn0view0

---

## 🖥️ 三、用户界面概览

n8n 主界面通常包括：

- **左侧边栏**：模板、执行记录、帮助等（部分功能企业版/云版才有）  
- **主窗口**：工作流列表，可“从头开始”创建  
- **设置入口**：底部菜单；可设置亮/暗色，工作流可设置时区（确保计划触发时间正确） citeturn0view0

---

## 🧩 四、入门：构建第一个工作流

### 🔹 1）核心概念

- **工作流（Workflow）**：通常从左到右执行  
- **节点（Node）**：接收输入 → 处理 → 输出  
- **数据流（Data Flow）**：节点间以 **JSON** 传递  
- **数据迭代（Data Iteration）**：当数据是列表/数组时，n8n 会自动迭代处理 citeturn0view0

### 🔹 2）节点面板结构

双击任意节点会看到：

- Input（输入）
- Node Details / Parameters（参数配置）
- Output（输出，调试非常关键）
- Docs（节点文档） citeturn0view0

### ✅ 3）Test Step（单步测试）与 Pin（固定数据）

- **Test Step**：只跑当前节点，快速验证输出  
- **Pin（固定）**：把某次输出固定住，后续测试不必重复请求数据源 citeturn0view0

### 🔹 4）表达式（Expression）访问数据

在字段里用 `{{ }}` 写 JavaScript 表达式，例如：

```text
{{ $json.customerID }}
{{ $json.name }}
```

> 花括号里是 JavaScript 表达式（不是 Python）。Code 节点支持 JS/Python。 citeturn0view0

### 🔹 5）示例：个性化消息工作流（流程）

一个典型的入门流程：

1. 第一个节点获取客户列表数据  
2. Test Step 查看输出  
3. 第二个节点（如 Edit Fields）提取需要的字段（ID/姓名/描述等）  
4. Test Step 验证字段提取结果  
5. 第三个节点发送个性化消息，文本里用表达式插入变量  
6. Test Step 验证每个客户都能生成对应消息  
7. 保存工作流、重命名、打标签管理 citeturn1view0turn1view1

---

## 🚀 五、进阶：探索更多功能

### 🔹 1）节点类型详解

n8n 的节点大致可分为：

- **触发器节点（Trigger）**：定义工作流如何开始  
  - Manual Trigger（手动）
  - Schedule Trigger（定时/Cron）
  - On Webhook（接收 HTTP 请求触发）
  - On App Event / On Form Submission 等
  - Execute Workflow（子工作流）
  - Chat Message（用于对话触发，常见于 AI Agent/RAG） citeturn1view1
- **应用节点（App/Action）**：对接第三方应用（Gmail/Telegram/MySQL/Supabase 等） citeturn1view1
- **数据转换节点（Transform）**：Code / Edit Fields / Merge / HTML / Limit 等 citeturn1view2turn2view0
- **流程控制节点（Flow）**：Filter / IF / Loop / Wait / Execute Workflow 等 citeturn1view2

### 🔹 2）AI 自动化（AI Agent / RAG）

n8n 支持把 AI 能力融入工作流：

- Chat Model：连接 OpenAI / Gemini 等大模型  
- Memory：给 AI Agent “长期记忆”能力  
- Tools：让 Agent 调用外部工具（向量库 Pinecone / Supabase 等），构建 RAG  
- 也支持 LangChain 节点，便于组合 AI 功能 citeturn1view2turn2view0

### 🔹 3）集成与连接（HTTP / Webhook / DB / 自定义节点）

- **HTTP Request**：主动调用外部 API（如天气/股票数据）  
- **Webhook**：被动接收外部推送触发工作流；网页也提到 Webhook 在 n8n 的支持覆盖面更广 citeturn2view0
- **数据库连接**：可连接 MySQL、Pinecone、Supabase 等（重点是配置连接信息） citeturn2view0
- **自定义节点（Custom Nodes）**：不够用就自己写节点（更灵活，可定制） citeturn2view0
- **MCP 服务端**：支持 MCP Server 的配置与使用 citeturn2view0

### ⚙️ 4）复杂逻辑与错误处理

- 分支：IF / Switch  
- 合并：Merge  
- 循环：Loop  
- 错误处理：可设置节点遇错行为（停止/忽略继续等），并可设置失败重试（如 `retryOnFail`）。也可使用错误处理工作流在主流程失败时触发。 citeturn2view0turn1view3

### 🧰 5）开发者工具

网页提到的一些“更像工程化”的能力：

- Code 节点（JS/Python）
- 清晰的输入/输出视图方便调试
- Data Pinning（固定数据）
- Environments（区分 dev/test/prod）
- 版本控制（Git 备份）
- Sticky Notes（画布注释） citeturn1view3turn2view1

---

## 🆚 六、n8n vs Zapier / Make.com（定位对比）

网页给出的整体结论：

- **Zapier**：集成最多、上手最容易，但更贵、灵活性最低，适合简单线性自动化  
- **n8n**：灵活性最高，可自托管、可无限定制，但集成数量相对少，上手更偏技术向  
- **Make.com**：介于两者之间，既能做复杂工作流，价格也更折中 citeturn1view3turn1view4

---

## 🌍 七、社区与资源

- 社区论坛：提问响应快  
- 官方文档：节点与功能说明全面  
- 工作流模板：官方与社区预构建模板可快速上手  
- 社区分享：大量教程、案例、视频/文章可参考 citeturn1view4

---

## ✅ 八、总结

n8n 是一个强大且灵活的自动化工具，尤其适合：

- 需要高度定制/控制的工作流场景  
- 技术团队构建复杂自动化与智能化应用  
- 结合表达式、AI 能力、节点生态与流程控制，把重复工作真正交给自动化 citeturn1view4

---

## 🧩 附：建议你文章里加一张“学习路径图”（可 AI 生成）

> 提示词：  
> “A minimal flowchart for learning n8n: install -> UI -> first workflow -> expressions -> webhooks -> error handling -> AI agent & RAG -> production, clean flat style, 16:9, no text”
