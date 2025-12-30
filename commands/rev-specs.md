---
description: 从现有代码库逆向生成全面的测试用例和规格说明书。分析已实现的业务逻辑、API行为、UI组件行为,识别并生成缺失的测试用例,并将其文档化为规格说明书。
---

# rev-specs

## 目的

从现有代码库逆向生成全面的测试用例和规格说明书。分析已实现的业务逻辑、API行为、UI组件行为,识别并生成缺失的测试用例,并将其文档化为规格说明书。

## 前提条件

- 存在待分析的代码库
- 存在 `docs/reverse/` 目录(如果不存在则创建)
- 如果可能,已执行 `/jimu:rev-requirements`、`/jimu:rev-design`

## 执行内容

1. **现有测试分析**
   - 确认单元测试(Unit Test)实现情况
   - 确认集成测试(Integration Test)实现情况
   - 确认E2E测试(End-to-End Test)实现情况
   - 测试覆盖率测量

2. **从实现代码逆向生成测试用例**
   - 从函数·方法的参数·返回值生成测试用例
   - 从条件分支生成边界值测试
   - 从错误处理生成异常系统测试
   - 从数据库操作生成数据测试

3. **从API规格生成测试用例**
   - 各端点的正常系统测试
   - 认证·授权测试
   - 验证错误测试
   - HTTP状态码测试

4. **从UI组件生成测试用例**
   - 组件渲染测试
   - 用户交互测试
   - 状态变更测试
   - 属性变更测试

5. **生成性能·安全测试用例**
   - 负载测试场景
   - 安全漏洞测试
   - 响应时间测试

6. **生成测试规格说明书**
   - 测试计划书
   - 测试用例列表
   - 测试环境规格
   - 测试程序书

7. **文件创建**
   - `docs/reverse/{项目名}-test-specs.md` - 测试规格说明书
   - `docs/reverse/{项目名}-test-cases.md` - 测试用例列表
   - `docs/reverse/tests/` - 生成的测试代码

## 输出格式示例

### test-specs.md

