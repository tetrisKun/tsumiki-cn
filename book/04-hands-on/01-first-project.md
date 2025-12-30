# 4.1 第一个AITDD项目

## 学习目标

本章通过可以体验AITDD基本流程的首次项目,掌握以下内容:

- 理解AITDD流程的整体流程
- 实践从TODO创建到实现完成的一系列步骤
- 体验AI支持与人工审查的适当平衡
- 通过小规模功能感受AITDD的效果

## 项目选择标准

### 适合第一个项目的特征

**实现范围**:
- 30分钟至1小时左右可完成的小规模功能
- 具有单一职责的简单处理
- 外部依赖少且独立性高的功能

**技术复杂度**:
- 使用熟悉的技术栈
- 可以想象实现方法的内容
- 调试相对简单的处理

**学习效果**:
- 可以体验AITDD的各个步骤
- 容易获得成功体验的内容
- 可以后续扩展的基础功能

### 推荐项目示例

**1. 简单的计算功能**
```
例:含税价格计算器
- 输入:商品价格、税率
- 输出:含税价格、税额
- 处理:基本数值计算
```

**2. 数据转换功能**
```
例:CSV数据格式转换
- 输入:CSV字符串
- 输出:JSON格式数据
- 处理:解析和转换逻辑
```

**3. 验证功能**
```
例:邮箱地址验证
- 输入:字符串
- 输出:验证结果(布尔值)
- 处理:正则表达式的格式检查
```

## 实践动手:含税价格计算器

在这个例子中,通过含税价格计算器的实现来学习AITDD流程。

### 步骤1:TODO创建

在项目内创建todo.md文件,明确要实现的功能。

```markdown
# TODO: 含税价格计算器的实现

## 实现的功能
- [ ] 从商品价格和税率计算含税价格的函数
- [ ] 计算税额的函数
- [ ] 输入值验证
- [ ] 错误处理(负值、null值等)

## 完成标准
- 正常值的计算正确运行
- 对异常值能够进行适当的错误处理
- 达到测试覆盖率100%
```

**要点**:
- 具体分解功能
- 明确完成标准
- 调整为30分钟至1小时完成的粒度

### 步骤2:规格创建

基于TODO与AI协作创建详细规格。

**给AI的提示词示例**:
```
请从以下TODO创建详细的规格书:

[粘贴todo.md的内容]

规格书请包含以下内容:
- 函数签名(参数、返回值)
- 输入值的约束
- 错误条件和应对
- 计算逻辑的详细说明
```

**期望的规格书(示例)**:
```markdown
# 含税价格计算器 规格书

## 函数规格

### calculateTaxIncludedPrice(price: number, taxRate: number): number
- 从商品价格和税率计算含税价格
- 参数:
  - price: 商品价格(0以上的数值)
  - taxRate: 税率(0以上1以下的小数,例:0.1 = 10%)
- 返回值:含税价格(四舍五入到小数第一位)

### calculateTax(price: number, taxRate: number): number
- 计算税额
- 参数·返回值与上述相同
- 返回值:税额(四舍五入到小数第一位)

## 错误处理
- 负价格:Error("价格必须为0以上")
- 税率超出范围:Error("税率必须在0以上1以下")
- null/undefined:Error("请输入有效数值")
```

**人工审查检查点**:
- [ ] TODO的意图是否正确反映
- [ ] 函数的职责范围是否适当
- [ ] 错误条件是否覆盖全面
- [ ] 内容是否可实现

### 步骤3:测试用例创建

基于规格书让AI创建测试用例。

**给AI的提示词示例**:
```
请基于以下规格书创建全面的测试用例:

[粘贴规格书的内容]

请从以下观点创建测试用例:
- 正常流程:一般值的运行确认
- 边界值:0、1等边界的运行确认
- 异常流程:错误条件的运行确认
- 测试框架:使用 Jest
```

**生成的测试用例示例**:
```typescript
import { calculateTaxIncludedPrice, calculateTax } from './taxCalculator';

describe('含税价格计算器', () => {
  describe('calculateTaxIncludedPrice', () => {
    // 正常流程
    test('一般的含税价格计算', () => {
      expect(calculateTaxIncludedPrice(1000, 0.1)).toBe(1100);
      expect(calculateTaxIncludedPrice(150, 0.08)).toBe(162);
    });

    // 边界值
    test('价格为0的情况', () => {
      expect(calculateTaxIncludedPrice(0, 0.1)).toBe(0);
    });

    test('税率为0的情况', () => {
      expect(calculateTaxIncludedPrice(1000, 0)).toBe(1000);
    });

    // 异常流程
    test('负价格时出错', () => {
      expect(() => calculateTaxIncludedPrice(-100, 0.1))
        .toThrow('价格必须为0以上');
    });

    test('税率超出范围时出错', () => {
      expect(() => calculateTaxIncludedPrice(1000, -0.1))
        .toThrow('税率必须在0以上1以下');
      expect(() => calculateTaxIncludedPrice(1000, 1.1))
        .toThrow('税率必须在0以上1以下');
    });
  });

  describe('calculateTax', () => {
    test('税额计算', () => {
      expect(calculateTax(1000, 0.1)).toBe(100);
      expect(calculateTax(150, 0.08)).toBe(12);
    });
  });
});
```

