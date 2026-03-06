---
title: comfyui学习笔记
date: 2025-07-22 15:00:00
tags:
  - comfyui
  - SDWEBUI
  - ai画图
categories: 学习笔记
index_img: /img/comfyui.jpg
banner_img: /img/comfyui_workflow.jpg
banner_mask_alpha: 0.6
comment: valine   
--- 

记录 ComfyUI 的来龙去脉 + 安装方式 + 一个「线稿生成建筑效果图」工作流拆解，方便以后快速复盘。

---

<!--more-->

#  ComfyUI 学习笔记

## ️ 一、ComfyUI 的产生背景与发展历程

### 1.1 背景简介

随着 AI 文生图技术（Stable Diffusion / SD）在 2022 年爆发，越来越多开发者希望更直观地掌握生成图像的过程，提升实验效率与可控性。在此背景下，ComfyUI 应运而生。

ComfyUI 是一款基于**节点式流程（Node Graph）**的图形化 AI 绘图工作流软件，主要针对 Stable Diffusion 模型，允许用户以很高自由度可视化搭建图像生成的各个环节。

它通过模块化拆解 SD 流程，让用户自由调整：

- Checkpoint 模型加载
- VAE 编解码
- 正/反向提示词编码
- LoRA / ControlNet / IPAdapter
- Sampler 等采样器

优势在于：**直观清晰、细节可控、模块复用性强**。

### 1.2 发展历程

- 2022 年底：第一版 ComfyUI 开源，起初仅供研究学习使用  
- 2023 年：快速更新，支持各类新模型（SDXL、LoRA、ControlNet 等），逐渐成为专业领域主流  
- 2024 年起：形成完整社区生态，成为继 Automatic1111 之外，AI 绘图的工业级必备工具  

---

##  二、ComfyUI 下载、安装与基本用法

### 2.1 下载与安装

**Windows 用户推荐：**

- GitHub 项目主页：`https://github.com/comfyanonymous/ComfyUI`
- 下载 `ComfyUI_windows_portable.zip`，解压即可使用

**Linux / Mac 用户：**

```bash
git clone https://github.com/comfyanonymous/ComfyUI
cd ComfyUI
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

### 2.2 启动方式

- Windows：双击 `run_nvidia_gpu.bat`
- 浏览器访问：`http://127.0.0.1:8188`

### 2.3 基本使用方法

- 拖入节点，连接数据流
- 配置参数，点击 **Queue Prompt** 运行
- 通过节点流程可视化查看结果

### 2.4 硬件要求

| 硬件 | 最低要求 | 推荐配置 |
|---|---|---|
| 显卡（GPU） | NVIDIA RTX 3060 12G | RTX 4070 / 4080 / A6000 |
| 显存（VRAM） | 8GB 以上 | 12GB - 24GB |
| CPU | Intel i5 / Ryzen 5 | i7 / Ryzen 7 |
| 内存（RAM） | 16GB | 32GB+ |
| 存储 | 50GB 可用空间（大模型更多） | SSD 固态硬盘 |
| 操作系统 | Windows 10 / Linux | 最新稳定版 |

> 注意：强依赖 GPU，无 CUDA 基本无法顺畅运行。

---

## ️ 三、本工作流（线稿生成建筑效果图）详解

### 3.1 核心思路

**线稿 + ControlNet + IPAdapter + LoRA**：突出结构、风格一致性与细节美观。

### 3.2 节点分解

| 节点 | 作用 |
|---|---|
| Checkpoint | 建筑专用模型 |
| LoraLoader | 景深 / 建筑风格 LoRA |
| VAE 编解码 | 潜变量互转图像 |
| LoadImage | 线稿 / 参考图输入 |
| Preprocessor | 线稿清理 |
| ControlNet | 结构注入 |
| IPAdapter | 风格融合 |
| CLIPText 编码 | 正反提示词 |
| Sampler | 采样器 |
| VAEDecode | 解码生成 |
| SaveImage | 输出保存 |

### 3.3 关键参数设置

| 模块 | 参数 | 目的 |
|---|---:|---|
| ControlNet | 0.9 | 保持线稿结构 |
| LoRA | 0.7 | 风格增强 |
| IPAdapter | 0.9 | 风格一致 |
| Sampler | 30 步 / CFG 7 | 提升细节、稳定输出 |

### 3.4 Prompt 范例

**正向：**

```text
Wallpaper, high quality, extreme details, realistic style, a building, outdoors…
```

**反向：**

```text
embedding:EasyNegativeV2, bad anatomy, disfigured, blurry, watermark…
```

---

## ️ 工作流结构示意图

> 网页原图（建议你也保存一份到本地 / 图床，避免以后丢失）：  
> `https://www.vgtmy.com/img/comfyui_workflow.jpg`

![建筑效果图工作流](https://www.vgtmy.com/img/comfyui_workflow.jpg)

---

##  四、ControlNet 理论与优化技巧

| 类型 | 模型 | 用途 |
|---|---|---|
| 线稿 | Lineart | 建筑结构 |
| 深度 | Depth | 光影体积 |
| 姿态 | Openpose | 人体结构 |

| 参数 | 范围 | 含义 |
|---|---|---|
| Strength | 0.6 - 1 | 越高越准 |
| Guidance | 0 - 1 | 持续时间 |

**建筑推荐：** Strength 0.9（全程）

---

##  五、采样器与 CFG

| 采样器 | 特点 |
|---|---|
| Euler A | 快、略粗糙 |
| DPM++2M | 稳定均衡 |
| DPM++SDE | 极致细节 |

| CFG | 效果 |
|---:|---|
| 5 - 6 | 灵活创意 |
| 7 - 8 | 稳妥结构 |
| 9+ | 强压提示词 |

**建筑推荐：** CFG 7-8，`DPM++2M Karras`

---

## ✅ 六、总结

ComfyUI 很灵活，尤其适合建筑等**结构稳定需求**。通过 **ControlNet / IPAdapter / LoRA** 组合，可以稳定输出高质量效果图。

建议继续探索：

- 双 ControlNet
- T2I Adapter
- 自动脚本批量化