```markdown
# {项目名} 测试规格说明书(逆向生成)

## 分析概要

**分析日期**: {执行日期}
**目标代码库**: {路径}
**测试覆盖率**: {当前覆盖率}%
**生成测试用例数**: {生成数}个
**推荐实现测试数**: {推荐数}个

## 当前测试实现情况

### 测试框架
- **单元测试**: {Jest/Vitest/pytest等}
- **集成测试**: {Supertest/TestContainers等}
- **E2E测试**: {Cypress/Playwright等}
- **代码覆盖率**: {istanbul/c8等}

### 测试覆盖率详情

| 文件/目录 | 行覆盖率 | 分支覆盖率 | 函数覆盖率 |
|---------------------|-------------|-------------|-------------|
| src/auth/ | 85% | 75% | 90% |
| src/users/ | 60% | 45% | 70% |
| src/components/ | 40% | 30% | 50% |
| **总计** | **65%** | **55%** | **75%** |

### 测试类别实现情况

#### 单元测试
- [x] **认证服务**: auth.service.spec.ts
- [x] **用户服务**: user.service.spec.ts
- [ ] **数据转换工具**: 未实现
- [ ] **验证辅助器**: 未实现

#### 集成测试
- [x] **认证API**: auth.controller.spec.ts
- [ ] **用户管理API**: 未实现
- [ ] **数据库操作**: 未实现

#### E2E测试
- [ ] **用户登录流程**: 未实现
- [ ] **数据操作流程**: 未实现
- [ ] **错误处理**: 未实现

## 生成的测试用例

### API测试用例

#### POST /auth/login - 登录认证

**正常系统测试**
\`\`\`typescript
describe('POST /auth/login', () => {
  it('使用有效认证信息登录成功', async () => {
    const response = await request(app)
      .post('/auth/login')
      .send({
        email: 'test@example.com',
        password: 'password123'
      });

    expect(response.status).toBe(200);
    expect(response.body.success).toBe(true);
    expect(response.body.data.token).toBeDefined();
    expect(response.body.data.user.email).toBe('test@example.com');
  });

  it('JWT令牌以正确格式返回', async () => {
    const response = await request(app)
      .post('/auth/login')
      .send(validCredentials);

    const token = response.body.data.token;
    expect(token).toMatch(/^[A-Za-z0-9-_]+\.[A-Za-z0-9-_]+\.[A-Za-z0-9-_]+$/);
  });
});
\`\`\`

**异常系统测试**
\`\`\`typescript
describe('POST /auth/login - 异常系统', () => {
  it('使用无效电子邮件地址时出错', async () => {
    const response = await request(app)
      .post('/auth/login')
      .send({
        email: 'invalid-email',
        password: 'password123'
      });

    expect(response.status).toBe(400);
    expect(response.body.success).toBe(false);
    expect(response.body.error.code).toBe('VALIDATION_ERROR');
  });

  it('不存在的用户时出错', async () => {
    const response = await request(app)
      .post('/auth/login')
      .send({
        email: 'nonexistent@example.com',
        password: 'password123'
      });

    expect(response.status).toBe(401);
    expect(response.body.error.code).toBe('INVALID_CREDENTIALS');
  });

  it('密码错误时出错', async () => {
    const response = await request(app)
      .post('/auth/login')
      .send({
        email: 'test@example.com',
        password: 'wrongpassword'
      });

    expect(response.status).toBe(401);
    expect(response.body.error.code).toBe('INVALID_CREDENTIALS');
  });
});
\`\`\`

**边界值测试**
\`\`\`typescript
describe('POST /auth/login - 边界值', () => {
  it('使用最小字符数密码测试', async () => {
    // 8个字符(最小要求)
    const response = await request(app)
      .post('/auth/login')
      .send({
        email: 'test@example.com',
        password: '12345678'
      });

    expect(response.status).toBe(200);
  });

  it('使用最大字符数电子邮件地址测试', async () => {
    // 255个字符(最大要求)
    const longEmail = 'a'.repeat(243) + '@example.com';
    const response = await request(app)
      .post('/auth/login')
      .send({
        email: longEmail,
        password: 'password123'
      });

    expect(response.status).toBe(400);
  });
});
\`\`\`

### UI组件测试用例

#### LoginForm 组件

**渲染测试**
\`\`\`typescript
import { render, screen } from '@testing-library/react';
import { LoginForm } from './LoginForm';

describe('LoginForm', () => {
  it('显示必要元素', () => {
    render(<LoginForm onSubmit={jest.fn()} />);

    expect(screen.getByLabelText('电子邮件地址')).toBeInTheDocument();
    expect(screen.getByLabelText('密码')).toBeInTheDocument();
    expect(screen.getByRole('button', { name: '登录' })).toBeInTheDocument();
  });

  it('初始状态下错误消息隐藏', () => {
    render(<LoginForm onSubmit={jest.fn()} />);

    expect(screen.queryByText(/错误/)).not.toBeInTheDocument();
  });
});
\`\`\`

**用户交互测试**
\`\`\`typescript
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

describe('LoginForm - 用户交互', () => {
  it('提交表单时调用onSubmit', async () => {
    const mockSubmit = jest.fn();
    render(<LoginForm onSubmit={mockSubmit} />);

    await userEvent.type(screen.getByLabelText('电子邮件地址'), 'test@example.com');
    await userEvent.type(screen.getByLabelText('密码'), 'password123');
    await userEvent.click(screen.getByRole('button', { name: '登录' }));

    expect(mockSubmit).toHaveBeenCalledWith({
      email: 'test@example.com',
      password: 'password123'
    });
  });

  it('验证错误时不提交', async () => {
    const mockSubmit = jest.fn();
    render(<LoginForm onSubmit={mockSubmit} />);

    await userEvent.click(screen.getByRole('button', { name: '登录' }));

    expect(mockSubmit).not.toHaveBeenCalled();
    expect(screen.getByText('电子邮件地址为必填项')).toBeInTheDocument();
  });
});
\`\`\`

### 服务层测试用例

#### AuthService 单元测试

\`\`\`typescript
import { AuthService } from './auth.service';
import { UserRepository } from './user.repository';

jest.mock('./user.repository');

describe('AuthService', () => {
  let authService: AuthService;
  let mockUserRepository: jest.Mocked<UserRepository>;

  beforeEach(() => {
    mockUserRepository = new UserRepository() as jest.Mocked<UserRepository>;
    authService = new AuthService(mockUserRepository);
  });

  describe('login', () => {
    it('使用有效认证信息返回用户信息和令牌', async () => {
      const mockUser = {
        id: '1',
        email: 'test@example.com',
        hashedPassword: 'hashed_password'
      };

      mockUserRepository.findByEmail.mockResolvedValue(mockUser);
      jest.spyOn(authService, 'verifyPassword').mockResolvedValue(true);
      jest.spyOn(authService, 'generateToken').mockReturnValue('mock_token');

      const result = await authService.login('test@example.com', 'password');

      expect(result).toEqual({
        user: { id: '1', email: 'test@example.com' },
        token: 'mock_token'
      });
    });

    it('不存在的用户时抛出错误', async () => {
      mockUserRepository.findByEmail.mockResolvedValue(null);

      await expect(
        authService.login('nonexistent@example.com', 'password')
      ).rejects.toThrow('Invalid credentials');
    });
  });
});
\`\`\`

## 性能测试用例

### 负载测试

\`\`\`typescript
describe('性能测试', () => {
  it('登录API - 100并发连接测试', async () => {
    const promises = Array.from({ length: 100 }, () =>
      request(app).post('/auth/login').send(validCredentials)
    );

    const startTime = Date.now();
    const responses = await Promise.all(promises);
    const endTime = Date.now();

    // 所有请求成功
    responses.forEach(response => {
      expect(response.status).toBe(200);
    });

    // 响应时间在5秒以内
    expect(endTime - startTime).toBeLessThan(5000);
  });

  it('数据库 - 大量数据搜索性能', async () => {
    // 创建1000条测试数据
    await createTestData(1000);

    const startTime = Date.now();
    const response = await request(app)
      .get('/users')
      .query({ limit: 100, offset: 0 });
    const endTime = Date.now();

    expect(response.status).toBe(200);
    expect(endTime - startTime).toBeLessThan(1000); // 1秒以内
  });
});
\`\`\`

### 安全测试

\`\`\`typescript
describe('安全测试', () => {
  it('SQL注入防护', async () => {
    const maliciousInput = "'; DROP TABLE users; --";

    const response = await request(app)
      .post('/auth/login')
      .send({
        email: maliciousInput,
        password: 'password'
      });

    // 系统正常运行,数据库未损坏
    expect(response.status).toBe(400);

    // 确认用户表仍然存在
    const usersResponse = await request(app)
      .get('/users')
      .set('Authorization', 'Bearer ' + validToken);
    expect(usersResponse.status).not.toBe(500);
  });

  it('XSS防护', async () => {
    const xssPayload = '<script>alert("XSS")</script>';

    const response = await request(app)
      .post('/users')
      .set('Authorization', 'Bearer ' + validToken)
      .send({
        name: xssPayload,
        email: 'test@example.com'
      });

    // 响应中脚本已转义
    expect(response.body.data.name).not.toContain('<script>');
    expect(response.body.data.name).toContain('&lt;script&gt;');
  });
});
\`\`\`

## E2E测试用例

### Playwright/Cypress 测试场景

\`\`\`typescript
// 用户登录流程 E2E测试
describe('用户登录流程', () => {
  it('从正常登录到仪表盘显示', async () => {
    await page.goto('/login');

    // 输入登录表单
    await page.fill('[data-testid="email-input"]', 'test@example.com');
    await page.fill('[data-testid="password-input"]', 'password123');
    await page.click('[data-testid="login-button"]');

    // 重定向到仪表盘
    await page.waitForURL('/dashboard');

    // 确认用户信息显示
    await expect(page.locator('[data-testid="user-name"]')).toContainText('测试用户');

    // 确认登出功能
    await page.click('[data-testid="logout-button"]');
    await page.waitForURL('/login');
  });

  it('登录失败时的错误显示', async () => {
    await page.goto('/login');

    await page.fill('[data-testid="email-input"]', 'wrong@example.com');
    await page.fill('[data-testid="password-input"]', 'wrongpassword');
    await page.click('[data-testid="login-button"]');

    // 确认错误消息显示
    await expect(page.locator('[data-testid="error-message"]'))
      .toContainText('认证信息不正确');
  });
});
\`\`\`

## 测试环境设置

### 数据库测试设置

\`\`\`typescript
// 测试用数据库设置
beforeAll(async () => {
  // 测试用数据库连接
  await setupTestDatabase();

  // 执行迁移
  await runMigrations();
});

beforeEach(async () => {
  // 每个测试前清理数据
  await cleanupDatabase();

  // 投入基本测试数据
  await seedTestData();
});

afterAll(async () => {
  // 断开测试用数据库连接
  await teardownTestDatabase();
});
\`\`\`

### Mock设置

\`\`\`typescript
// 外部服务的Mock
jest.mock('./email.service', () => ({
  EmailService: jest.fn().mockImplementation(() => ({
    sendEmail: jest.fn().mockResolvedValue(true)
  }))
}));

// 环境变量的Mock
process.env.JWT_SECRET = 'test-secret';
process.env.NODE_ENV = 'test';
\`\`\`

## 缺失测试的优先级

### 高优先级(建议立即实现)
1. **E2E测试套件** - 保证整个用户流程的运行
2. **API集成测试** - 测试整个后端API
3. **安全测试** - 验证漏洞防护

### 中优先级(在下个Sprint中实现)
1. **性能测试** - 负载·响应时间测试
2. **UI组件测试** - 保证前端运行
3. **数据库测试** - 数据一致性测试

### 低优先级(作为持续改进实现)
1. **浏览器兼容性测试** - 在多个浏览器中确认运行
2. **可访问性测试** - 确认a11y对应
3. **国际化测试** - 确认多语言支持

```

