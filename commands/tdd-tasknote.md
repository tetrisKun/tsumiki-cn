---
description: 收集TDD开发的上下文信息并整理到笔记中。整理技术栈、附加规则、相关文件的信息。
allowed-tools: Read, Glob, Grep, Task, Write, TodoWrite, Edit
argument-hint: [需求名称] [TASK-ID]
---
在TDD开发前收集上下文信息,将开发所需的信息整理到笔记文件中。

# context

输出目录="./docs/implements"
功能名={{feature_name}}
任务ID={{task_id}}
需求名称={{requirement_name}}
收集信息=[]
note文件="./docs/implements/{需求名称}/{{task_id}}/note.md"

# step

- 如果没有 $ARGUMENTS,则说"请在参数中指定需求名称和TASK-ID(例: 用户认证功能 TASK-0001)"后结束
- 如果已经有 {{note文件}},则向用户确认是否可以更新。
  - 如果用户同意则重新创建。
  - 如果用户不更新则结束
- 收集开发上下文:
   **读取附加规则**
   - 如果存在 `AGENTS.md` 文件则读取
   - 如果存在 `./docs/rule` 目录则读取
   - 如果存在 `./docs/rule/tdd` 目录则读取
   - 如果存在 `./docs/rule/tdd/green` 目录则读取
   - 读取各目录内的所有文件,作为附加规则应用

  **使用 @agent-symbol-searcher 搜索实现相关信息,并读取找到的文件**
    - `./docs/spec/{需求名称}-requirements.md`: 集成功能需求和相关文档
    - `./docs/spec/{需求名称}-user-stories.md`: 详细的用户故事
    - `./docs/spec/{需求名称}-acceptance-criteria.md`: 接受标准和测试项目
    - `./docs/spec/{需求名称}-*.md`: 接受标准和测试项目
   - 搜索现有的类似功能或工具函数,使用Read工具读取相应文件
   - 识别实现模式或架构指南,使用Read工具读取设计文档
   - 确认依赖关系或导入路径,使用Read工具读取相关文件

  **直接读取相关文件**
   - `./docs/implements/{需求名称}/{{task_id}}/*.md` - 读取与task相关的所有文件
   - 根据需要读取相关的设计文档或任务文件

- 整理收集的信息并保存到 {{note文件}}
  - **重要**: 笔记文件内的所有文件路径以项目根目录为基准用相对路径记载(不使用绝对路径)

# rules

## 文件名规则

### 输出文件的路径格式
- `./docs/implements/{需求名称}/{{task_id}}/note.md`
- 例: `docs/implements/user-auth/task-0001/note.md`

### 文件名命名规则
- 将功能名转换为简洁的英语
- 使用短横线命名法(kebab-case)
- 控制在50个字符左右
- 例:
  - "用户认证功能" → "user-auth"
  - "数据导出功能" → "data-export"
  - "密码重置功能" → "password-reset"

## 文件路径记载规则(必需)

- **使用以项目根目录为基准的相对路径**
- **绝对不记载绝对路径(全路径)**
- **目录路径也用相对路径记载**
- 例:
  - ❌ `/Users/username/projects/myapp/src/utils/helper.ts`
  - ❌ `/Users/username/projects/myapp/docs/spec/`
  - ✅ `src/utils/helper.ts`
  - ✅ `docs/spec/`
  - ✅ `backend/app/main.py`
  - ✅ `frontend/src/components/`

# output_format

## 笔记构成

### 1. 技术栈
- 使用技术・框架
- 架构模式
- 参照元: [相对文件路径]

### 2. 开发规则
- 项目固有的规则
- 编码规范
- 参照元: [相对文件路径列表]

### 3. 相关实现
- 类似功能的实现例
- 参考模式
- 参照元: [相对文件路径列表]

### 4. 设计文档
- 架构・API规格
- 数据模型
- 参照元: [相对文件路径列表]

### 5. 注意事项
- 技术限制
- 安全・性能要求
- 参照元: [相对文件路径]

---

## 🚨 重要的记载规则

**所有文件路径・目录路径以项目根目录为基准用相对路径记载**

### 相对路径记载示例

✅ **正确的记载例**:
```
- 参照元: docs/tech-stack.md
- 参照元: backend/app/main.py
- 参照元: frontend/src/components/TodoList.tsx
- 目录: docs/spec/personal-todo-app/
```

❌ **错误的记载例(绝对避免)**:
```
- 参照元: /Users/username/projects/ai/test02/docs/tech-stack.md
- 参照元: /Users/username/projects/ai/test02/backend/app/main.py
- 目录: /Users/username/projects/ai/test02/docs/spec/
```

### 实现时的注意

1. **使用 Read 工具读取时**: 使用绝对路径读取
2. **记载到笔记文件时**: 必须转换为相对路径记载
3. **目录路径也同样**: `/Users/.../docs/spec/` → `docs/spec/`
