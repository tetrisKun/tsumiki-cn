# 4.2 CRUD操作实现示例

## 学习目标

本章通过更实践性的CRUD(Create, Read, Update, Delete)操作实现,掌握以下内容:

- 在多个功能集成中活用AITDD
- 数据管理逻辑的设计与实现
- 接近实际应用开发的体验
- 体验与Vibe编码的区别

## 项目概要:简单的任务管理系统

### 实现的功能

创建基于内存的简单任务管理系统:

```typescript
interface Task {
  id: string;
  title: string;
  description: string;
  completed: boolean;
  createdAt: Date;
  updatedAt: Date;
}
```

**CRUD操作**:
- **Create**: 创建新任务
- **Read**: 获取任务(全部、单个、条件搜索)
- **Update**: 更新任务
- **Delete**: 删除任务

### 技术复杂度级别

与前章的计算功能相比增加了以下复杂度:
- 数据的持久化(基于内存)
- 多个实体操作
- 验证的组合
- 错误处理的多样化

## 实践动手

### 步骤1:TODO创建和功能分解

作为AITDD的重要经验教训,我们知道**集成约3个功能是实用的极限**。因此,将功能分解为适当的粒度。

```markdown
# TODO: 任务管理系统CRUD实现

## 阶段1:基础实现
- [ ] Task接口的定义
- [ ] TaskManager类的基本结构
- [ ] 内存存储的实现

## 阶段2:基本CRUD(最多3个功能)
- [ ] Create: 创建新任务
- [ ] Read: 获取全部任务
- [ ] Read: 通过ID获取单个任务

## 阶段3:扩展CRUD(其余功能)
- [ ] Update: 更新任务
- [ ] Delete: 删除任务
- [ ] Read: 条件搜索(完成状态等)

## 技术要求
- 使用TypeScript
- 使用Jest测试
- 基于内存的数据管理
- 适当的错误处理
```

**要点**:
- 通过阶段划分控制集成的复杂性
- 各阶段可以独立测试
- 可以阶段性添加功能

### 步骤2:阶段1的实现

#### 规格创建

**给AI的提示词**:
```
请创建任务管理系统基础部分的规格。

要求:
- Task接口的定义
- TaskManager类的设计
- 基于内存的存储功能
- TypeScript的类型安全保证

请参考以下TODO:
[粘贴阶段1的TODO]
```

**生成的规格示例**:
```markdown
# 任务管理系统基础规格

## Task接口
```typescript
interface Task {
  id: string;          // UUID格式的唯一ID
  title: string;       // 任务标题(1-100字符)
  description: string; // 任务说明(0-500字符)
  completed: boolean;  // 完成状态
  createdAt: Date;     // 创建日期时间
  updatedAt: Date;     // 更新日期时间
}
```

## TaskManager类
- 基于内存的任务管理
- 任务的持久化(数组)
- ID的自动生成
- 基本验证

## 错误处理
- 任务未找到时:TaskNotFoundError
- 验证失败:ValidationError
- 不正确的ID格式:InvalidIdError
```

#### 测试用例创建

**AI提示词**:
```
请基于以下规格创建基础部分的测试用例:

[粘贴基础规格]

测试观点:
- 接口的类型检查
- TaskManager的初始化
- 内存存储的运行确认
- 错误类的定义确认
```

#### Red-Green-Refactor-Validation执行

逐步请AI实现:

1. **Red**: 确认测试失败
2. **Green**: 基础类的最小实现
3. **Refactor**: 设计改进
4. **Validation**: 基础的合理性确认

### 步骤3:阶段2的实现(基本CRUD)

#### 集成时的注意事项

作为与Vibe编码的重要区别,需要**结构化的方法**:

**AITDD方式**:
```
1. 明确的规格定义
2. 全面的测试设计
3. 阶段性实现
4. 质量检查
→ 集成容易,质量稳定
```

**Vibe编码方式(应避免)**:
```
1. 凭一时冲动请AI实现
2. 测试后补
3. 集成时发现问题
4. 手工修正
→ 3功能集成即崩溃
```

#### Create功能的实现