### test-cases.md

```markdown
# {项目名} 测试用例列表(逆向生成)

## 测试用例概要

| ID | 测试名称 | 类别 | 优先级 | 实现状况 | 预估工时 |
|----|----------|----------|--------|----------|----------|
| TC-001 | 登录正常系统 | API | 高 | ✅ | 2h |
| TC-002 | 登录异常系统 | API | 高 | ✅ | 3h |
| TC-003 | E2E登录流程 | E2E | 高 | ❌ | 4h |
| TC-004 | 性能负载测试 | 性能 | 中 | ❌ | 6h |

## 详细测试用例

### TC-001: 登录API正常系统测试

**测试目的**: 验证使用有效认证信息的登录功能

**前提条件**:
- 测试用户存在于数据库中
- 密码已正确哈希

**测试步骤**:
1. 向 POST /auth/login 发送请求
2. 发送包含有效email、password的JSON
3. 确认响应

**期望结果**:
- HTTP状态: 200
- success: true
- data.token: JWT格式的令牌
- data.user: 用户信息

**实现文件**: `auth.controller.spec.ts`

### TC-002: 登录API异常系统测试

**测试目的**: 验证使用无效认证信息时的适当错误处理

**测试用例**:
1. 不存在的电子邮件地址
2. 无效密码
3. 不正确的电子邮件格式
4. 空字符·null值
5. SQL注入攻击

**期望结果**:
- 适当的HTTP状态码
- 统一的错误响应格式
- 无安全漏洞

**实现状况**: ✅ 部分实现

```

