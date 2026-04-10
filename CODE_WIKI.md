# Multica Code Wiki

## 1. 项目概述

Multica是一个开源的智能体管理平台，将编码智能体转变为真正的团队成员。它允许用户像分配任务给同事一样分配任务给智能体，智能体会自主完成工作、报告障碍并更新状态。

**主要功能**：
- 智能体作为团队成员参与工作流程
- 自主执行任务，全生命周期管理
- 可重用的技能系统
- 统一的运行时管理
- 多工作空间支持

## 2. 目录结构

Multica采用monorepo结构，使用pnpm和turbo进行依赖管理和构建。项目分为前端、后端和共享包三个主要部分。

```
├── apps/              # 应用目录
│   ├── desktop/       # 桌面应用
│   └── web/           # Web应用
├── docs/              # 文档目录
├── e2e/               # 端到端测试
├── packages/          # 共享包
│   ├── core/          # 核心功能包
│   ├── eslint-config/ # ESLint配置
│   ├── tsconfig/      # TypeScript配置
│   ├── ui/            # UI组件库
│   └── views/         # 视图组件
├── scripts/           # 脚本文件
├── server/            # 后端服务器
│   ├── cmd/           # 命令行工具
│   ├── internal/      # 内部包
│   ├── migrations/    # 数据库迁移
│   └── pkg/           # 公共包
├── .env.example       # 环境变量示例
├── docker-compose.yml # Docker Compose配置
├── Makefile           # 构建和运行命令
└── package.json       # 项目配置
```

### 2.1 主要目录说明

