# 4.4 错误处理和调试

## 学习目标

本章将掌握AITDD开发中发生的各种错误的应对方法和有效的调试手法:

- 理解AI生成代码特有的错误模式
- 识别和修正由提示词引起的错误
- 建立高效的调试流程
- 切换到手动实现的判断及其实践
- 错误预防的最佳实践

## 错误分类与应对策略

AITDD开发中会发生与传统开发不同类型的错误。正确分类这些错误并应用相应的应对方法非常重要。

### 错误的基本分类

**1. 提示词引起的错误**
- 由于指示的模糊性
- 由于需求的不明确
- 由于上下文不足

**2. AI实现引起的错误**
- AI生成代码的bug
- 与现有代码的一致性问题
- 性能问题

**3. 集成引起的错误**
- 多功能集成时的问题
- 接口不一致
- 依赖关系问题

**4. 传统型错误**
- 一般编程错误
- 环境配置问题
- 外部依赖问题

## 实用调试流程

### 步骤1: 全面收集错误信息

发生错误时,首先系统地收集信息。

**需要收集的信息**:
```markdown
## 错误信息收集清单

### 基本信息
- [ ] 错误消息(完整版)
- [ ] 堆栈跟踪
- [ ] 发生时机
- [ ] 执行环境

### 上下文信息
- [ ] 执行的命令
- [ ] 输入数据
- [ ] 期望的行为
- [ ] 实际的行为

### AI相关信息
- [ ] 使用的提示词
- [ ] AI生成代码的相关部分
- [ ] 相关的现有代码

### 执行环境
- [ ] OS/浏览器信息
- [ ] Node.js/TypeScript版本
- [ ] 依赖包的状态
```

**信息收集的实例**:
```
错误发生时的记录示例:

错误消息:
TypeError: Cannot read property 'map' of undefined
    at getAllTasks (TaskController.ts:15)
    at Router.handle (express/lib/router/layer.js:95)

发生时机:
GET /api/tasks 端点的测试执行时

输入数据:
GET http://localhost:3000/api/tasks

期望行为:
返回包含空数组的响应

实际行为:
对undefined调用map时发生错误

使用的提示词:
"请实现调用TaskManager类的getAllTasks方法并返回响应的API端点"
```

### 步骤2: 使用AI进行原因分析

将收集的所有信息提供给AI,请求进行错误原因分析。

**有效的分析提示词**:
```
请分析以下错误的原因:

## 错误信息
[粘贴收集的所有错误信息]

## 相关代码
[粘贴发生错误的全部源代码]

## 分析要求
1. 错误的直接原因
2. 根本原因(设计/实现问题)
3. 提示词引起的可能性
4. 修正方针的建议
5. 类似错误的预防措施

请尽可能具体和实用地进行分析。
```

**AI的分析示例**:
```
## 错误分析结果

### 直接原因
TaskController.getAllTasks方法中,taskManager.getAllTasks()返回undefined

### 根本原因
1. TaskManager类的实例化存在问题
2. getAllTasks方法的实现中任务数组未初始化

### 提示词引起的可能性
"使用现有的TaskManager类"这一指示很模糊,
可能在没有确认实际类定义的情况下就进行了实现

### 修正方针
1. 确认TaskManager类的初始化
2. 验证getAllTasks方法的返回值
3. 添加null/undefined检查

### 预防措施
1. 在提示词中包含现有代码的详细信息
2. 强化返回值的类型安全性
3. 在单元测试中添加null/undefined情况
```

### 步骤3: 阶段性调查和假设验证

基于AI的分析,逐步调查问题。

**调查流程示例**:
```typescript
// 1. 确认TaskManager类的状态
describe('TaskManager调试', () => {
  test('TaskManager实例化确认', () => {
    const manager = new TaskManager();
    console.log('TaskManager instance:', manager);
    expect(manager).toBeDefined();
  });

  test('getAllTasks返回值确认', () => {
    const manager = new TaskManager();
    const result = manager.getAllTasks();
    console.log('getAllTasks result:', result);
    console.log('result type:', typeof result);
    expect(result).toBeDefined();
  });

  test('任务数组初始状态确认', () => {
    const manager = new TaskManager();
    const tasks = manager.getAllTasks();
    expect(Array.isArray(tasks)).toBe(true);
    console.log('Initial tasks array:', tasks);
  });
});
```

**调试执行**:
```bash
npm test -- --verbose TaskManager调试
```

### 步骤4: 与AI协作实现修正

将调查结果反馈给AI,请求修正实现。

**修正请求提示词**:
```
调试调查的结果,发现了以下情况:

## 调查结果
[粘贴调试测试的输出结果]

## 发现的问题
1. TaskManager类的数组初始化不适当
2. getAllTasks方法的返回值为undefined

## 修正要求
请实现满足以下条件的修正:
1. 数组能够正确初始化
2. 确保类型安全性
3. 添加null/undefined检查
4. 现有测试能够通过
5. 同时添加新的测试用例

也请提供修正后的代码和修正理由的说明。
```

