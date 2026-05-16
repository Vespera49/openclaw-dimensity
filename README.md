# OpenClaw on Dimensity 9500 —— 基于文心大模型的移动端个人 AI 助手

本项目是 **飞桨黑客松第十期** 进阶任务（联发科·天玑9500），将开源 AI 助手平台 OpenClaw 移植到天玑 9500 Android 设备，并接入百度文心大模型 API，构建移动端个人 AI 助手。

## 环境要求

| 依赖 | 版本/说明 |
|------|-----------|
| 操作系统 | Windows 10/11（开发验证），Android + Termux（最终部署） |
| Node.js | v24.15.0 |
| npm | 11.12.1（随 Node.js 安装） |
| API 服务 | 百度星河社区大模型 API（`https://aistudio.baidu.com/llm/lmapi/v3`） |
| 模型 | `ernie-3.5-8k` |
| OpenClaw | 2026.5.7（全局安装） |

## 项目结构
```
openclaw-dimensity/
├── config/
│   └── openclaw.json
├── screenshots/
│   ├── gateway_ready.png
│   ├── workspace_files.png
│   ├── schtasks_reminder.png
│   ├── taskmgr_memory.png
│   └── node_version.png
├── performance.txt
└── README.md
```

## 安装与部署

### 1. 安装 Node.js

**Windows**  
从 [Node.js 官网](https://nodejs.org/) 下载 v24.15.0 并安装。

**Android (Termux)**  
```bash
pkg update && pkg upgrade
pkg install nodejs

### 2. 全局安装 OpenClaw

```bash
npm install -g openclaw@latest
```
3. 配置文件
编辑用户目录下的 ~/.openclaw/openclaw.json，填入以下关键配置（请替换 apiKey 为你的星河 Access Token）：

json
{
  "gateway": {
    "mode": "local",
    "port": 18789
  },
  "models": {
    "providers": {
      "openai": {
        "baseUrl": "https://aistudio.baidu.com/llm/lmapi/v3",
        "apiKey": "你的星河Access Token",
        "api": "openai-completions",
        "models": [
          {
            "id": "ernie-3.5-8k",
            "name": "ERNIE-3.5-8K",
            "reasoning": false,
            "input": ["text"],
            "contextWindow": 8000,
            "maxTokens": 2048
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": "openai/ernie-3.5-8k"
    }
  }
}
⚠️ 注意：maxTokens 必须 ≤ 2048，否则星河 API 会拒绝请求。

4. 启动 Gateway
由于全局命令可能因 PATH 问题不可用，推荐直接用 Node 启动：

bash
node D:\node_global\node_modules\openclaw\openclaw.mjs gateway
看到 [gateway] ready 表示启动成功，保持窗口运行。

5. 启动 Agent
打开新终端，执行：

bash
node D:\node_global\node_modules\openclaw\openclaw.mjs chat
进入聊天界面后，输入 /think off 关闭思维链模式，然后即可开始对话。

性能数据
指标	实测值
内存占用	< 2 GB（峰值约 1.5 GB）
连续运行	> 30 分钟无崩溃
API 响应延迟	普通对话 30~60 秒，复杂任务 60~120 秒
任务成功率	基础对话 100%，工具调用因 API 兼容性问题偶有失败
任务验证结果
按照赛题要求，已完成以下三类任务的验证：

任务类型	完成方式	截图
文件操作	Agent 成功在工作区创建并读取 test_openclaw.txt	workspace_files.png
信息查询	手动创建日期文件并通过 Agent 确认	workspace_files.png
日程提醒	使用 schtasks 创建系统计划任务，5分钟后弹窗提醒	schtasks_reminder.png
常见问题
Q: 启动时提示 'openclaw' 不是内部或外部命令
A: 使用 Node 直接启动（见上方命令），或手动将 D:\node_global 添加到系统 PATH。

Q: 出现 Unknown model: openai/qianfan:ernie-4.5-turbo-8k
A: 模型 ID 与星河 API 支持列表不匹配。请确保 openclaw.json 中的模型 ID 为 ernie-3.5-8k。

Q: 出现 HTTP 400: parameter check failed, max_completion_tokens range is [1, 2048]
A: 在配置文件中将 maxTokens 设为 2048 或更小值。

Q: 对话卡住或返回 LLM request failed
A: 可能是会话历史过长超出 token 限制。尝试输入 /think off 并重启 Agent。

后续工作
等待天玑 9500 工程设备

在 Termux 中部署 Node.js + OpenClaw

录制 ≥3 分钟演示视频

提交最终交付物

开发者信息
GitHub：Vespera49

任务序号：# 23

邮箱：1846968445@qq.com

许可证
MIT License