| 目录 | 描述 | 路径 |
|------|------|------|
| Web应用 | 基于Next.js的前端应用 | [apps/web](file:///workspace/apps/web) |
| 桌面应用 | 基于Electron的桌面应用 | [apps/desktop](file:///workspace/apps/desktop) |
| 核心包 | 前端核心功能，包括API客户端、认证等 | [packages/core](file:///workspace/packages/core) |
| UI组件库 | 可复用的UI组件 | [packages/ui](file:///workspace/packages/ui) |
| 视图组件 | 业务逻辑相关的视图组件 | [packages/views](file:///workspace/packages/views) |
| 后端服务器 | Go语言实现的后端服务 | [server](file:///workspace/server) |
| 数据库迁移 | PostgreSQL数据库迁移脚本 | [server/migrations](file:///workspace/server/migrations) |

## 3. 系统架构

Multica采用前后端分离的架构，前端使用Next.js，后端使用Go语言，数据库使用PostgreSQL。

```
┌──────────────┐     ┌──────────────┐     ┌──────────────────┐
│   Next.js    │────>│  Go Backend  │────>│   PostgreSQL     │
│   Frontend   │<────│  (Chi + WS)  │<────│   (pgvector)     │
└──────────────┘     └──────┬───────┘     └──────────────────┘
                            │
                     ┌──────┴───────┐
                     │ Agent Daemon │  (runs on your machine)
                     │Claude/Codex/ │
                     │OpenClaw/Code │
                     └──────────────┘
```

### 3.1 技术栈

| 层 | 技术 | 路径 |
|------|------|------|
| 前端 | Next.js 16 (App Router) | [apps/web](file:///workspace/apps/web) |
| 后端 | Go (Chi router, sqlc, gorilla/websocket) | [server](file:///workspace/server) |
| 数据库 | PostgreSQL 17 with pgvector | [server/migrations](file:///workspace/server/migrations) |
| 智能体运行时 | 本地守护进程执行Claude Code, Codex, OpenClaw, 或OpenCode | [server/cmd/multica](file:///workspace/server/cmd/multica) |

## 4. 主要模块

### 4.1 前端模块

| 模块 | 描述 | 路径 |
|------|------|------|
| 认证模块 | 处理用户登录和认证 | [apps/web/app/(auth)](file:///workspace/apps/web/app/(auth)) |
| 仪表盘模块 | 主要的用户界面，包括问题管理、项目管理等 | [apps/web/app/(dashboard)](file:///workspace/apps/web/app/(dashboard)) |
| 登录页面 | 用户登录界面 | [packages/views/auth](file:///workspace/packages/views/auth) |
| 问题管理 | 问题的创建、编辑、查看和管理 | [packages/views/issues](file:///workspace/packages/views/issues) |
| 智能体管理 | 智能体的创建、配置和管理 | [packages/views/agents](file:///workspace/packages/views/agents) |
| 运行时管理 | 运行时的监控和管理 | [packages/views/runtimes](file:///workspace/packages/views/runtimes) |
| 项目管理 | 项目的创建、编辑和管理 | [packages/views/projects](file:///workspace/packages/views/projects) |
| 技能管理 | 技能的创建、编辑和管理 | [packages/views/skills](file:///workspace/packages/views/skills) |
| 内容编辑器 | 富文本编辑器，支持Markdown | [packages/views/editor](file:///workspace/packages/views/editor) |

### 4.2 后端模块

| 模块 | 描述 | 路径 |
|------|------|------|
| 认证服务 | 处理用户认证和授权 | [server/internal/auth](file:///workspace/server/internal/auth) |
| 问题服务 | 处理问题的CRUD操作 | [server/internal/handler/issue.go](file:///workspace/server/internal/handler/issue.go) |
| 智能体服务 | 处理智能体的CRUD操作和任务执行 | [server/internal/handler/agent.go](file:///workspace/server/internal/handler/agent.go) |
| 运行时服务 | 处理运行时的监控和管理 | [server/internal/handler/runtime.go](file:///workspace/server/internal/handler/runtime.go) |
| 项目服务 | 处理项目的CRUD操作 | [server/internal/handler/project.go](file:///workspace/server/internal/handler/project.go) |
| 技能服务 | 处理技能的CRUD操作 | [server/internal/handler/skill.go](file:///workspace/server/internal/handler/skill.go) |
| 实时通信 | 处理WebSocket连接和实时更新 | [server/internal/realtime](file:///workspace/server/internal/realtime) |
| 事件总线 | 处理系统事件的发布和订阅 | [server/internal/events](file:///workspace/server/internal/events) |

### 4.3 核心包模块

| 模块 | 描述 | 路径 |
|------|------|------|
| API客户端 | 前端与后端通信的API客户端 | [packages/core/api](file:///workspace/packages/core/api) |
| 认证 | 处理用户认证和会话管理 | [packages/core/auth](file:///workspace/packages/core/auth) |
| 问题 | 处理问题相关的状态管理和操作 | [packages/core/issues](file:///workspace/packages/core/issues) |
| 工作空间 | 处理工作空间的管理和切换 | [packages/core/workspace](file:///workspace/packages/core/workspace) |
| 实时同步 | 处理WebSocket连接和实时数据同步 | [packages/core/realtime](file:///workspace/packages/core/realtime) |
| 类型定义 | 系统中使用的TypeScript类型定义 | [packages/core/types](file:///workspace/packages/core/types) |

## 5. 核心功能

### 5.1 问题管理

问题管理是Multica的核心功能之一，允许用户创建、分配、跟踪和管理问题。

- **创建问题**：支持标题、描述、优先级、状态、指派等字段
- **问题列表**：支持按状态、优先级、指派等过滤
- **问题详情**：查看问题的详细信息、评论、历史记录等
- **问题评论**：支持Markdown格式的评论，包括代码块、图片等
- **问题状态**：支持自定义状态和工作流
- **问题优先级**：支持高、中、低优先级

### 5.2 智能体管理

智能体管理允许用户创建和配置智能体，将任务分配给智能体执行。

- **创建智能体**：设置智能体名称、描述、运行时、提供商等
- **智能体配置**：配置智能体的指令、技能、并发等
- **智能体任务**：查看智能体的任务历史和执行状态
- **智能体技能**：为智能体分配和管理技能

### 5.3 运行时管理

运行时管理允许用户监控和管理智能体的运行环境。

- **运行时列表**：查看所有可用的运行时
- **运行时详情**：查看运行时的详细信息、使用情况等
- **运行时监控**：监控运行时的状态和资源使用情况
- **运行时更新**：更新运行时的版本

### 5.4 项目管理

项目管理允许用户创建和管理项目，将问题组织到项目中。

- **创建项目**：设置项目名称、描述、状态等
- **项目详情**：查看项目的详细信息和相关问题
- **项目问题**：查看和管理项目中的问题

### 5.5 技能管理

技能管理允许用户创建和管理可重用的技能，智能体可以使用这些技能来完成任务。

- **创建技能**：设置技能名称、描述、代码等
- **技能列表**：查看所有可用的技能
- **技能详情**：查看技能的详细信息和使用情况
- **技能导入**：从外部源导入技能

### 5.6 聊天功能

聊天功能允许用户与智能体进行实时通信。

- **创建聊天会话**：与智能体开始新的聊天会话
- **发送消息**：向智能体发送消息
- **查看聊天历史**：查看与智能体的聊天历史

### 5.7 通知系统

通知系统会向用户发送系统事件和更新的通知。

- **收件箱**：查看所有通知
- **未读通知**：查看未读的通知
- **通知状态**：标记通知为已读或归档

## 6. 关键类与函数

### 6.1 前端关键类与函数

#### ApiClient

**描述**：前端与后端通信的API客户端，封装了所有API请求。

**路径**：[packages/core/api/client.ts](file:///workspace/packages/core/api/client.ts)

**主要方法**：
- `setToken(token: string | null)`：设置认证令牌
- `setWorkspaceId(id: string | null)`：设置工作空间ID
- `fetch<T>(path: string, init?: RequestInit): Promise<T>`：通用的API请求方法
- `listIssues(params?: ListIssuesParams): Promise<ListIssuesResponse>`：获取问题列表
- `createIssue(data: CreateIssueRequest): Promise<Issue>`：创建问题
- `updateIssue(id: string, data: UpdateIssueRequest): Promise<Issue>`：更新问题
- `listAgents(params?: { workspace_id?: string; include_archived?: boolean }): Promise<Agent[]>`：获取智能体列表
- `createAgent(data: CreateAgentRequest): Promise<Agent>`：创建智能体

#### CoreProvider

**描述**：前端的核心提供者，初始化API客户端、认证存储、工作空间存储等。

**路径**：[packages/core/platform/core-provider.tsx](file:///workspace/packages/core/platform/core-provider.tsx)

**主要功能**：
- 初始化API客户端
- 初始化认证存储
- 初始化工作空间存储
- 提供QueryClient和WebSocket连接

#### QueryProvider

**描述**：提供React Query的配置和管理。

**路径**：[packages/core/provider.tsx](file:///workspace/packages/core/provider.tsx)

**主要功能**：
- 创建和提供QueryClient
- 提供React Query DevTools

### 6.2 后端关键类与函数

#### main (server)

**描述**：后端服务器的主入口，初始化数据库连接、事件总线、实时通信等。

**路径**：[server/cmd/server/main.go](file:///workspace/server/cmd/server/main.go)

**主要功能**：
- 初始化日志
- 连接数据库
- 创建事件总线
- 创建实时通信中心
- 注册监听器
- 启动HTTP服务器
- 启动运行时清理器

#### NewRouter

**描述**：创建和配置HTTP路由。

**路径**：[server/cmd/server/router.go](file:///workspace/server/cmd/server/router.go)

**主要功能**：
- 配置路由
- 应用中间件
- 注册处理函数

#### Hub (realtime)

**描述**：实时通信中心，管理WebSocket连接和消息广播。

**路径**：[server/internal/realtime/hub.go](file:///workspace/server/internal/realtime/hub.go)

**主要功能**：
- 管理客户端连接
- 广播消息
- 处理客户端注册和注销

#### ApiClient (前端)

**描述**：前端与后端通信的API客户端，封装了所有API请求。

**路径**：[packages/core/api/client.ts](file:///workspace/packages/core/api/client.ts)

**主要方法**：
- `setToken(token: string | null)`：设置认证令牌
- `setWorkspaceId(id: string | null)`：设置工作空间ID
- `fetch<T>(path: string, init?: RequestInit): Promise<T>`：通用的API请求方法
- `listIssues(params?: ListIssuesParams): Promise<ListIssuesResponse>`：获取问题列表
- `createIssue(data: CreateIssueRequest): Promise<Issue>`：创建问题

## 7. 依赖关系

### 7.1 前端依赖

| 依赖 | 版本 | 用途 | 路径 |
|------|------|------|------|
| next | ^16.1.6 | React框架 | [apps/web/package.json](file:///workspace/apps/web/package.json) |
| react | catalog: | React库 | [apps/web/package.json](file:///workspace/apps/web/package.json) |
| @tanstack/react-query | ^5.96.2 | 数据获取和状态管理 | [apps/web/package.json](file:///workspace/apps/web/package.json) |
| @tiptap/react | ^3.22.1 | 富文本编辑器 | [apps/web/package.json](file:///workspace/apps/web/package.json) |
| zustand | catalog: | 状态管理 | [apps/web/package.json](file:///workspace/apps/web/package.json) |
| tailwindcss | catalog: | CSS框架 | [apps/web/package.json](file:///workspace/apps/web/package.json) |
| @dnd-kit/core | ^6.3.1 | 拖放功能 | [apps/web/package.json](file:///workspace/apps/web/package.json) |
| recharts | 3.8.0 | 图表库 | [apps/web/package.json](file:///workspace/apps/web/package.json) |

### 7.2 后端依赖

| 依赖 | 版本 | 用途 | 路径 |
|------|------|------|------|
| github.com/go-chi/chi/v5 | v5.2.5 | HTTP路由器 | [server/go.mod](file:///workspace/server/go.mod) |
| github.com/gorilla/websocket | v1.5.3 | WebSocket支持 | [server/go.mod](file:///workspace/server/go.mod) |
| github.com/jackc/pgx/v5 | v5.8.0 | PostgreSQL驱动 | [server/go.mod](file:///workspace/server/go.mod) |
| github.com/golang-jwt/jwt/v5 | v5.3.1 | JWT认证 | [server/go.mod](file:///workspace/server/go.mod) |
| github.com/aws/aws-sdk-go-v2 | v1.41.5 | AWS SDK | [server/go.mod](file:///workspace/server/go.mod) |
| github.com/spf13/cobra | v1.10.2 | 命令行工具 | [server/go.mod](file:///workspace/server/go.mod) |

## 8. 运行与配置

### 8.1 环境变量

Multica使用.env文件来配置环境变量，主要包括：

| 环境变量 | 描述 | 默认值 |
|----------|------|--------|
| PORT | 后端服务器端口 | 8080 |
| FRONTEND_PORT | 前端服务器端口 | 3000 |
| DATABASE_URL | 数据库连接字符串 | postgres://multica:multica@localhost:5432/multica?sslmode=disable |
| NEXT_PUBLIC_API_URL | 前端API URL | http://localhost:8080 |
| NEXT_PUBLIC_WS_URL | 前端WebSocket URL | ws://localhost:8080/ws |
| JWT_SECRET | JWT签名密钥 | (必须设置) |

### 8.2 运行命令

| 命令 | 描述 | 路径 |
|------|------|------|
| make setup | 首次设置：安装依赖、启动数据库、运行迁移 | [Makefile](file:///workspace/Makefile) |
| make start | 启动所有服务（后端 + 前端） | [Makefile](file:///workspace/Makefile) |
| make stop | 停止所有服务 | [Makefile](file:///workspace/Makefile) |
| make dev | 启动后端服务器 | [Makefile](file:///workspace/Makefile) |
| make daemon | 启动智能体守护进程 | [Makefile](file:///workspace/Makefile) |
| make migrate-up | 运行数据库迁移 | [Makefile](file:///workspace/Makefile) |
| make build | 构建项目 | [Makefile](file:///workspace/Makefile) |

### 8.3 Docker Compose

Multica使用Docker Compose来管理PostgreSQL数据库：

```bash
docker compose up -d postgres  # 启动PostgreSQL
docker compose down             # 停止PostgreSQL
```

## 9. 开发流程

### 9.1 开发环境设置

1. 克隆仓库：`git clone https://github.com/multica-ai/multica.git`
2. 安装依赖：`pnpm install`
3. 配置环境变量：`cp .env.example .env`
4. 启动数据库：`docker compose up -d postgres`
5. 运行迁移：`make migrate-up`
6. 启动开发服务器：`make start`

### 9.2 代码规范

- 前端使用ESLint和TypeScript进行代码检查
- 后端使用Go的标准代码规范
- 提交前运行：`make check`进行全面检查

### 9.3 测试

- 前端：`pnpm test`
- 后端：`make test`
- 端到端测试：`npx playwright test`

## 10. 部署

### 10.1 自托管部署

1. 克隆仓库：`git clone https://github.com/multica-ai/multica.git`
2. 配置环境变量：`cp .env.example .env`
3. 启动数据库：`docker compose up -d postgres`
4. 运行迁移：`cd server && go run ./cmd/migrate up && cd ..`
5. 启动应用：`make start`

### 10.2 生产部署

1. 构建项目：`make build`
2. 配置生产环境变量
3. 部署后端服务器和前端静态文件
4. 配置反向代理（如Nginx）

## 11. 常见问题

### 11.1 数据库连接问题

- 确保PostgreSQL服务正在运行
- 检查DATABASE_URL环境变量是否正确
- 检查数据库用户和密码是否正确

### 11.2 智能体执行问题

- 确保智能体守护进程正在运行：`make daemon`
- 检查运行时是否已连接
- 检查智能体配置是否正确

### 11.3 实时更新问题

- 检查WebSocket连接是否正常
- 检查网络防火墙设置
- 检查后端服务器是否正在运行

### 11.4 依赖安装问题

- 确保使用pnpm安装依赖：`pnpm install`
- 确保Node.js版本 >= v20
- 确保Go版本 >= v1.26

## 12. 总结

Multica是一个功能强大的开源智能体管理平台，通过将编码智能体转变为真正的团队成员，提高了开发效率和团队协作。它采用现代化的技术栈，包括Next.js前端、Go后端和PostgreSQL数据库，支持智能体的全生命周期管理、可重用的技能系统、统一的运行时管理和多工作空间支持。

通过本Code Wiki文档，开发者可以快速了解Multica的架构、功能和使用方法，从而更好地参与到项目的开发和维护中。