## 测试代码生成算法

### 1. 通过静态分析提取测试用例

```
1. 函数签名分析 → 参数·返回值的测试用例
2. 条件分支分析 → 分支覆盖测试用例
3. 异常处理分析 → 异常系统测试用例
4. 数据库访问分析 → 数据测试用例
```

### 2. 通过动态分析生成测试

```
1. API调用日志 → 实际使用模式测试
2. 用户操作日志 → E2E测试场景
3. 性能日志 → 负载测试场景
```

### 3. 测试覆盖率差距分析

```
1. 测量当前覆盖率
2. 识别未测试的行·分支
3. 识别关键路径
4. 基于风险的优先级排序
```

## 执行命令示例

```bash
# 完整分析(生成所有测试用例)
claude code rev-specs

# 仅生成特定测试类别
claude code rev-specs --type unit
claude code rev-specs --type integration
claude code rev-specs --type e2e

# 针对特定文件/目录
claude code rev-specs --path ./src/auth

# 实际生成和输出测试代码
claude code rev-specs --generate-code

# 结合覆盖率报告分析
claude code rev-specs --with-coverage

# 优先级过滤
claude code rev-specs --priority high
```

## 执行后确认

- 显示当前测试覆盖率和缺失部分的详细报告
- 显示生成的测试用例数和预估实现工时
- 提示按优先级排序的实现推荐列表
- 建议测试环境的设置要求和推荐工具
- 提示集成到CI/CD流水线的方案
