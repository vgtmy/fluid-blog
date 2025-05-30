---
title: LibreELEC与Kodi指南
tags:
  - LibreELCE
  - Kodi
  - 指南
  - 教程
  - 家庭影院
categories:
  - 学习心得
index_img: /img/kodi.jpg
banner_img: /img/LibreELEC.jpg
banner_mask_alpha: 0.7
sticky: 
comment: valine
date: 2025-05-30 15:09:13
---

<!-- 这里是模板内容 -->
---


LibreELEC 是一个开源的 Linux 发行版，内置 Kodi 媒体中心。它由已停止开发的 OpenELEC 项目发展而来，目标是提供一个轻量级、快速启动、资源占用极低的 Kodi 专用系统。LibreELEC 可直接开机进入 Kodi，支持多种芯片平台，包括 PC、树莓派、Amlogic、Rockchip 等，是运行 Kodi 的理想平台，尤其适用于性能较低的设备。

<!--more-->
---

# 📥 LibreELEC 下载、安装与应用详细教程

## 🧩 一、LibreELEC 下载

要下载 LibreELEC，首先需根据设备平台选择正确版本。

### 1. 选择设备对应版本

- **PC 平台**  
  - 推荐：`Generic (AMD/Intel)`  
  - 较老设备：`Generic Legacy (AMD/Intel/NVIDIA)`

- **Amlogic 盒子**  
  - 示例：S905 (GXBB)、S905X/S905D (GXL)、S912 (GXM)

- **树莓派**  
  - 专用版本，按型号区分：树莓派 0/1、2/3、4

### 2. 下载工具

- **官方 LibreELEC USB/SD 制作工具**（适用于 PC 安装）
- **Etcher** 烧写工具（支持 Windows/macOS/Linux，适用于树莓派）

### 3. 系统镜像版本参考

| LibreELEC 版本 | Kodi 版本 | Linux 内核 |
|----------------|-----------|------------|
| 12.0.2         | 21.2      | 6.6.x      |
| 12.0 / 12.0.1  | 21.0/21.1 | 6.6.x      |
| 11.0           | 20.3      |            |
| 10.0           | 19.5      |            |
| 9.2            | 18.9      |            |

---

## 💾 二、LibreELEC 安装

### 1. 安装准备

- 一个 **U 盘**（用于 PC/电视盒子）或 **Micro SD 卡**（用于树莓派）
- 安装目标设备（如 PC、盒子、树莓派）
- 一台下载镜像并烧录的电脑（Windows/macOS/Linux）
- 配件（树莓派）：HDMI、电源、网线/无线模块

### 2. 安装步骤

#### **方式一：使用 LibreELEC 制作工具（适用于 PC/电视盒子）**

1. 打开 LibreELEC 制作工具。
2. 选择镜像文件，或根据提示下载适配版本。
3. 插入 U 盘，选择目标设备（确认无误）。
4. 点击“写入”，完成后拔出 U 盘。
5. 将 U 盘插入目标设备，进入 BIOS 设置 USB 启动。
6. 启动后按提示进行安装，安装完成后拔掉 U 盘。
7. 重启后进入 LibreELEC。

#### **方式二：使用 Etcher 烧写（适用于树莓派）**

1. 插入 Micro SD 卡，启动 Etcher。
2. 选择镜像文件，选择 SD 卡为目标设备。
3. 点击 `Flash!` 开始写入，完成后弹出卡。
4. 插入树莓派，连接电源/HDMI 等启动设备。
5. 初次启动进入设置向导。

---

## 🛠️ 三、LibreELEC 首次设置

LibreELEC 首次启动会进入图形化配置向导：

1. **语言选择**：选择简体中文等首选语言。
2. **主机名**：修改默认名称（如 `LibreELEC`）。
3. **网络连接**：
   - 有线：自动连接
   - 无线：手动输入密码连接
4. **服务配置**：
   - **SSH**：高级用途，非必须时建议关闭。
   - **Samba (SMB)**：用于局域网共享文件，建议开启。
5. 设置完成后可进入 Kodi 主界面。

---

## 🎬 四、LibreELEC (Kodi) 应用与设置

LibreELEC 的核心为 Kodi，配置 Kodi 才是系统的重点。

### 常用设置技巧

- **电影集显示**：`设置 → 媒体 → 视频` 开启“显示电影集”
- **视频卡顿优化**：Kodi 21 支持缓存设置，可调节缓冲区大小
- **阿里云盘挂载**：使用 WebDAV 或第三方插件作为视频源
- **首选语言/字幕/音轨**：
  - 设置默认语言为中文
  - 默认开启中文字幕
  - 默认音轨为国语
- **启动画面**：可更换或关闭启动图（Kodi 开机画面）
- **电影海报墙显示名称**：修改 Kodi 界面设置
- **插件推荐**：
  - `PVR IPTV Simple Client`：电视直播
  - `Zimuku`：字幕库插件
  - `Emby / Jellyfin / Plex`：媒体服务器连接
  - `The Movie Database`：视频信息刮削器

---

## 🧯 五、LibreELEC 故障排除

### 1. 无法从 U 盘启动（电视盒子）

- 某些安卓盒子需刷入特殊固件或使用特定方式启动
- 建议查阅对应芯片型号的教程

### 2. Samba 网络共享不可用

- 启用 LibreELEC 的 `Samba` 服务
- 检查 Windows 防火墙/局域网设置

### 3. Kodi 刮削器无法连接 themoviedb

- 可能与 `/storage/.kodi/addons` 目录下某些插件有关

### 4. HDMI 音频无法输出（树莓派）

- 解决方法：
  - 在 `config.txt` 中添加 `hdmi_drive=2`
  - 确保电视在树莓派启动前已开机并设定 HDMI 输入
  - 更换 HDMI 线缆，尝试不同的端口（HDMI0/HDMI1）
  - 树莓派 5：断电静置数分钟后重启，再检查音频输出

---

## 写在最后

希望本教程能帮助您顺利下载、安装并使用 LibreELEC。如果您在使用过程中遇到问题，欢迎查阅社区教程或继续探索更多 Kodi 插件扩展功能。
