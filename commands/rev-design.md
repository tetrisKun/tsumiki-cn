---
description: 从现有代码库逆向生成技术设计文档。分析已实现的架构、数据流、API规格、数据库架构、TypeScript接口,并将其文档化为设计书。
---

# rev-design

## 目的

从现有代码库逆向生成技术设计文档。分析已实现的架构、数据流、API规格、数据库架构、TypeScript接口,并将其文档化为设计书。

## 前提条件

- 存在待分析的代码库
- 存在 `docs/reverse/` 目录(如果不存在则创建)
- 如果可能,已执行 `/jimu:rev-tasks`

## 执行内容

1. **架构分析**
   - 从项目结构识别架构模式
   - 确认层级构成(MVC、Clean Architecture等)
   - 微服务架构的有无
   - 前端/后端的分离情况

2. **数据流提取**
   - 用户交互流程
   - API调用流程
   - 数据库访问模式
   - 状态管理流程

3. **API规格提取**
   - 生成端点列表
   - 分析请求/响应结构
   - 确认认证·授权方式
   - 错误响应格式

4. **数据库架构逆向生成**
   - 提取表定义
   - 分析关系
   - 确认索引设置
   - 提取约束条件

5. **TypeScript类型定义整理**
   - 提取实体类型
   - 提取API类型
   - 整理通用类型
   - 类型依赖关系分析

6. **组件设计分析**
   - UI组件层次结构
   - Props接口
   - 状态管理设计
   - 路由设计

7. **文件创建**
   - `docs/reverse/{项目名}-architecture.md` - 架构概述
   - `docs/reverse/{项目名}-dataflow.md` - 数据流图
   - `docs/reverse/{项目名}-api-specs.md` - API规格
   - `docs/reverse/{项目名}-database.md` - DB设计
   - `docs/reverse/{项目名}-interfaces.ts` - 类型定义汇总

## 输出格式示例

### architecture.md

```markdown
# {项目名} 架构设计(逆向生成)

## 分析日期
{执行日期}

## 系统概述

### 已实现的架构
- **模式**: {识别的架构模式}
- **框架**: {使用的框架}
- **构成**: {发现的构成}

### 技术栈

#### 前端
- **框架**: {React/Vue/Angular等}
- **状态管理**: {Redux/Zustand/Pinia等}
- **UI库**: {Material-UI/Ant Design等}
- **样式**: {CSS Modules/styled-components等}

#### 后端
- **框架**: {Express/NestJS/FastAPI等}
- **认证方式**: {JWT/Session/OAuth等}
- **ORM/数据访问**: {TypeORM/Prisma/Sequelize等}
- **验证**: {Joi/Yup/zod等}

#### 数据库
- **DBMS**: {PostgreSQL/MySQL/MongoDB等}
- **缓存**: {Redis/Memcached等 或 无}
- **连接池**: {是否实现}

#### 基础设施·工具
- **构建工具**: {Webpack/Vite/Rollup等}
- **测试框架**: {Jest/Vitest/Pytest等}
- **代码质量**: {ESLint/Prettier/SonarQube等}

## 层级构成

### 发现的层级
```
{实际的目录结构}
```

### 层级职责分析
- **表现层**: {实现情况}
- **应用层**: {实现情况}
- **领域层**: {实现情况}
- **基础设施层**: {实现情况}

## 设计模式

### 发现的模式
- **依赖注入**: {是否实现}
- **仓库模式**: {是否实现}
- **工厂模式**: {使用位置}
- **观察者模式**: {使用位置}
- **策略模式**: {使用位置}

## 非功能需求实现情况

### 安全性
- **认证**: {实现方式}
- **授权**: {实现方式}
- **CORS设置**: {设置状况}
- **HTTPS对应**: {对应状况}

### 性能
- **缓存**: {实现状况}
- **数据库优化**: {索引等}
- **CDN**: {使用状况}
- **图像优化**: {实现状况}

### 运维·监控
- **日志输出**: {实现状况}
- **错误追踪**: {实现状况}
- **指标收集**: {实现状况}
- **健康检查**: {实现状况}
```

### dataflow.md

```markdown
# 数据流图(逆向生成)

## 用户交互流程

### 认证流程
\`\`\`mermaid
sequenceDiagram
    participant U as 用户
    participant F as 前端
    participant B as 后端
    participant D as 数据库

    U->>F: 输入登录信息
    F->>B: POST /auth/login
    B->>D: 用户验证
    D-->>B: 用户信息
    B-->>F: JWT令牌
    F-->>U: 登录完成
\`\`\`

### 数据获取流程
\`\`\`mermaid
flowchart TD
    A[用户操作] --> B[React组件]
    B --> C[useQuery钩子]
    C --> D[Axios HTTP Client]
    D --> E[API Gateway/Express]
    E --> F[控制器]
    F --> G[服务层]
    G --> H[仓库层]
    H --> I[数据库]
    I --> H
    H --> G
    G --> F
    F --> E
    E --> D
    D --> C
    C --> B
    B --> J[UI更新]
\`\`\`

## 状态管理流程

### {使用的状态管理库} 流程
\`\`\`mermaid
flowchart LR
    A[组件] --> B[Action Dispatch]
    B --> C[Reducer/Store]
    C --> D[状态更新]
    D --> A
\`\`\`

## 错误处理流程

\`\`\`mermaid
flowchart TD
    A[错误发生] --> B{错误类型}
    B -->|认证错误| C[重定向到登录]
    B -->|网络错误| D[重试功能]
    B -->|验证错误| E[表单错误显示]
    B -->|服务器错误| F[错误提示显示]
\`\`\`
```