**人工审查检查点**:
- [ ] 是否覆盖了规格的全部要求
- [ ] 异常流程测试是否适当
- [ ] 测试的期望值是否正确
- [ ] 是否有应该添加的测试用例

### 步骤4:Red-Green-Refactor-Validation循环

#### 4.1 Red(测试失败)

执行测试确认预期的失败。

```bash
npm test
```

期望输出:
```
FAIL  src/taxCalculator.test.ts
  ✕ calculateTaxIncludedPrice is not defined
  ✕ calculateTax is not defined
```

#### 4.2 Green(最小实现)

请AI进行最小实现。

**给AI的提示词示例**:
```
请创建通过以下测试用例的最小实现:

[粘贴测试用例]

要求:
- 优先使测试通过
- 不要过度复杂
- 适当实现错误处理
```

**生成的实现示例**:
```typescript
export function calculateTaxIncludedPrice(price: number, taxRate: number): number {
  validateInputs(price, taxRate);
  const tax = calculateTax(price, taxRate);
  return Math.round((price + tax) * 10) / 10;
}

export function calculateTax(price: number, taxRate: number): number {
  validateInputs(price, taxRate);
  return Math.round((price * taxRate) * 10) / 10;
}

function validateInputs(price: number, taxRate: number): void {
  if (price < 0) {
    throw new Error('价格必须为0以上');
  }
  if (taxRate < 0 || taxRate > 1) {
    throw new Error('税率必须在0以上1以下');
  }
}
```

测试执行:
```bash
npm test
# 确认所有测试通过
```

#### 4.3 Refactor(重构)

请AI改进代码。

**给AI的提示词示例**:
```
请重构以下代码:

[粘贴实现代码]

改进点:
- 提高代码可读性
- 函数职责分离
- 性能优化
- 提高TypeScript的类型安全性
```

#### 4.4 Validation(验证)

让AI验证实现的合理性。

**给AI的提示词示例**:
```
请全面验证以下实现:

[粘贴重构后的代码]
[也粘贴测试用例]

验证观点:
- 与规格要求的一致性
- 代码质量(可读性、可维护性)
- 测试覆盖率的妥当性
- 潜在问题点
- 性能方面的担忧
```

### 步骤5:最终审查

**人工审查检查点**:

**功能方面**:
- [ ] 所有测试通过
- [ ] 满足规格要求
- [ ] 错误处理适当

**代码质量**:
- [ ] 可读性高
- [ ] 适当的函数分割
- [ ] 遵循命名规范
- [ ] TypeScript类型定义适当

**可维护性**:
- [ ] 易于扩展的结构
- [ ] 测试易于维护
- [ ] 文档适当

## 回顾和学习要点

### 成功模式

**遵守流程**:
- 不跳过每个步骤执行
- 确实进行人工审查
- 不盲目接受AI输出

**适当的AI活用**:
- 使用明确具体的提示词
- 充分提供上下文
- 指定期望的输出格式

### 常见失败模式和对策

**失败1:提示词模糊**
```
❌ 不好的例子:"创建税计算的函数"
✅ 好的例子:"请按照以下规格创建从商品价格和税率计算含税价格的函数:[详细规格]"
```

**失败2:省略人工审查**
```
❌ 问题:直接使用AI的输出
✅ 对策:必须确认与规格的一致性
```

**失败3:测试设计不充分**
```
❌ 问题:仅有正常流程的测试
✅ 对策:包含边界值、异常流程的全面测试
```

### 为下一步做准备

在这个首次项目中应该掌握的感觉:
- 与AI协作开发的节奏
- 质量管理的重要性
- 提示词设计的基础
- 理解审查要点

下一章将通过更复杂的CRUD操作实现,掌握AITDD的应用能力。

## 总结

在第一个AITDD项目中重视以下内容:

1. **获得成功体验**:即使小规模也要体验完整流程
2. **确立基本习惯**:理解各步骤的重要性
3. **AI活用的感觉**:提示词设计的基础
4. **培养质量意识**:感受人工审查的价值

有了这些基础,就能在更复杂的项目中有效活用AITDD。