**规格**:
```markdown
## 任务创建功能

### 方法:createTask(taskData)
- 参数:{ title: string, description: string }
- 返回值:创建的Task
- 验证:
  - title为1-100字符必填
  - description为0-500字符
- 自动设置:id, createdAt, updatedAt, completed=false
```

**AI提示词示例**:
```
请为以下规格创建createTask方法的测试用例和实现:

[粘贴规格]

要求:
1. 全面的测试用例(正常流程、异常流程、边界值)
2. 按Red-Green-Refactor-Validation循环实现
3. 确保与现有基础代码的一致性
4. 活用TypeScript的类型安全性
```

#### Read功能的实现

**同时实现全部获取和单个获取**:

**规格**:
```markdown
## 任务获取功能

### getAllTasks(): Task[]
- 以数组返回所有任务
- 空时返回空数组
- 按创建日期时间降序排序

### getTaskById(id: string): Task
- 通过ID搜索任务
- 未找到时:TaskNotFoundError
- 无效ID格式:InvalidIdError
```

### 步骤4:阶段3的实现(扩展CRUD)

#### Update功能

**支持部分更新**:

```typescript
interface TaskUpdateData {
  title?: string;
  description?: string;
  completed?: boolean;
}

updateTask(id: string, updateData: TaskUpdateData): Task
```

#### Delete功能

**包含逻辑删除vs物理删除的考虑的实现**:

```typescript
deleteTask(id: string): boolean  // 物理删除
// 或
softDeleteTask(id: string): Task  // 逻辑删除
```

#### 条件搜索功能

**过滤功能**:

```typescript
interface TaskFilter {
  completed?: boolean;
  titleContains?: string;
  createdAfter?: Date;
}

searchTasks(filter: TaskFilter): Task[]
```

### 步骤5:集成测试和最终审查

#### 集成场景测试

假设实际用例的测试:

```typescript
describe('任务管理集成场景', () => {
  test('一般的任务管理流程', async () => {
    const manager = new TaskManager();

    // 1. 任务创建
    const task1 = manager.createTask({
      title: '项目计划',
      description: '需求定义和设计'
    });

    // 2. 任务获取·确认
    const allTasks = manager.getAllTasks();
    expect(allTasks).toHaveLength(1);

    // 3. 任务更新
    const updatedTask = manager.updateTask(task1.id, {
      completed: true
    });
    expect(updatedTask.completed).toBe(true);

    // 4. 搜索功能
    const completedTasks = manager.searchTasks({
      completed: true
    });
    expect(completedTasks).toHaveLength(1);

    // 5. 任务删除
    const deleted = manager.deleteTask(task1.id);
    expect(deleted).toBe(true);
    expect(manager.getAllTasks()).toHaveLength(0);
  });
});
```

#### 性能测试

**大量数据的运行确认**:

```typescript
describe('性能测试', () => {
  test('1000件任务操作', () => {
    const manager = new TaskManager();

    // 创建1000件
    const startTime = Date.now();
    for (let i = 0; i < 1000; i++) {
      manager.createTask({
        title: `任务${i}`,
        description: `说明${i}`
      });
    }
    const createTime = Date.now() - startTime;

    // 搜索性能
    const searchStart = Date.now();
    const results = manager.searchTasks({
      titleContains: '任务1'
    });
    const searchTime = Date.now() - searchStart;

    expect(createTime).toBeLessThan(1000); // 1秒以内
    expect(searchTime).toBeLessThan(100);  // 100ms以内
  });
});
```

## 故障排除

### 常见问题和对处方法

#### 问题1:AI生成代码的一致性

**症状**:
- 无意中修改现有代码
- 接口不一致
- 命名规范不同

**对处方法**:
```
提示词改善示例:
"请完全不要修改现有代码,仅按照以下接口添加新方法:
[明示现有接口]"
```

#### 问题2:测试质量不足

**症状**:
- 异常流程测试不足
- 边界值测试遗漏
- 没有集成测试

**对处方法**:
```
审查检查清单:
- [ ] 全面覆盖正常流程、异常流程、边界值
- [ ] 确认错误消息
- [ ] 实际用例的测试
```

