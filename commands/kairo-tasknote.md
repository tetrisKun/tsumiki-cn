---
description: 收集Kairo开发的上下文信息并整理到笔记中。整理技术栈、附加规则、相关文件的信息。
allowed-tools: Read, Glob, Grep, Task, Write, TodoWrite, Bash
argument-hint: [需求名称]
---
在Kairo开发前收集上下文信息,将开发所需的信息整理到笔记文件中。

# context

输出目录="docs/spec"
需求名称={{requirement_name}}
收集信息=[]

# step

- 如果没有$ARGUMENTS,显示"请在参数中指定需求名称(例:用户认证系统)"后结束
- 向用户声明$ARGUMENTS的内容和context的内容
- 执行step2

## step2: 确认现有笔记

- 如果`{{输出目录}}/{需求名称}/note.md`已存在:
  - 显示现有文件的内容
  - 向用户确认:"存在现有的笔记文件。是否更新?(y/n)"
  - 如果用户回答"y": 执行step3
  - 如果用户回答"n": 结束
- 如果不存在: 执行step3

## step3: 收集开发上下文

### Phase 1: 收集项目基本信息

**读取附加规则**
- 如果`CLAUDE.md`文件存在则读取(技术栈·约束)
- 如果`AGENTS.md`文件存在则读取
- 如果`README.md`存在则读取
- 如果`docs/rule`目录存在则读取
- 如果`docs/rule/kairo`目录存在则读取
- 读取各目录内的所有文件,作为附加规则应用

### Phase 2: 收集现有设计文档·规范书

**搜索现有需求定义·设计书**
- `docs/spec/{需求名称}-requirements.md`: 统一功能需求
- `docs/spec/{需求名称}-user-stories.md`: 详细用户故事
- `docs/spec/{需求名称}-acceptance-criteria.md`: 验收标准
- `docs/spec/{需求名称}-*.md`: 其他相关文档
- `docs/design/*.md`: 设计文档目录
- `docs/tech-stack.md`: 技术栈
- 用Read工具读取找到的所有文件

### Phase 3: 调查现有实现(可选)

- 向用户确认:"是否需要详细分析现有代码库?(y/n)"
- 仅在必要时执行:
  - **使用@task agent-symbol-searcher搜索实现相关信息**
    - 搜索类似功能的实现示例
    - 搜索实用函数·共通模块
    - 识别实现模式和架构指南
    - 确认依赖关系和导入路径
  - 用Read工具读取找到的相关文件

### Phase 4: 收集Git信息

- 用`git status`确认当前开发状况
- 用`git log --oneline -20`确认最近的提交历史
- 确认现有分支信息

### Phase 5: 了解项目结构

- 了解目录结构(仅主要目录)
- 确认配置文件(package.json, tsconfig.json等)

- 执行step4

## step4: 整理和保存收集信息

- 按<note_template>格式整理收集的信息
- 包含以下内容:
  1. **项目概要**
  2. **技术栈**
  3. **开发规则**
  4. **现有需求定义**
  5. **现有设计文档**
  6. **相关实现**(可选)
  7. **技术约束**
  8. **注意事项**

- 使用Write工具保存到`{{输出目录}}/{需求名称}/note.md`
- 执行step5

## step5: 完成报告

- 使用TodoWrite工具更新TODO状态
  - 将当前TODO标记为"completed"
  - 在TODO内容中反映上下文收集阶段的完成
  - 在TODO中添加下一阶段"需求定义创建"

- 显示完成报告:
  - 收集的文件列表
  - 项目概要摘要
  - 创建的笔记文件路径

# rules

## 文件名规则

### 输出文件路径格式
- `docs/spec/{需求名称}/note.md`
- 例: `docs/spec/user-auth-system/note.md`

### 创建目录
- 如果`docs/spec/{需求名称}/`目录不存在则自动创建
- 根据需要也创建父目录

### 文件名命名规则
- 将需求名称转换为简洁的英语
- 使用kebab-case(短横线分隔)
- 控制在最多50字符左右
- 例:
  - "用户认证系统" → "user-auth-system"
  - "数据导出功能" → "data-export"
  - "密码重置" → "password-reset"

## 文件路径记载规则

- **使用基于项目根目录的相对路径**
- 不记载完整路径(绝对路径)
- 例:
  - ❌ `/Users/username/projects/myapp/src/utils/helper.ts`
  - ✅ `src/utils/helper.ts`

## 信息收集的优先级

1. **必需**: CLAUDE.md, README.md, 现有需求定义·设计书
2. **推荐**: 附加规则、配置文件、Git信息
3. **可选**: 现有实现的详细分析

## TODO更新模式

```
- 将当前TODO标记为"completed"
- 在TODO内容中反映上下文收集阶段的完成
- 在TODO中添加下一阶段"需求定义创建"
```

# info

<note_template>
# {需求名称} 开发上下文笔记

## 创建日期时间
{创建日期时间}

