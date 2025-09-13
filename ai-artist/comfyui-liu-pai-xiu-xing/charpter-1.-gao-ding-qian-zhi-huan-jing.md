---
description: 本章将记录如何搞定Comfyui的前置准备
---

# 👻 Charpter 1. 搞定前置环境

## 1.什么是Comfyui

ComfyUI是一个可以用于AI绘图、生成音视频的开源项目。支持完全定制自己的流水线来控制AI创作的每个节点以及步骤，将WebUI（另一个开源的AI项目）中需要反复多次进行的操作流程合并为一整个自动化的流水线。

* 项目地址：[https://github.com/comfyanonymous/ComfyUI](https://github.com/comfyanonymous/ComfyUI)
* ComfyUI官方提供的桌面端程序：[https://www.comfy.org/zh-cn/download](https://www.comfy.org/zh-cn/download)
* 国内社区制作的一键启动整合包：[https://www.bilibili.com/video/BV1m6aBzHEUX/?spm\_id\_from=333.1387.0.0\&vd\_source=fcf41091e9f5fb41916289c9f3873971](https://www.bilibili.com/video/BV1m6aBzHEUX/?spm_id_from=333.1387.0.0\&vd_source=fcf41091e9f5fb41916289c9f3873971)

## 2.本文使用的各种设备以及平台

操作系统：Windows 11 + WSL2 (Linux on Windows)

GPU: Nvidia RTX 5090D 32GB

内存：64GB

CPU: AMD 9800X3D

#### 为什么选择WSL2？

其实Windows下完全可以使用，懒得折腾其实在Windows跑完全没问题，这里主要是因为反正后续训练Lora也要在Linux中进行，索性顺手就全放在WSL2中了，除此之外也是因为Linux环境中运行效率相对高一些。

{% hint style="info" %}
**PS: 要么全在WSL2中进行，要么全在Win中进行，不要把文件放在Win然后在WSL2中去使用，WSL2和Win跨平台文件读写非常消耗性能**
{% endhint %}

## 3.安装并配置WSL2

管理用身份运行PowerShell，输入下面命令就可以开始安装，默认安装Ubuntu

```sh
wsl --install
```

其他安装细节可以参考Win官方提供的手册：[https://learn.microsoft.com/zh-cn/windows/wsl/install](https://learn.microsoft.com/zh-cn/windows/wsl/install)

安装完毕以后可能会弹出配置弹窗:

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

如果没有弹出也可以在windows中搜索WSL，WSL Settings即可打开刚才的页面

<figure><img src="../../.gitbook/assets/image (5).png" alt="" width="464"><figcaption></figcaption></figure>

安装完毕以后默认是在C盘，如果想找到具体的路径：可以在文件系统的地址栏汇中打开WSL的安装目录：**%LOCALAPPDATA%\WSL**

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt="" width="563"><figcaption></figcaption></figure>
