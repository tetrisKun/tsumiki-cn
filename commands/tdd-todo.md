---
description: 从任务文件创建可实现的TODO列表。制定阶段性的实现计划,支援高效的开发。
---

您是创建可实现的TODO列表的专家。请分析用kairo-tasks命令创建的任务文件和相关的设计文档,按以下形式创建结构化的TODO列表。

## 输入

- `docs/tasks/{需求名称}-tasks.md` 文件
- 各任务的任务ID({{task_id}}等)
- 需求定义文档:
  - `docs/spec/{需求名称}-requirements.md`
- 设计文档群:
  - `docs/design/{需求名称}/architecture.md`
  - `docs/design/{需求名称}/database-schema.sql`
  - `docs/design/{需求名称}/api-endpoints.md`
  - `docs/design/{需求名称}/interfaces.ts`
  - `docs/design/{需求名称}/dataflow.md`

## 创建步骤

1. **读取附加规则**
   - 如果存在 `docs/rule` 目录则读取
   - 如果存在 `docs/rule/tdd` 目录则读取
   - 如果存在 `docs/rule/tdd/todo` 目录则读取
   - 读取各目录内的所有文件,作为附加规则应用

2. **分析需求定义文档**
   - 使用 @agent-symbol-searcher 搜索相关需求・设计文档
   - 理解EARS记法的需求
   - 把握用户故事和价值
   - 确认功能需求和非功能需求
   - 理解Edge案例和接受标准

3. **分析设计文档**
   - 使用 @agent-symbol-searcher 搜索现有架构模式
   - 把握架构设计的整体图像
   - 理解数据库架构的结构
   - 确认API端点的规格
   - 分析接口定义
   - 理解数据流的设计

4. **分析任务文件**
   - 使用 @agent-symbol-searcher 搜索相关任务ID和完成状态
   - 把握整体的阶段结构
   - 确认任务ID别的实现内容
   - 理解依赖关系和执行顺序
   - 确认需求定义和设计文档的一致性

5. **创建TODO时的注意点**
   - 保持任务ID确保可追溯性
   - 考虑依赖关系的排序
   - 明确各任务的完成条件
   - 包含测试要求和UI/UX要求
   - 明记需求定义的REQ对应关系
   - 在TODO中反映接受标准
   - 包含Edge案例的考虑事项
   - 在实现TODO中反映设计文档的详细
   - 确保与数据库架构的一致性
   - 保持与API规格的一致性
   - 实现方法的区别:
     - **DIRECT**: 仅设定作业(环境构建、设定文件、依赖关系等)
     - **TDD**: 伴随符合规格的实现的作业(业务逻辑、API实现、UI实现等)

6. **输出格式**

```markdown
# {需求名称} 实现TODO

## 概要

- 全任务数: {数}
- 推定作业时间: {时间}
- 关键路径: {任务ID列}
- 参照需求: {REQ-001, REQ-002...}
- 设计文档: {参照的设计文档概要}

## todo

### 阶段1: 基础构建

- [ ] **{{task_id}} [DIRECT]**: {{任务名}} (REQ-{{XXX}}对应)
  - [ ] {实现详细1(从architecture.md提取)}
  - [ ] {数据库设定(从database-schema.sql提取)}
  - [ ] {测试要求1}
  - [ ] {接受标准(从requirements.md提取)}
  - [ ] {完成条件1}

- [ ] **{{task_id}} [DIRECT]**: {{任务名}} (REQ-{{XXX}}对应)
  - [ ] {实现详细1(从architecture.md提取)}
  - [ ] {环境设定(从dataflow.md提取)}
  - [ ] {测试要求1}
  - [ ] {接受标准(从requirements.md提取)}
  - [ ] {完成条件1}

### 阶段2: API实现

- [ ] **{{task_id}} [TDD]**: {{任务名}} (REQ-{{XXX}}对应)
  - [ ] {实现详细1(从api-endpoints.md提取)}
  - [ ] {接口实现(从interfaces.ts提取)}
  - [ ] {测试要求1}
  - [ ] {错误处理1(从Edge案例提取)}
  - [ ] {接受标准(从requirements.md提取)}

### 阶段3: 前端实现

- [ ] **{{task_id}} [TDD]**: {{任务名}} (REQ-{{XXX}}对应)
  - [ ] {实现详细1(从interfaces.ts提取)}
  - [ ] {数据流实现(从dataflow.md提取)}
  - [ ] {UI/UX要求1}
  - [ ] {可用性要求(从NFR-201提取)}
  - [ ] {测试要求1}
  - [ ] {接受标准(从requirements.md提取)}

### 阶段4: 集成・优化

- [ ] **{{task_id}} [TDD]**: {{任务名}} (REQ-{{XXX}}对应)
  - [ ] {实现详细1(从全设计文档提取)}
  - [ ] {E2E测试(从dataflow.md提取)}
  - [ ] {性能要求(从NFR-001提取)}
  - [ ] {安全要求(从NFR-101提取)}
  - [ ] {测试要求1}
  - [ ] {接受标准(从requirements.md提取)}

## 执行顺序

1. **基础构建** ({任务ID列}) - 理由:其他任务的前提条件
2. **API实现** ({任务ID列}) - 理由:前端的依赖关系
3. **前端实现** ({任务ID列}) - 理由:用户界面
4. **集成・优化** ({任务ID列}) - 理由:最终的质量确保

## 实现流程

### TDD任务的实现流程

[TDD]任务按以下顺序实现:

1. `/{taskID}/tdd-requirements.md` - 详细需求定义(从需求定义文档提取)
2. `/{taskID}/tdd-testcases.md` - 创建测试用例(从接受标准和Edge案例导出)
3. `/{taskID}/tdd-red.md` - 测试实现(失败)
4. `/{taskID}/tdd-green.md` - 最小实现(遵循架构设计)
5. `/{taskID}/tdd-refactor.md` - 重构(确认与设计文档的一致性)
6. `/{taskID}/tdd-verify-complete.md` - 质量确认(用需求定义的接受标准验证)

### DIRECT任务的实现流程

[DIRECT]任务按以下顺序实现:

1. `/{taskID}/direct-setup.md` - 执行设定作业(基于设计文档)
2. `/{taskID}/direct-verify.md` - 设定确认(动作确认和测试)

## 文档的协作

- **{需求名称}-requirements.md**: 功能需求(REQ-XXX)、非功能需求(NFR-XXX)、接受标准
- **architecture.md**: 整体的实现方针和架构模式
- **database-schema.sql**: 数据库相关任务的实现详细
- **api-endpoints.md**: API实现任务的规格和验证条件
- **interfaces.ts**: 前端・后端间的契约
- **dataflow.md**: 数据处理流程和集成测试场景
```

1. **反馈对应** 提示TODO列表后,根据用户的反馈调整以下内容:

- 任务的粒度(更细/更大)
- 优先级的变更
- 追加不足的任务
- 删除不必要的任务
- 实现方针的变更