## 项目概要

### 项目名称
{项目名称}

### 项目的目的
{项目的目的·概要}

**参照源**: {README.md 或 CLAUDE.md 的路径}

## 技术栈

### 使用技术·框架
- **语言**: {编程语言}
- **框架**: {使用框架}
- **运行时**: {Node.js, Python等}
- **包管理器**: {npm, yarn, pip等}

### 架构模式
- **架构风格**: {MVC, 清洁架构, 微服务等}
- **设计模式**: {使用的设计模式}
- **目录结构**: {项目的结构}

**参照源**:
- [CLAUDE.md](CLAUDE.md)
- [architecture.md](docs/design/architecture.md)

## 开发规则

### 项目特有规则
{项目特有的开发规则}

### 编码规约
- **命名规则**: {命名规则的详细信息}
- **类型检查**: {TypeScript strict mode等}
- **注释**: {注释规则}
- **格式**: {Prettier, ESLint等的设置}

### 测试要求
- **测试框架**: {Jest, Pytest等}
- **覆盖率要求**: {最低覆盖率}
- **测试模式**: {AAA, Given-When-Then等}

**参照源**:
- [AGENTS.md](AGENTS.md)
- [docs/rule/](docs/rule/)
- [docs/rule/kairo/](docs/rule/kairo/)

## 现有需求定义

### 需求定义书
{现有需求定义的摘要}

**参照源**:
- [requirements.md](docs/spec/{需求名称}-requirements.md)
- [user-stories.md](docs/spec/{需求名称}-user-stories.md)
- [acceptance-criteria.md](docs/spec/{需求名称}-acceptance-criteria.md)

### 主要功能需求(EARS记法)
- REQ-001: {需求概要}
- REQ-002: {需求概要}
- ...

### 主要非功能需求
- NFR-001: {性能需求}
- NFR-101: {安全需求}
- ...

## 现有设计文档

### 架构设计
{架构摘要}

**参照源**: [architecture.md](docs/design/architecture.md)

### 数据流
{数据流摘要}

**参照源**: [dataflow.md](docs/design/dataflow.md)

### TypeScript类型定义
{主要类型定义的摘要}

**参照源**: [interfaces.ts](docs/design/interfaces.ts)

### 数据库设计
{数据库模式摘要}

**参照源**: [database-schema.sql](docs/design/database-schema.sql)

### API规范
{API端点摘要}

**参照源**: [api-endpoints.md](docs/design/api-endpoints.md)

## 相关实现

### 类似功能的实现示例
{现有类似实现的参照}

**参照源**:
- [{文件路径1}]({文件路径1})
- [{文件路径2}]({文件路径2})

### 参考模式
{实现模式摘要}

### 共通模块·实用程序
{可用的共通功能}

**参照源**:
- [{实用程序文件1}]({实用程序文件1})
- [{实用程序文件2}]({实用程序文件2})

### 依赖关系·导入路径
{重要依赖关系的信息}

## 技术约束

### 性能约束
- {性能需求详细信息}

### 安全约束
- {安全需求详细信息}

### 兼容性约束
- {浏览器兼容性、版本约束等}

### 数据约束
- {数据大小、格式等的约束}

**参照源**: [CLAUDE.md](CLAUDE.md)

## 注意事项

### 开发时的注意点
- {开发时应注意的事项}

### 部署·运营时的注意点
- {部署·运营时的注意事项}

### 安全方面的注意点
- {安全相关注意事项}

### 性能方面的注意点
- {性能相关注意事项}

## Git信息

### 当前分支
{当前分支名}

### 最近的提交
{最近提交历史(摘录)}

### 开发状况
{当前开发状况摘要}

## 收集的文件列表

### 项目基本信息
- [CLAUDE.md](CLAUDE.md)
- [README.md](README.md)
- [AGENTS.md](AGENTS.md)

### 附加规则
- [docs/rule/](docs/rule/)
- [docs/rule/kairo/](docs/rule/kairo/)

### 需求定义·规范书
- [docs/spec/{需求名称}-requirements.md](docs/spec/{需求名称}-requirements.md)
- [docs/spec/{需求名称}-user-stories.md](docs/spec/{需求名称}-user-stories.md)
- [docs/spec/{需求名称}-acceptance-criteria.md](docs/spec/{需求名称}-acceptance-criteria.md)

### 设计文档
- [docs/design/architecture.md](docs/design/architecture.md)
- [docs/design/dataflow.md](docs/design/dataflow.md)
- [docs/design/interfaces.ts](docs/design/interfaces.ts)
- [docs/design/database-schema.sql](docs/design/database-schema.sql)
- [docs/design/api-endpoints.md](docs/design/api-endpoints.md)

### 相关实现(可选)
- [{实现文件1}]({实现文件1})
- [{实现文件2}]({实现文件2})

---

**注意**: 所有文件路径都以项目根目录的相对路径记载。
</note_template>
