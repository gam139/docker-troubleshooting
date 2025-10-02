markdown
---

# Docker Desktop 启动失败故障排查记录（Windows + WSL2）

**创作者**：高飞  
**时间**：2025年10月2日 上午  

---

## 📋 目录

- [故障背景](#故障背景)
- [排查过程](#排查过程)
- [最终解决方案](#最终解决方案)
- [工具来源](#工具来源)
- [故障总结](#故障总结)

---

## 🧩 故障背景

- 开机后系统卡顿，任务管理器显示内存占用超过 90%
- 重启后 Docker 无法启动，WSL 子系统状态为 `Stopped`
- 执行 `docker info` 报错：
error during connect: Get "http://%2F%2F.%2Fpipe%2FdockerDesktopLinuxEngine/v1.50/info": open //./pipe/dockerDesktopLinuxEngine: The system cannot find the file specified.

代码
- Windows 服务中未注册 `com.docker.service`

---

## 🔍 排查过程

1. 使用 `wsl -l -v` 查看 WSL 子系统状态
2. 启动 `docker-desktop` 子系统：`wsl -d docker-desktop`
3. 启动 `Ubuntu-20.04` 子系统：`wsl -d Ubuntu-20.04`
4. 验证 Docker daemon 是否恢复：`docker info`
5. 检查 Windows 服务：`sc query com.docker.service`
6. 使用 `wsl --shutdown` 清理 WSL 状态
7. 使用 Docker Desktop 的 Troubleshoot 工具尝试修复

---

## ✅ 最终解决方案

- 使用来自 Pi Node 节点管理器的“一键删除 Docker 所有文件”小程序彻底清除 Docker 残留配置
- 卸载 Docker Desktop
- 重新安装 Docker Desktop（以管理员身份运行）
- 问题成功解决，Docker daemon 正常启动

---

## 🛠️ 工具来源

- 工具名称：一键删除 Docker 所有文件的小程序  
- 来源平台：[Pi Node 节点管理器官网](http://glq.pi-node.cn/dl/)  
- 说明：该工具可彻底清除 Docker Desktop 的所有残留配置，包括 WSL 子系统、注册表项、命名管道和缓存文件

---

## 📌 故障总结

这次故障的根因可能是：

- WSL 虚拟机资源泄漏导致系统卡顿
- Docker Desktop 后端未能成功初始化 `dockerd`
- 残留配置阻止服务注册与管道创建

最终通过彻底清理 + 重新安装解决问题，建议今后：

- 定期清理未使用的容器与镜像
- 关闭 Docker 的自动启动功能
- 保留卸载工具以备后用

---

> 📎 本文适用于 Windows + WSL2 环境下 Docker Desktop 启动失败的排查与恢复，欢迎收藏或分享。