#### 问题3:集成时的复杂性

**症状**:
- 3个以上功能集成崩溃
- 调试困难
- 测试不通过

**对处方法**:
```
阶段性集成方法:
1. 逐个功能完全实现
2. 测试2个功能的组合
3. 添加第3个功能时谨慎进行
4. 发生问题时分割功能
```

## AI提示词的最佳实践

### 有效的提示词设计

**1. 上下文的明确化**:
```
好的例子:
"在任务管理系统的CRUD操作中,请按照以下规格实现createTask方法。
以添加到现有Task接口和TaskManager类的形式实现,
使用基于内存的存储。

[现有代码]
[详细规格]
[期望的输出格式]"
```

**2. 约束的明示**:
```
约束示例:
- "请不要修改现有代码"
- "请最大限度活用TypeScript的类型安全性"
- "请适当实现错误处理"
- "请测试优先实现"
```

**3. 期望质量的指定**:
```
质量要求示例:
- "请以生产级别的质量实现"
- "请重视可读性和可维护性"
- "请考虑性能进行实现"
- "请包含适当的注释"
```

### 提示词模板

CRUD操作专用的提示词模板:

```
### CRUD实现提示词模板

**基本信息**:
- 功能: [Create/Read/Update/Delete]操作
- 目标实体: [实体名]
- 实现语言: TypeScript
- 测试框架: Jest

**现有上下文**:
[现有的接口·类定义]

**实现要求**:
[具体的规格]

**约束条件**:
- 禁止修改现有代码
- 最大限度活用类型安全性
- 错误处理必须

**输出要求**:
1. 测试用例(正常流程·异常流程·边界值)
2. 实现代码
3. 使用示例
4. 注意事项·限制事项
```

## 质量管理要点

### 代码审查检查清单

**功能性质量**:
- [ ] 满足所有规格要求
- [ ] 错误处理适当
- [ ] 边界值的运行正确
- [ ] 性能在允许范围内

**技术性质量**:
- [ ] TypeScript类型安全性得到保证
- [ ] 遵循命名规范
- [ ] 适当的抽象级别
- [ ] 遵守DRY原则

**测试质量**:
- [ ] 测试覆盖率充分
- [ ] 测试易于理解
- [ ] mock的使用适当
- [ ] 集成测试全面

**可维护性**:
- [ ] 代码易读
- [ ] 易于变更的结构
- [ ] 文档适当
- [ ] 考虑扩展性的设计

## 实践中的学习效果

### AITDD的优势(可以感受到的)

**开发速度**:
- 传统CRUD实现:1天~2天
- 使用AITDD:不到1小时
- **体验20~48倍的效率化**

**质量稳定性**:
- 通过测试优先的质量保证
- 重构阶段的优化
- Validation步骤的综合质量检查

**学习效果**:
- 与AI协作开发技能
- 有效的提示词设计能力
- 对质量管理的敏感度提升

### 与Vibe编码的明确区别

**集成的容易性**:
- Vibe编码:3功能集成即崩溃
- AITDD:阶段性集成稳定

**调试效率**:
- Vibe编码:重复相同问题
- AITDD:系统化的问题解决

**长期可维护性**:
- Vibe编码:事后需要修改
- AITDD:可持续开发

## 为下一章做准备

通过这次CRUD实现体验,掌握以下技能:

1. **多个功能的集成管理**
2. **阶段性功能开发**
3. **理解质量管理的重要性**
4. **AI提示词设计的实践力**

下一章将活用这些技能,在API开发这一更实践性的开发场景中开展工作。将学习AITDD如何应对外部依赖和异步处理等新的复杂性。

## 总结

通过CRUD操作的实现学习了以下内容:

**流程的重要性**:
- 结构化方法的价值
- 阶段性功能添加的效果
- 质量管理的自动化

**AI活用的诀窍**:
- 明确的上下文提供
- 适当的约束指定
- 持续的提示词改善

**实践性技能**:
- 多个功能的集成技法
- 测试驱动的设计思维
- 审查和质量管理

有了这些基础,就能在更复杂的实际开发项目中有效活用AITDD。
