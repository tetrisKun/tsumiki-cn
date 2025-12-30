---
description: 执行DIRECT任务的设置工作。根据设计文档进行环境构建、创建配置文件、安装依赖关系等。
allowed-tools: Read, Glob, Grep, Task, Write, Edit, TodoWrite
argument-hint: [需求名称] [TASK-ID]
---

# direct-setup

## 目的

执行DIRECT任务的设置工作。根据设计文档进行环境构建、创建配置文件、安装依赖关系等。

## 前提条件

- 提供了任务ID
- 存在相关设计文档
- 已准备必要的权限和环境

## 实行内容

1. **读取附加规则**
   - 如果存在 `docs/rule` 目录则读取
   - 如果存在 `docs/rule/direct` 目录则读取
   - 如果存在 `docs/rule/direct/setup` 目录则读取
   - 读取各目录内的所有文件，作为附加规则应用

2. **读取技术栈定义**
   - 如果存在 `docs/tech-stack.md` 则读取
   - 如果不存在则从 `CLAUDE.md` 读取技术栈部分
   - 如果两者都不存在则使用 `.claude/commands/tech-stack.md` 的默认定义

3. **确认设计文档**
   - 根据读取的技术栈定义特定关联文件
   - 使用 @agent-symbol-searcher 搜索相关设计文档和配置模式，并用Read工具读取找到的文件
   - 用Read工具读取 `docs/design/{需求名称}/architecture.md`
   - 用Read工具读取 `docs/design/{需求名称}/database-schema.sql`
   - 用Read工具读取其他相关设计文档

4. **执行设置工作**
   - 使用 @agent-symbol-searcher 搜索现有配置文件和环境变量，并用Read工具读取找到的文件
   - 设置环境变量
   - 创建・更新配置文件
   - 安装依赖关系
   - 初始化数据库
   - 配置服务启动设置
   - 设置权限

5. **创建工作记录**
   - 记录执行的命令
   - 记录修改的配置
   - 记录遇到的问题和解决方法

## 输出目录

工作记录将创建在 `docs/implements/{需求名称}/{TASK-ID}/` 目录中，文件如下：
- `setup-report.md`: 设置工作执行记录

## README.md中的记录

开发环境的构建和运维相关的重要信息**必须也记录在README.md中**：

### 需要记录的内容
- 新增环境变量的设置方法
- 添加的依赖关系及其安装方法
- 数据库初始化步骤
- 新增服务的启动方法
- 配置文件的位置和说明
- 故障排查信息（错误及解决方法）

### 记录方法
1. 如果需要追加到现有章节，找到适当位置追加
2. 如果需要新增章节，在适当位置创建新章节
3. 命令示例必须用代码块记载
4. 重要的注意事项要清楚地记载

### 记录示例

#### 添加到设置章节
```markdown
### 6. {新服务名}的设置
\`\`\`bash
cd {目录}
{安装命令}
{启动命令}
\`\`\`
```

#### 添加到开发命令章节
```markdown
### {服务名}
\`\`\`bash
# {命令说明}
{命令}
\`\`\`
```

#### 添加到故障排查章节
```markdown
### {问题概述}

\`\`\`bash
# 问题确认方法
{确认命令}

# 解决方法
{解决命令}
\`\`\`
```

## 输出格式示例

<setup_report_format>
```markdown
# {TASK-ID} 设置工作执行

## 工作概述

- **任务ID**: {TASK-ID}
- **工作内容**: {设置工作的概述}
- **执行日时**: {执行日时}
- **执行者**: {执行者}

## 设计文档参考

- **参考文档**: {参考的设计文档列表}
- **相关需求**: {REQ-XXX, REQ-YYY}

## 执行的工作

### 1. 环境变量设置

```bash
# 执行的命令
export NODE_ENV=development
export DATABASE_URL=postgresql://localhost:5432/mydb
```

**设置内容**:

- NODE_ENV: 设置为开发环境
- DATABASE_URL: PostgreSQL数据库的URL

### 2. 创建配置文件

**创建的文件**: `config/database.json`

```json
{
  "development": {
    "host": "localhost",
    "port": 5432,
    "database": "mydb"
  }
}
```

### 3. 安装依赖关系

```bash
# 执行的命令
npm install express pg
```

**安装内容**:

- express: Web框架
- pg: PostgreSQL客户端

### 4. 初始化数据库

```bash
# 执行的命令
createdb mydb
psql -d mydb -f database-schema.sql
```

**执行内容**:

- 创建数据库
- 应用Schema

## 工作结果

- [x] 环境变量设置完成
- [x] 配置文件创建完成
- [x] 依赖关系安装完成
- [x] 数据库初始化完成
- [x] 服务启动设置完成

## 遇到的问题和解决方法

### 问题1: {问题的概述}

- **发生情况**: {问题发生的情况}
- **错误消息**: {错误消息}
- **解决方法**: {解决方法}

## 下一步

- 执行 `/jimu:direct-verify` 来确认设置
- 根据需要调整设置
```
</setup_report_format>

## 执行后的确认
- 确认 `docs/implements/{需求名称}/{TASK-ID}/setup-report.md` 文件已创建
- 确认设置已正确应用
- 确认为下一步（direct-verify）的准备已完成

## 目录创建

执行前请创建必要的目录：
```bash
mkdir -p docs/implements/{需求名称}/{TASK-ID}
```
