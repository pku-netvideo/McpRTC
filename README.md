# 👁️ McpRTC

([English](README_en.md) | [中文](README.md))

![Platform](https://img.shields.io/badge/Platform-ESP32-blue) ![Protocol](https://img.shields.io/badge/Protocol-MCP-purple) ![Stream](https://img.shields.io/badge/Stream-WebRTC-red) ![License](https://img.shields.io/badge/License-Apache-green)

**McpRTC：基于 MCP 协议的 Agent 原生 RTC 框架**

> ****从“被动传输”到“主动感知”，由Agent意图驱动的自主、按需、低延迟视频传输。****

## 📖 简介 (Introduction)

**McpRTC** 是一个运行在 [MCP (Model Context Protocol) ](https://modelcontextprotocol.io/) 协议之上的实时通信（RTC）框架。

正如 WebRTC 解决了 Web 浏览器之间的实时通信问题，McpRTC 旨在解决 AI Agent 与物理世界之间的实时连接问题。它不仅仅是一个传输协议，更是一种AI 原生的感知范式。

在 McpRTC 中，视频流的开启与关闭不再依赖人类的手动操作，而是由 Agent 根据当前交互的上下文自主决策。同时，我们将帧率、码率等参数的控制权，从 ABR 等传输算法（规则驱动）交还给了 Agent 本身（意图驱动），让 Agent 能够根据任务需求实现真正的语义级流控。

## 💡 核心理念 (Core Insight)
传统的 RTC 是 Human-Centric（以人类为中心）的，而 McpRTC 是 Agent-Centric（以Agent为中心）的。我们正在经历从规则驱动到意图驱动的范式转移：

| 特性 | 传统 RTC / WebRTC            | 🤖 McpRTC                 |
| :--- |:---------------------------|:--------------------------|
| **触发机制** | **人类发起**（人工点击呼叫/挂断）        | **AI 自主决策**（基于意图主动介入）     |
| **连接形态** | **持续连接**（通话期间全程视频传输）       | **按需感知**（仅在需要视觉时开启）       |
| **QoS 控制** | **被动适应网络**（基于网络状况的算法GCC、BBR等） | **主动适应内容**（基于语义理解的控制）     |
| **典型场景** | 视频会议、直播、1v1 通话             | Always-on 虚拟助手、具身机器人、智能眼镜 |


## 🚀 为什么我们需要 McpRTC？一个例子 (Case Study)：
在 Always-on AI Assistant 的场景下，全天24h持续传输高质量视频流 (1080p 30fps) 是极其低效且昂贵的（带宽、算力、功耗），对于一些可穿戴AIoT设备（如智能眼镜）来说更无法接受（如短续航）。

McpRTC 允许 Agent 像人类一样**决定何时“闭目养神”、“随便撇一眼”或“认真注视”**：
1.  **纯音频交互**：当用户只是闲聊或问题无需视觉（如询问天气）时，Agent **保持视频关闭**，节省计算、电池、带宽资源。
2.  **主动视觉介入**：当 Agent 听到异常声音（如玻璃破碎）或无法通过音频理解用户意图时，**主动发起**视频流请求，“看一眼”现场情况。
3.  **语义级传输控制**：当 Agent 发现内容看不清、跟不上，**对任务执行有影响时**，自动提升视频码率、分辨率、帧率。否则进行降级。

综上，McpRTC实现了响应延迟、准确率、带宽开销、设备能耗之间的平衡。经初步测试，**McpRTC可以在降低63.95%能耗的情况下，维持“满血视频传输”准确率的98.26%**。

## 📦 使用方法 (Usage)
本项目基于开源硬件项目 **Xiaozhi (小智)** 进行深度改造，将 ESP32 提供的 WebRTC 库移植到小智平台，并首创性地将 WebRTC 的参数控制函数通过 MCP 协议提供给大模型，从而实现视觉感知的自主控制。

**1、固件环境安装**：ESP-IDF v5.5.0 或更高版本，具体可参考 [xiaozhi编译环境安装教程](https://icnynnzcwou8.feishu.cn/wiki/JEYDwTTALi5s2zkGlFGcDiRknXf)

**2、编译与烧录**:
首先，克隆本项目到本地：
```bash
git clone https://github.com/pku-netvideo/McpRTC.git
```
然后，**打开ESP-IDF Powershell**，进入项目目录并编译固件
```bash
cd McpRTC/xiaozhi-esp32-board
idf.py set-target esp32s3
idf.py build
```
最后，将编译好的固件烧录到 ESP32 设备上：
```bash
idf.py build flash monitor
```
**3、服务端配置**：请参考 [xiaozhi-esp32-server](https://github.com/xinnan-tech/xiaozhi-esp32-server/blob/main/docs/Deployment_all.md#%E6%96%B9%E5%BC%8F%E4%BA%8C%E6%9C%AC%E5%9C%B0%E6%BA%90%E7%A0%81%E8%BF%90%E8%A1%8C%E5%85%A8%E6%A8%A1%E5%9D%97) 项目进行服务端配置。对应的文件在 ``McpRTC/xiaozhi-esp32-server`` 目录下。

由于原项目架构为ASR+LLM+TTS，为了将LLM替换为可以接收视频的VLM，我们对项目进行了部分修改，因此目前暂时仅支持原项目的 ``方式二：本地源码运行全模块`` 部署。

部署后，需要在智控台将LLM改为gemini，并将意图识别改为函数调用意图识别，如果需要代理，请把代理开启在7890端口。

**4、运行项目**：启动服务端，并确保 ESP32 设备与服务器之间的网络连接正常，即可开始与模型对话。

> [!NOTE]
> 本项目作为实验性项目，目前支持立创开发版实战派S3作为硬件平台，后续会适配更多平台。当前版本LLM可以调用 `start_rtc_stream` `stop_rtc_stream` 等工具主动控制视频流的开关。此外，**McpRTC** 还在框架层面支持LLM动态调整视频流的**码率、帧率**等参数。当前由于S3的硬件性能不足，我们实现了工具接口，但并未被LLM调用。

## ️✨ 实机演示（Demo）
https://github.com/user-attachments/assets/279b6baf-844d-4e49-9111-cbd73ebee5b4

上方演示了McpRTC受Agent控制，自主按需开启视频的例子：

- 当用户对话无需视频时，视频流关闭（**区别于“用户说话必会触发视频传输”的主流VAD方案**）；
- 当对话依赖视觉信息，Agent自主开启视频。

通过实时的**功率波动**，直观展示了**视频流**的开关情况。不同工作模式下的功耗变化如下：

- **纯音频对话**：功率波动在 **1.1W** 左右；
- **视频流对话**：开启实时视频流后，功率上升至 **1.4W** 左右。

> [!NOTE]
> 感觉功率增加不大？这是由于S3开发板算力较弱，仅支持320x240@2fps视频传输，采集和编码开销都不大，因此相较于audio-only功率提升不显著。对于算力更强的终端设备，高帧率&高分辨率下McpRTC的收益会更显著；

> [!NOTE]
> 感觉响应延迟大？本样例的VLM模型并非本地部署，而是通过API调用。端到端延迟主要来自于API调用，不同VLM的响应速度见：[4.2 大语言模型(LLM)响应速度对比](https://github.com/xinnan-tech/xiaozhi-performance-research)；

 
## 🏗 致谢 (Acknowledgement)

本项目站在了巨人的肩膀上。核心代码基于以下两个仓库进行了二次开发：

* **Firmware**: [xiaozhi-esp32](https://github.com/78/xiaozhi-esp32) - 提供了基于ESP32的AI智能助手原型
* **Server**: 基于 [xiaozhi-esp32-server](https://github.com/xinnan-tech/xiaozhi-esp32-server) - 提供了适配xiaozhi-esp32的服务端实现




