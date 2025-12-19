# 👁️ McpRTC

([English](README_en.md) | [中文](README.md))

![Platform](https://img.shields.io/badge/Platform-ESP32-blue) ![Protocol](https://img.shields.io/badge/Protocol-MCP-purple) ![Stream](https://img.shields.io/badge/Stream-WebRTC-red) ![License](https://img.shields.io/badge/License-Apache-green)

**McpRTC: An Agent-Native RTC Framework based on the MCP Protocol**

> **From "Passive Transmission" to "Active Perception": Autonomous, on-demand, low-latency video transmission driven by Agent intent.**

## 📖 Introduction

**McpRTC** is a Real-Time Communication (RTC) framework running on top of the [MCP (Model Context Protocol)](https://modelcontextprotocol.io/).

Just as WebRTC standardized real-time communication for web browsers, McpRTC aims to solve the real-time connection problem between **AI Agents** and the physical world. It is not just a transmission protocol, but an AI-native perception paradigm.

In McpRTC, the initiation and termination of video streams no longer rely on manual human operation. Instead, they are **autonomously decided by the Agent** based on the current interaction context. Furthermore, we have returned the control of parameters such as frame rate and bitrate from traditional transport algorithms (Rule-driven) to the Agent itself (Intent-driven), enabling true **semantic-level flow control**.

## 💡 Core Insight

Traditional RTC is **Human-Centric**, while McpRTC is **Agent-Centric**. We are witnessing a paradigm shift from rule-driven to intent-driven communication:

| Feature | Traditional RTC / WebRTC | 🤖 McpRTC (Next Gen) |
| :--- | :--- | :--- |
| **Trigger Mechanism** | **Human Initiated** (Manual Call/Hangup) | **AI Autonomous Decision** (Intent-based intervention) |
| **Connection State** | **Continuous Connection** (Always streaming during calls) | **On-Demand Perception** (Enabled only when vision is needed) |
| **QoS Control** | **Passive Network Adaptation** (Algorithm-based: GCC, BBR) | **Active Content Adaptation** (Semantic-based control) |
| **Typical Scenarios** | Video Conferencing, Live Streaming, 1v1 Calls | Always-on Assistants, Embodied Robots, Smart Glasses |

## 🚀 Why McpRTC? A Case Study

In the context of an **Always-on AI Assistant**, continuously transmitting high-quality video (e.g., 1080p 30fps) 24/7 is extremely inefficient and expensive (in terms of bandwidth, compute, and power). This is especially unacceptable for wearable AIoT devices (like smart glasses) due to battery constraints.

McpRTC allows Agents to **decide when to "Rest", "Glance", or "Gaze"** like humans:

1.  **Pure Audio Interaction**: When the user is just chatting or asking questions that don't require vision (e.g., asking about the weather), the Agent **keeps the video off** to save compute, battery, and bandwidth resources.
2.  **Active Visual Intervention**: When the Agent hears an anomaly (e.g., glass breaking) or cannot understand the user's intent through audio alone, it **autonomously initiates** a video stream request to "take a look" at the situation.
3.  **Semantic Transmission Control**: When the Agent finds the content unclear or fast-moving, affecting **task execution**, it automatically increases the bitrate, resolution, and frame rate. Otherwise, it downgrades them.

In summary, McpRTC achieves a balance between response latency, accuracy, bandwidth overhead, and device power consumption. Preliminary tests show that **McpRTC achieves 63.95% power reduction compared to full video streaming while maintaining 98.26% of the original accuracy.**

## 📦 Usage

This project is a deep modification based on the open-source hardware project **Xiaozhi (小智)**. We ported the WebRTC library for ESP32 to the Xiaozhi platform and pioneered the exposure of WebRTC parameter control functions to LLMs via the MCP protocol, enabling autonomous control of visual perception.

**1. Firmware Environment Setup**:
Requires ESP-IDF v5.5.0 or higher. Please refer to the [Xiaozhi Compilation Environment Tutorial](https://icnynnzcwou8.feishu.cn/wiki/JEYDwTTALi5s2zkGlFGcDiRknXf).

**2. Compile and Flash**:
First, clone this repository locally:
```bash
git clone https://github.com/pku-netvideo/McpRTC.git
```
Then, open the **ESP-IDF Powershell**, navigate to the project directory, and build the firmware:
```bash
cd McpRTC/xiaozhi-esp32-board
idf.py set-target esp32s3
idf.py build
```
Finally, flash the compiled firmware to your ESP32 device:
```bash
idf.py build flash monitor
```
**3. Server Configuration**: Please refer to the [xiaozhi-esp32-server](https://github.com/xinnan-tech/xiaozhi-esp32-server/blob/main/docs/Deployment_all.md#%E6%96%B9%E5%BC%8F%E4%BA%8C%E6%9C%AC%E5%9C%B0%E6%BA%90%E7%A0%81%E8%BF%90%E8%A1%8C%E5%85%A8%E6%A8%A1%E5%9D%97) project for server setup. The corresponding files are located in the ``McpRTC/xiaozhi-esp32-server`` directory.

Since the original architecture was ASR+LLM+TTS, to replace the LLM with a VLM (Vision Language Model) capable of receiving video, we have modified parts of the project. Therefore, currently, we only support ``the Method 2: Local Source Code Execution (Full Module)`` deployment.

After deployment, you need to change the LLM to Gemini in the control panel and set intent recognition to Function Call Intent Recognition. If a proxy is needed, please open it on port 7890.

**4. Run the Project**: Start the server and ensure the network connection between the ESP32 device and the server is normal. You can then start conversing with the model.

> [!NOTE]
> This is an experimental project. Currently, it supports the Lichuang Dev Board Practical S3 as the hardware platform; more platforms will be supported in the future. In the current version, the LLM can use tools like `start_rtc_stream` and `stop_rtc_stream` to actively control the video stream. Additionally, **McpRTC** supports dynamic adjustment of **bitrate and frame rate** at the framework level. However, due to the hardware performance limitations of the S3, while the tool interfaces are implemented, they are not yet actively called by the LLM in the default demo.

## ️✨ Demo
https://github.com/user-attachments/assets/279b6baf-844d-4e49-9111-cbd73ebee5b4

The video above demonstrates McpRTC under Agent control, autonomously turning on the video on demand:

When the user dialogue does not require video, the video stream is closed (unlike mainstream VAD solutions where user speech triggers video transmission); when the dialogue relies on visual information, the Agent autonomously turns on the video.

The real-time power consumption fluctuations intuitively show the on/off status of the video stream. The power consumption changes under different working modes are as follows:

- **Audio Only Dialogue**: Power fluctuates around **1.1W**.
- **Video-Audio Dialogue**: After turning on the real-time video stream, the power rises to around **1.4W**.

> [!NOTE]
> Why is the power increase small? This is because the S3 board has limited computing power and only supports 320x240@2fps video transmission. The acquisition and encoding overheads are small, so the power increase is not significant compared to audio-only. For terminal devices with stronger computing power, the benefits of McpRTC under high frame rate & high resolution will be more significant.

> [!NOTE]
> Experiencing high latency? The VLM model in this demo is not deployed locally but accessed via API. The end-to-end latency primarily stems from API overhead. For a comparison of response speeds across different VLMs, see: [4.2 LLM Response Speed Comparison](https://github.com/xinnan-tech/xiaozhi-performance-research).

## 🏗 Acknowledgement

This project stands on the shoulders of giants. The core code is developed based on the following two repositories:

* **Firmware**: [xiaozhi-esp32](https://github.com/78/xiaozhi-esp32) - Provided the AI intelligent assistant prototype based on ESP32.
* **Server**: Based on [xiaozhi-esp32-server](https://github.com/xinnan-tech/xiaozhi-esp32-server) - Provided the server-side implementation adapted for xiaozhi-esp32.