## 识别和修正提示词引起的错误

### 提示词问题的判断标准

**基于频率的判定**:
```
相同错误模式的发生情况:
- 第1次: 作为实现错误处理
- 第2次: 考虑提示词问题的可能性
- 第3次: 必须修正提示词
```

**典型提示词问题的征兆**:
- 相同要求产生完全不同的实现
- 与期望差异很大的输出
- 意外修改现有代码
- 超出指示范围的实现

### 提示词问题的修正流程

#### 1. 提示词诊断

**请求AI进行诊断**:
```
请分析以下提示词并指出问题点:

## 使用的提示词
[存在问题的提示词]

## 期望的结果
[期望的输出]

## 实际的结果
[实际的输出]

## 诊断要求
1. 提示词的模糊部分
2. 缺少的信息
3. 可能引起误解的表达
4. 改进建议
```

#### 2. 创建提示词改进方案

**改进提示词的示例**:
```
改进前(存在问题的提示词):
"请使用TaskManager类创建API端点"

改进后:
"请使用以下现有的TaskManager类实现GET /api/tasks端点。

现有代码:
[TaskManager类的完整代码]

要求:
1. 完全不修改现有代码
2. 使用Express.js的Router
3. 响应格式: { success: boolean, data: Task[] }
4. 包含错误处理
5. 确保TypeScript的类型安全性

输出格式:
- routes/tasks.ts 文件
- controllers/TaskController.ts 文件
- 对应的测试文件"
```

#### 3. 验证改进的提示词

**验证流程**:
```typescript
describe('提示词改进验证', () => {
  test('改进前提示词的问题重现', async () => {
    // 用存在问题的提示词请求实现
    // 确认期望的问题发生
  });

  test('改进后提示词的解决确认', async () => {
    // 用改进的提示词请求实现
    // 确认问题已解决
  });

  test('改进提示词的副作用确认', async () => {
    // 确认改进没有导致新问题
  });
});
```

## 切换到手动实现的判断

### 切换时机的判断

**实现开始前的判断**:
```markdown
## 手动实现考虑清单

### 实现意象的有无
- [ ] 能想到具体的实现步骤
- [ ] 使用的技术/库很明确
- [ ] 能预测实现的陷阱

### 技术复杂性
- [ ] 需要性能优化
- [ ] 需要复杂的算法
- [ ] 需要深厚的领域知识

### 向AI解释的难度
- [ ] 无法明确地用语言表达需求
- [ ] 提示词变得异常长
- [ ] 难以解释前提知识
```

**实现中的切换判断**:
```
切换判断标准:
1. 相同错误发生3次以上
2. 修正提示词也无法解决
3. 调试时间超过实现时间
4. AI生成代码的质量不一致
```

### 有效的手动实现方法

#### 阶段性AI并用策略

**不是完全手动,而是部分AI活用**:
```typescript
// 1. 复杂逻辑部分手动实现
const complexAlgorithm = (data: any[]) => {
  // 手动实现(难以向AI解释的部分)
  let result = [];
  for (let i = 0; i < data.length; i++) {
    // 复杂的计算逻辑
  }
  return result;
};

// 2. 定型代码委托给AI
const generateApiResponse = (data: any) => {
  // 这部分可以用AI生成
  return {
    success: true,
    data,
    meta: {
      timestamp: new Date().toISOString(),
      count: Array.isArray(data) ? data.length : 1
    }
  };
};

// 3. 组合成最终实现
export const processData = async (inputData: any[]) => {
  try {
    const processedData = complexAlgorithm(inputData); // 手动部分
    return generateApiResponse(processedData); // AI生成部分
  } catch (error) {
    // 错误处理也可以AI支援
  }
};
```

#### 手动实现中的AI支援活用

**1. 活用IDE补全功能**:
```typescript
// 积极使用VS Code等的AI补全
const taskManager = new TaskManager();
// ↑这里可以利用AI补全的部分
```

**2. 部分代码生成请求**:
```
有明确实现意象部分的AI请求示例:

"请根据以下类型定义创建验证函数:

interface TaskInput {
  title: string;
  description?: string;
}

要求:
- title必须为1-100字符
- description可选为0-500字符
- 无效时提供具体的错误消息
- 作为TypeScript的类型守卫功能
```

#### 品质流程的持续

**手动实现也必须进行Validation步骤**:
```
手动实现的Validation检查项目:
1. 与规格要求的一致性
2. 确保类型安全性
3. 错误处理的适当性
4. 确认测试覆盖率
5. 性能的合理性
6. 代码的可读性/可维护性
```

## 错误预防的最佳实践

### 提示词设计的改进

**1. 明确化上下文**:
```
良好的提示词示例:

"请为以下现有系统添加新功能:

现有代码:
[粘贴所有相关代码]

新功能要求:
[具体明确的要求]

约束条件:
- 禁止修改现有代码
- 确保TypeScript类型安全性
- 必须进行错误处理

期望的输出:
- 实现代码
- 测试代码
- 使用示例
- 注意事项"
```

