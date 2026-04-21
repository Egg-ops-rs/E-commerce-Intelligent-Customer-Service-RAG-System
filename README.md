# 问道星辰：企业级智能客服系统

一个基于 `FastAPI`、`LangGraph`、`GraphRAG` 和多模型接入能力构建的智能客服项目。系统面向企业知识问答、联网检索、文档问答、图谱推理和多轮会话等场景，支持同时接入 `DeepSeek`、`Ollama`、视觉模型和 GraphRAG 检索链路。

## 核心能力

- 通用对话：支持 DeepSeek 在线模型与 Ollama 本地模型切换
- 推理问答：支持深度思考类模型和多轮流式输出
- Agent 编排：基于 LangGraph 组织工具调用、检索与推理流程
- 文档问答：支持文件上传、索引构建和基于知识库的问答
- 图谱增强：集成 Neo4j 与 GraphRAG 能力
- 联网搜索：支持通过 SerpAPI 补充外部信息
- 图片理解：支持图像输入与视觉模型分析

## 技术栈

- 后端：`FastAPI`、`SQLAlchemy`、`Redis`
- 大模型编排：`LangGraph`、`LangChain`
- 检索增强：`GraphRAG`、`Neo4j`
- 模型支持：`DeepSeek`、`Ollama`、OpenAI 兼容视觉/Embedding 接口

## 项目结构

```text
.
├─ llm_backend/
│  ├─ app/
│  │  ├─ api/                # 接口路由
│  │  ├─ core/               # 配置、日志、数据库等基础模块
│  │  ├─ services/           # 聊天、搜索、索引等服务
│  │  ├─ lg_agent/           # LangGraph Agent 相关逻辑
│  │  └─ graphrag/           # GraphRAG 相关配置和代码
│  ├─ static/dist/           # 前端静态资源
│  ├─ main.py                # FastAPI 入口
│  └─ run.py                 # 本地启动脚本
├─ .env.example              # 环境变量模板
└─ requirements.txt          # Python 依赖
```

## 快速开始

### 1. 安装依赖

```bash
python -m venv .venv
```

Windows:

```bash
.venv\Scripts\activate
pip install -r requirements.txt
```

Linux / macOS:

```bash
source .venv/bin/activate
pip install -r requirements.txt
```

### 2. 配置环境变量

将根目录的 `.env.example` 复制为 `llm_backend/.env`，然后按你的实际环境填写：

```bash
copy .env.example llm_backend\.env
```

或：

```bash
cp .env.example llm_backend/.env
```

当前项目已经调整为在后端启动时自动加载 `llm_backend/.env`，同时 GraphRAG 的 YAML 配置也会复用这套环境变量。

常用变量示例：

```env
DEEPSEEK_API_KEY=your-deepseek-api-key
DEEPSEEK_BASE_URL=https://api.deepseek.com
DEEPSEEK_MODEL=deepseek-chat

VISION_API_KEY=your-vision-api-key
VISION_BASE_URL=https://ai.devtool.tech/proxy/v1
VISION_MODEL=gpt-4o

EMBEDDING_API_KEY=your-embedding-api-key
EMBEDDING_API_BASE=https://ai.devtool.tech/proxy/v1
EMBEDDING_MODEL_NAME=text-embedding-3-small

CHAT_SERVICE=deepseek
REASON_SERVICE=ollama
AGENT_SERVICE=deepseek
```

### 3. 准备依赖服务

请确保以下服务可用，并在 `llm_backend/.env` 中填写对应连接信息：

- MySQL
- Redis
- Neo4j
- DeepSeek 或 Ollama

如果你使用文档索引或 GraphRAG，还需要检查：

- `GRAPHRAG_PROJECT_DIR`
- `GRAPHRAG_DATA_DIR`
- Embedding 相关配置

### 4. 启动后端

```bash
cd llm_backend
python run.py
```

默认访问地址：

- 接口文档：`http://localhost:8000/docs`
- 应用首页：`http://localhost:8000`
- 健康检查：`http://localhost:8000/health`

## 配置说明

### 模型切换

- `CHAT_SERVICE=deepseek` 或 `ollama`
- `REASON_SERVICE=deepseek` 或 `ollama`
- `AGENT_SERVICE=deepseek` 或 `ollama`

### GraphRAG 配置

`llm_backend/app/graphrag/settings.yaml` 和 `llm_backend/app/graphrag/data/settings_pdf.yaml` 已改为通过环境变量读取模型配置，避免将密钥直接写入仓库。

### 安全建议

- 不要把真实的 `.env` 文件提交到 GitHub
- 不要在 `settings.yaml`、脚本或测试文件中写死 API Key
- 生产环境请替换 `SECRET_KEY` 为高强度随机值

## 常见问题

### 1. 启动时报缺少环境变量

检查 `llm_backend/.env` 是否存在，并确认必要变量已经填写完整。

### 2. GraphRAG 无法构建索引

优先确认以下几项：

- GraphRAG 相关目录是否存在
- Embedding 模型配置是否可用
- 上传文件目录和输出目录是否有写权限

### 3. Ollama 模型调用失败

检查本机或远程 Ollama 服务地址是否正确，以及对应模型是否已拉取完成。