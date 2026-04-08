# AI Monorepo — Replit AI API 代理

## 概述

一个兼容 OpenAI 接口的 AI 代理服务，通过 Replit AI Integrations 支持将请求透明转发至 Anthropic (Claude)、OpenAI、Gemini 和 OpenRouter，无需自备 API Key。

**来源：** https://github.com/Akatsuki03/AI-Monorepo.git

## 架构

```
artifacts/api-server/   — 核心 AI 代理 API 服务 (Express 5, port 8080, /api)
artifacts/api-portal/   — React 管理门户前端 (Vite, port 24927, /)
lib/api-spec/           — OpenAPI 规范 (单一数据源)
lib/api-client-react/   — 生成的 React Query 钩子
lib/api-zod/            — 生成的 Zod 验证 schema
lib/db/                 — PostgreSQL + Drizzle ORM
```

## Stack

- **Monorepo**: pnpm workspaces
- **Node.js**: 24
- **API 框架**: Express 5
- **前端**: React + Vite + TailwindCSS 4
- **数据库**: PostgreSQL + Drizzle ORM
- **验证**: Zod (zod/v4), drizzle-zod
- **API Codegen**: Orval (from OpenAPI spec)
- **AI 集成**: Replit AI Integrations (Anthropic, OpenAI, Gemini, OpenRouter)

## 核心功能

- 🔀 双 Provider 路由 — `claude-*` 走 Anthropic，其余走 OpenAI/Gemini/OpenRouter
- 🛠 完整 Tool Calling — 流式与非流式均支持
- 🖼 图像识别 — URL 自动预取转 base64
- 🧠 Claude 思考模式 — 模型名后缀 `-thinking`/`-thinking-visible`
- 🔑 三种鉴权方式 — Bearer token、x-goog-api-key、?key= 查询参数
- 🖥 管理门户 — 配置向导、版本检测、一键更新

## 重要环境变量

| 变量名 | 说明 |
|--------|------|
| `PROXY_API_KEY` | 代理服务的访问密钥（已自动生成） |
| `AI_INTEGRATIONS_ANTHROPIC_*` | Anthropic 集成（自动配置） |
| `AI_INTEGRATIONS_OPENAI_*` | OpenAI 集成（自动配置） |
| `AI_INTEGRATIONS_GEMINI_*` | Gemini 集成（自动配置） |
| `AI_INTEGRATIONS_OPENROUTER_*` | OpenRouter 集成（自动配置） |

## API 端点

- `GET /api/healthz` — 健康检查
- `GET /api/setup-status` — 配置状态
- `GET /api/v1/models` — 可用模型列表
- `POST /api/v1/chat/completions` — 聊天补全（兼容 OpenAI 格式）

## 使用方法

部署后，将任意 OpenAI 兼容客户端的 Base URL 改为：
```
https://你的域名.replit.app/api
```
API Key 填写你的 `PROXY_API_KEY`。

## 关键命令

- `pnpm --filter @workspace/api-server run dev` — 启动 API 服务器
- `pnpm --filter @workspace/api-portal run dev` — 启动管理门户
- `pnpm --filter @workspace/api-spec run codegen` — 重新生成 API 钩子
- `pnpm --filter @workspace/db run push` — 推送 DB schema 变更