**2. 阶段性实现指示**:
```
复杂功能的阶段性实现:

"请阶段性实现以下内容:

步骤1: 接口设计
步骤2: 基本实现
步骤3: 错误处理
步骤4: 创建测试

请在每个步骤确认后再推进。"
```

### 测试策略的强化

**1. AI生成代码特有的测试**:
```typescript
describe('AI生成代码验证测试', () => {
  test('确认没有意外修改现有代码', () => {
    // 测试重要的现有函数没有被修改
    const originalFunction = require('./legacy/original-module');
    expect(originalFunction.criticalMethod).toBeDefined();
    expect(typeof originalFunction.criticalMethod).toBe('function');
  });

  test('确认推测实现的范围', () => {
    // 确认AI推测实现的部分在要求范围内
    const implementation = new FeatureImplementation();
    expect(implementation.getImplementedFeatures())
      .toEqual(expect.arrayContaining(REQUIRED_FEATURES));
  });

  test('确认类型安全性', () => {
    // 确认TypeScript的类型检查正常工作
    // 测试不发生编译错误
  });
});
```

**2. 集成测试的强化**:
```typescript
describe('功能集成测试', () => {
  test('3功能集成的回归确认', async () => {
    // 测试3个功能组合后也能正常工作
    const feature1 = await executeFeature1();
    const feature2 = await executeFeature2(feature1.result);
    const feature3 = await executeFeature3(feature2.result);

    expect(feature3.result).toMatchExpectedOutput();
  });
});
```

### 持续改进流程

**1. 错误模式的积累**:
```markdown
## 错误模式管理

### 发生频率高的错误
1. undefined/null访问错误
   - 原因: AI生成代码中初始化不足
   - 对策: 明确的初始化指示

2. 类型不一致错误
   - 原因: 提示词中类型信息不足
   - 对策: 明确提供类型定义

3. 现有代码修改错误
   - 原因: "禁止修改"指示不彻底
   - 对策: 具体的约束指示
```

**2. 提示词模板的改进**:
```
改进的提示词模板:

### 基本模板
```
功能: [功能名]
实现对象: [具体的实现内容]

现有代码:
[所有相关代码]

要求:
[明确具体的要求]

约束:
- 禁止修改现有代码
- [其他约束]

输出要求:
- [期望的输出格式]

质量要求:
- TypeScript类型安全性
- 错误处理
- 包含测试代码
```

## 实用调试技术

### 基于日志的调试

**有效的日志输出**:
```typescript
// 调试用日志辅助类
class DebugLogger {
  static log(context: string, data: any) {
    if (process.env.NODE_ENV === 'development') {
      console.log(`[${context}]`, {
        timestamp: new Date().toISOString(),
        data: JSON.stringify(data, null, 2)
      });
    }
  }

  static error(context: string, error: any) {
    console.error(`[ERROR:${context}]`, {
      message: error.message,
      stack: error.stack,
      timestamp: new Date().toISOString()
    });
  }
}

// 使用示例
export const taskController = {
  getAllTasks: async (req, res, next) => {
    try {
      DebugLogger.log('TaskController.getAllTasks', 'Starting execution');

      const tasks = taskManager.getAllTasks();
      DebugLogger.log('TaskController.getAllTasks', { tasksCount: tasks?.length });

      const response = generateResponse(tasks);
      DebugLogger.log('TaskController.getAllTasks', { response });

      res.json(response);
    } catch (error) {
      DebugLogger.error('TaskController.getAllTasks', error);
      next(error);
    }
  }
};
```

### 测试驱动调试

**从失败测试逆推**:
```typescript
describe('bug重现测试', () => {
  test('特定条件下的null错误重现', () => {
    // 确定发生bug的最小条件
    const manager = new TaskManager();
    const result = manager.getAllTasks();

    // 此时应该发生错误
    expect(() => result.map(x => x.id)).toThrow();
  });

  test('修正后的行为确认', () => {
    // 测试修正后期望的行为
    const manager = new TaskManager();
    const result = manager.getAllTasks();

    expect(Array.isArray(result)).toBe(true);
    expect(() => result.map(x => x.id)).not.toThrow();
  });
});
```

## 总结

本章学习了AITDD开发中全面的错误处理和调试手法:

**主要学习成果**:
- 错误的适当分类和应对方法
- 利用AI的高效调试流程
- 识别和修正提示词引起的错误
- 切换到手动实现的判断和AI并用策略
- 错误预防的最佳实践

**实践技能**:
- 全面的信息收集技法
- 与AI协作的错误分析
- 阶段性问题解决方法
- 质量管理的持续改进

**为下一章做准备**:
通过这些技能,为学习更高级的AITDD手法和优化技术做好了准备。下面将进入提示词设计和AI活用的优化。

通过AITDD的实践练习系列,从基础到应用掌握了一系列技术。在下一部分,将进一步完善这些技术,学习在实际生产环境中活用的高级技法。