### api-specs.md

```markdown
# API规格书(逆向生成)

## 基础URL
\`{发现的基础URL}\`

## 认证方式
{发现的认证方式详情}

## 端点列表

### 认证相关

#### POST /auth/login
**说明**: 用户登录

**请求**:
\`\`\`typescript
{
  email: string;
  password: string;
}
\`\`\`

**响应**:
\`\`\`typescript
{
  success: boolean;
  data: {
    token: string;
    user: {
      id: string;
      email: string;
      name: string;
    }
  };
}
\`\`\`

**错误响应**:
\`\`\`typescript
{
  success: false;
  error: {
    code: string;
    message: string;
  }
}
\`\`\`

#### POST /auth/logout
**说明**: 用户登出

**请求头**:
\`\`\`
Authorization: Bearer {token}
\`\`\`

### {其他端点}

## 错误代码列表

| 代码 | 消息 | 说明 |
|--------|------------|------|
| AUTH_001 | Invalid credentials | 认证信息无效 |
| AUTH_002 | Token expired | 令牌已过期 |
| VALID_001 | Validation failed | 验证错误 |

## 响应通用格式

### 成功响应
\`\`\`typescript
{
  success: true;
  data: T; // 类型根据端点变化
}
\`\`\`

### 错误响应
\`\`\`typescript
{
  success: false;
  error: {
    code: string;
    message: string;
    details?: any;
  }
}
\`\`\`
```

### database.md

```markdown
# 数据库设计(逆向生成)

## 架构概述

### 表列表
{发现的表列表}

### ER图
\`\`\`mermaid
erDiagram
    USERS {
        uuid id PK
        varchar email UK
        varchar name
        timestamp created_at
        timestamp updated_at
    }

    POSTS {
        uuid id PK
        uuid user_id FK
        varchar title
        text content
        timestamp created_at
        timestamp updated_at
    }

    USERS ||--o{ POSTS : creates
\`\`\`

## 表详情

### users 表
\`\`\`sql
{实际的CREATE TABLE语句}
\`\`\`

**列说明**:
- \`id\`: {说明}
- \`email\`: {说明}
- \`name\`: {说明}

**索引**:
- \`idx_users_email\`: email列的搜索用

### {其他表}

## 约束·关系

### 外键约束
{发现的外键约束}

### 唯一约束
{发现的唯一约束}

## 数据访问模式

### 常用查询
{从代码中发现的查询模式}

### 性能考虑事项
{发现的索引策略}
```

### interfaces.ts

```typescript
// ======================
// 实体类型定义
// ======================

export interface User {
  id: string;
  email: string;
  name: string;
  createdAt: Date;
  updatedAt: Date;
}

export interface Post {
  id: string;
  userId: string;
  title: string;
  content: string;
  createdAt: Date;
  updatedAt: Date;
  user?: User;
}

// ======================
// API类型定义
// ======================

export interface LoginRequest {
  email: string;
  password: string;
}

export interface LoginResponse {
  success: boolean;
  data: {
    token: string;
    user: User;
  };
}

export interface ApiResponse<T = any> {
  success: boolean;
  data?: T;
  error?: {
    code: string;
    message: string;
    details?: any;
  };
}

// ======================
// 组件Props类型
// ======================

export interface LoginFormProps {
  onSubmit: (data: LoginRequest) => void;
  loading?: boolean;
  error?: string;
}

// ======================
// 状态管理类型
// ======================

export interface AuthState {
  user: User | null;
  token: string | null;
  isAuthenticated: boolean;
  loading: boolean;
}

// ======================
// 配置类型
// ======================

export interface AppConfig {
  apiBaseUrl: string;
  tokenStorageKey: string;
  supportedLanguages: string[];
}
```

## 分析算法

### 1. 文件遍历·模式匹配
- 通过AST分析提取函数·类·接口
- 通过正则表达式分析配置文件
- 从目录结构推测架构

### 2. API规格自动生成
- 分析Express/NestJS路由定义
- 分析FastAPI架构定义
- 从TypeScript类型定义推测请求/响应

### 3. 数据库架构提取
- 分析迁移文件
- 分析ORM模型定义
- 分析SQL文件

## 执行命令示例

```bash
# 完整分析(生成所有设计书)
claude code rev-design

# 仅生成特定设计书
claude code rev-design --target architecture
claude code rev-design --target api
claude code rev-design --target database

# 分析特定目录
claude code rev-design --path ./backend

# 指定输出格式
claude code rev-design --format markdown,openapi
```

## 执行后确认

- 显示生成的设计书文件列表
- 显示提取的API数、表数、类型定义数等统计信息
- 提示缺失的设计要素和推荐改进点
- 建议下一个逆向工程步骤(需求定义生成等)
