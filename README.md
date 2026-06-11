```markdown
# OpenClaw on Dimensity 9500 —— 基于文心大模型的移动端个人 AI 助手

本项目是 **飞桨黑客松第十期** 进阶任务（联发科·天玑9500），将开源 AI 助手平台 OpenClaw 移植到天玑 9500 Android 设备，并接入百度文心大模型 API，构建移动端个人 AI 助手。

## 环境要求

| 依赖 | 版本/说明 |
|------|-----------|
| 操作系统 | Windows 10/11（开发验证），Android + Termux（最终部署） |
| Node.js | v24.15.0 |
| npm | 11.12.1（随 Node.js 安装） |
| API 服务 | 百度星河社区大模型 API（`https://aistudio.baidu.com/llm/lmapi/v3`） |
| 模型 | `ernie-4.5-turbo-32k`（文心大模型 ERNIE-4.5 系列） |
| OpenClaw | 2026.6.1（全局安装） |

## 项目结构

```
openclaw-dimensity/
├── config/
│   └── openclaw.json          # OpenClaw 主配置文件（已脱敏）
├── screenshots/
│   ├── gateway_ready.png
│   ├── workspace_files.png
│   ├── schtasks_reminder.png
│   ├── taskmgr_memory.png
│   └── node_version.png
├── performance.txt             # 性能报告
└── README.md                   # 本文件
```

## 安装与部署

### 1. 安装 Node.js

**Windows**  
从 [Node.js 官网](https://nodejs.org/) 下载 v24.15.0 并安装。

**Android (Termux)**  
```bash
pkg update && pkg upgrade
pkg install nodejs
```

### 2. 全局安装 OpenClaw

```bash
npm install -g openclaw@latest
```

### 3. 配置文件

编辑用户目录下的 `~/.openclaw/openclaw.json`，填入以下关键配置（**请替换 `apiKey` 为你的星河 Access Token**）：

```json
{
  "gateway": { "mode": "local", "port": 18789 },
  "models": { "providers": { "openai": { "baseUrl": "https://aistudio.baidu.com/llm/lmapi/v3", "apiKey": "你的星河Access Token", "api": "openai-completions", "models": [ { "id": "ernie-4.5-turbo-32k", "name": "ERNIE-4.5-Turbo-32K", "reasoning": false, "input": ["text"], "contextWindow": 32768, "maxTokens": 2048 } ] } } },
  "agents": { "defaults": { "model": "openai/ernie-4.5-turbo-32k" } }
}
```

> ⚠️ **注意**：
> - `maxTokens` 必须 ≤ 2048，否则星河 API 会拒绝请求。
> - 如果你在星河社区开通了其他 ERNIE-4.5 模型（如 `ernie-4.5-8k-preview`），只需替换 `id` 和 `model` 字段即可。

### 4. 启动 Gateway

```bash
openclaw gateway
```

看到 `[gateway] ready` 表示启动成功，保持窗口运行。

### 5. 启动 Agent

打开新终端，执行：

```bash
openclaw chat
```

进入聊天界面后，输入 `/new` 开始对话。

## 性能数据

| 指标 | 实测值 |
|------|--------|
| 内存占用 | < 2 GB（峰值约 1.5 GB） |
| 连续运行 | > 30 分钟无崩溃 |
| API 响应延迟 | 普通对话 30~60 秒，复杂任务 60~120 秒 |
| 任务成功率 | 基础对话 100%，工具调用成功率较高 |

## 任务验证结果

按照赛题要求，在天玑 9500 工程机上成功完成以下三类任务的验证：

| 任务类型 | 指令 | 结果 |
|----------|------|:----:|
| 信息查询 | `Please tell me today's date and day of the week` | ✅ 返回正确日期和星期 |
| 文件操作 | `Please write "OpenClaw test success" to ~/workspace/test.txt and read it back` | ✅ 创建并读取文件成功 |
| 日程提醒 | `Please set a reminder for 3 minutes with message "Demo completed"` | ✅ Agent 确认提醒已设置 |

模型状态：`openai/ernie-4.5-turbo-32k`，Gateway 状态：`reachable`。

## 常见问题

### Q: 出现 `auth or provider access failed for openai`
A: 请检查 `apiKey` 是否正确填写，并确认你的星河 Token 已开通 ERNIE-4.5 系列模型权限。如未开通，前往 [星河社区访问令牌页面](https://aistudio.baidu.com/account/accessToken) 申请或联系官方。

### Q: 出现 `Unknown model`
A: 请确保 `openclaw.json` 中的模型 ID 与星河 API 支持的列表一致，当前可用模型为 `ernie-4.5-turbo-32k`。

### Q: 出现 `HTTP 400: parameter check failed, max_completion_tokens range is [1, 2048]`
A: 在配置文件中将 `maxTokens` 设为 2048 或更小值。

### Q: 对话卡住或返回 `LLM request failed`
A: 可能是会话历史过长超出 token 限制。尝试输入 `/new` 创建新会话，并重启 Gateway。

## 后续工作

- [x] 等待天玑 9500 工程设备
- [x] 在 Termux 中部署 Node.js + OpenClaw
- [x] 录制 ≥3 分钟演示视频
- [ ] 提交最终交付物

## 开发者信息

- GitHub：Vespera49
- 任务序号：#23
- 邮箱：1846968445@qq.com

## 许可证

MIT License
```
