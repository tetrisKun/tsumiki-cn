# 4.3 API开发实践

## 学习目标

本章将通过RESTful API的开发,学习AITDD在实际Web应用开发中的应用方法:

- 在包含外部依赖的实现中活用AITDD
- 异步处理和错误处理的实现
- HTTP状态码和响应设计
- API文档的自动生成
- 接近实际生产环境的开发体验

## 项目概述:任务管理API

基于前一章开发的任务管理系统,使用Express.js构建RESTful API。

### API规格概述

```http
GET    /api/tasks       # 获取所有任务
GET    /api/tasks/:id   # 获取特定任务
POST   /api/tasks       # 创建新任务
PUT    /api/tasks/:id   # 更新任务
DELETE /api/tasks/:id   # 删除任务
```

### 技术栈

- **Web框架**: Express.js
- **语言**: TypeScript
- **测试**: Jest + Supertest
- **验证**: express-validator
- **文档**: OpenAPI (Swagger)

## 新的技术复杂性

API开发在前一章的CRUD基础上增加了以下要素:

**HTTP相关**:
- 请求/响应处理
- 状态码管理
- 请求头处理
- 路由设计

**异步处理**:
- Promise/async-await
- 错误处理
- 超时处理

**验证**:
- 请求数据验证
- 响应格式统一
- 错误响应标准化

## 实践练习

### 步骤1:创建TODO和API设计

在AITDD的API开发中,**3个功能左右的整合限制**同样适用。因此,需要适当地分割端点。

```markdown
# TODO: 任务管理API实现

## 阶段1:基础构建
- [ ] Express.js项目设置
- [ ] TypeScript配置
- [ ] 基本中间件配置
- [ ] 错误处理中间件

## 阶段2:基础API(3个端点)
- [ ] GET /api/tasks - 获取所有任务
- [ ] GET /api/tasks/:id - 获取单个任务
- [ ] POST /api/tasks - 创建任务

## 阶段3:扩展API(剩余端点)
- [ ] PUT /api/tasks/:id - 更新任务
- [ ] DELETE /api/tasks/:id - 删除任务
- [ ] GET /api/tasks?search=xxx - 条件搜索

## 阶段4:质量提升
- [ ] OpenAPI规格书生成
- [ ] 输入验证增强
- [ ] 安全请求头添加
- [ ] 性能优化
```

### 步骤2:阶段1 - 基础构建

#### 规格创建

**给AI的提示词**:
```
请创建使用Express.js和TypeScript的API项目基础配置的规格。

要求:
- TypeScript环境下的Express.js配置
- 基本中间件(CORS、JSON解析等)
- 结构化的错误处理
- 开发·生产环境的配置分离
- 测试环境的设置

请参考以下TODO:
[粘贴阶段1的TODO]
```

**生成的规格示例**:
````markdown
# 任务管理API基础规格

## 项目结构
```
src/
├── app.ts              # Express应用配置
├── server.ts           # 服务器启动入口点
├── routes/             # 路由定义
├── controllers/        # 业务逻辑
├── middleware/         # 自定义中间件
├── types/              # TypeScript类型定义
└── tests/              # 测试文件
```

## Express配置
- CORS支持
- JSON/URL编码解析
- 请求日志
- 安全请求头
- 错误处理中间件

## 错误处理标准化
```typescript
interface APIError {
  message: string;
  code: string;
  statusCode: number;
  details?: any;
}
```

## 响应格式统一
```typescript
interface APIResponse<T> {
  success: boolean;
  data?: T;
  error?: APIError;
  meta?: {
    timestamp: string;
    requestId: string;
  };
}
```
```

#### 测试用例创建和Red-Green-Refactor-Validation

**API基础测试**:
```typescript
describe('API基础测试', () => {
  test('服务器启动确认', async () => {
    const response = await request(app).get('/health');
    expect(response.status).toBe(200);
    expect(response.body.success).toBe(true);
  });

  test('CORS配置确认', async () => {
    const response = await request(app)
      .options('/api/tasks')
      .set('Origin', 'http://localhost:3000')
      .set('Access-Control-Request-Method', 'GET');

    expect(response.headers['access-control-allow-origin']).toBeDefined();
  });

  test('JSON解析确认', async () => {
    const response = await request(app)
      .post('/api/test')
      .send({ test: 'data' })
      .set('Content-Type', 'application/json');

    expect(response.status).not.toBe(400); // 不是JSON解析错误
  });

  test('错误处理确认', async () => {
    const response = await request(app).get('/api/nonexistent');
    expect(response.status).toBe(404);
    expect(response.body.success).toBe(false);
    expect(response.body.error).toBeDefined();
  });
});
````

### 步骤3:阶段2 - 基础API实现

#### GET /api/tasks 的实现

**规格**:
```markdown
## 获取所有任务API

### 端点
GET /api/tasks

### 响应(成功时)
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "title": "任务标题",
      "description": "任务说明",
      "completed": false,
      "createdAt": "2025-06-21T10:00:00Z",
      "updatedAt": "2025-06-21T10:00:00Z"
    }
  ],
  "meta": {
    "timestamp": "2025-06-21T10:00:00Z",
    "requestId": "req-123"
  }
}
```

### 状态码
- 200: 正常获取(包括空数组)
- 500: 服务器错误


**AI提示词示例**:
```
请根据以下规格实现 GET /api/tasks 端点:

[粘贴规格]

要求:
1. 使用Express.js路由器
2. 利用前一章创建的TaskManager类
3. 适当地实现错误处理
4. 确保TypeScript的类型安全
5. 创建使用Supertest的集成测试

现有代码:
[粘贴TaskManager类的代码]
[粘贴API基础的代码]
```

**预期实现**:
```typescript
// routes/tasks.ts
import { Router } from 'express';
import { TaskController } from '../controllers/TaskController';

const router = Router();
const taskController = new TaskController();

router.get('/', taskController.getAllTasks);

export default router;

// controllers/TaskController.ts
import { Request, Response, NextFunction } from 'express';
import { TaskManager } from '../services/TaskManager';
import { APIResponse } from '../types/api';

export class TaskController {
  private taskManager = new TaskManager();

  getAllTasks = async (req: Request, res: Response, next: NextFunction) => {
    try {
      const tasks = this.taskManager.getAllTasks();

      const response: APIResponse<typeof tasks> = {
        success: true,
        data: tasks,
        meta: {
          timestamp: new Date().toISOString(),
          requestId: req.headers['x-request-id'] as string || 'unknown'
        }
      };

      res.status(200).json(response);
    } catch (error) {
      next(error);
    }
  };
}
```

#### GET /api/tasks/:id 的实现

**规格追加**:
```markdown
## 获取单个任务API

### 端点
GET /api/tasks/:id

### 参数
- id: 任务ID(UUID格式)

### 状态码
- 200: 正常获取
- 400: 无效的ID格式
- 404: 找不到任务
- 500: 服务器错误
```

#### POST /api/tasks 的实现

**规格追加**:
````markdown
## 任务创建API

### 端点
POST /api/tasks

### 请求体
```json
{
  "title": "任务标题",
  "description": "任务说明"
}
```

### 验证
- title: 必需,1-100字符
- description: 可选,0-500字符

### 状态码
- 201: 创建成功
- 400: 验证错误
- 500: 服务器错误
````

### 步骤4:阶段3 - 扩展API实现

#### 输入验证增强

**活用express-validator**:

```typescript
// middleware/validation.ts
import { body, param, validationResult } from 'express-validator';
import { Request, Response, NextFunction } from 'express';

export const createTaskValidation = [
  body('title')
    .notEmpty()
    .withMessage('标题是必需的')
    .isLength({ min: 1, max: 100 })
    .withMessage('标题必须是1-100字符'),

  body('description')
    .optional()
    .isLength({ max: 500 })
    .withMessage('说明必须是500字符以下'),
];

export const taskIdValidation = [
  param('id')
    .isUUID()
    .withMessage('请指定有效的UUID格式ID'),
];

export const handleValidationErrors = (
  req: Request,
  res: Response,
  next: NextFunction
) => {
  const errors = validationResult(req);
  if (!errors.isEmpty()) {
    return res.status(400).json({
      success: false,
      error: {
        message: '验证错误',
        code: 'VALIDATION_ERROR',
        statusCode: 400,
        details: errors.array()
      }
    });
  }
  next();
};
```

#### PUT /api/tasks/:id 实现

**支持部分更新**:
```typescript
export const updateTaskValidation = [
  ...taskIdValidation,
  body('title')
    .optional()
    .isLength({ min: 1, max: 100 })
    .withMessage('标题必须是1-100字符'),

  body('description')
    .optional()
    .isLength({ max: 500 })
    .withMessage('说明必须是500字符以下'),

  body('completed')
    .optional()
    .isBoolean()
    .withMessage('completed必须是boolean值'),
];
```

#### DELETE /api/tasks/:id 实现

**考虑软删除**:
```typescript
deleteTask = async (req: Request, res: Response, next: NextFunction) => {
  try {
    const { id } = req.params;
    const deleted = this.taskManager.deleteTask(id);

    if (!deleted) {
      return res.status(404).json({
        success: false,
        error: {
          message: '找不到任务',
          code: 'TASK_NOT_FOUND',
          statusCode: 404
        }
      });
    }

    res.status(204).send(); // No Content
  } catch (error) {
    next(error);
  }
};
```

### 步骤5:阶段4 - 质量提升

#### OpenAPI规格书的生成

**AI提示词示例**:
```
请从以下API端点生成OpenAPI 3.0规格书:

[实现的端点列表]
[响应格式的定义]
[错误响应的定义]

要求:
1. 可在Swagger UI中显示的格式
2. 所有端点的详细文档
3. 包含请求/响应的示例
4. 包含错误代码的说明
```

**生成的OpenAPI规格示例**:
```yaml
openapi: 3.0.0
info:
  title: 任务管理API
  version: 1.0.0
  description: 简单任务管理系统的RESTful API

paths:
  /api/tasks:
    get:
      summary: 获取所有任务
      responses:
        '200':
          description: 成功
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/TaskListResponse'

    post:
      summary: 创建任务
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateTaskRequest'
      responses:
        '201':
          description: 创建成功
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/TaskResponse'
        '400':
          description: 验证错误
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ErrorResponse'

components:
  schemas:
    Task:
      type: object
      properties:
        id:
          type: string
          format: uuid
        title:
          type: string
          minLength: 1
          maxLength: 100
        description:
          type: string
          maxLength: 500
        completed:
          type: boolean
        createdAt:
          type: string
          format: date-time
        updatedAt:
          type: string
          format: date-time
```

## 复杂问题的处理方法

### 切换到手动实现的判断

在API开发中,以下情况考虑手动实现:

**无法想象实现方法的情况**:
- "自定义中间件的复杂认证逻辑"
- "与WebSocket的集成处理"
- "复杂的数据库优化"

**需要性能优化的情况**:
- "大量请求的处理优化"
- "内存使用量的优化"
- "响应时间的缩短"

### 手动实现时的AI活用

不是完全手动,而是在以下方面活用AI:
```typescript
// 可以想象实现的部分委托给AI
const generateResponseHelper = (data: any, meta: any) => {
  // 这部分可以用AI生成
  return {
    success: true,
    data,
    meta: {
      timestamp: new Date().toISOString(),
      ...meta
    }
  };
};

// 复杂的逻辑手动实现
const complexAuthMiddleware = (req, res, next) => {
  // 复杂的认证逻辑手动实现
  // 但是,部分利用AI补全
};
```

## API开发特有的测试策略

### 集成测试模式

**端到端测试**:
```typescript
describe('API集成测试', () => {
  test('任务管理流程', async () => {
    // 1. 创建任务
    const createResponse = await request(app)
      .post('/api/tasks')
      .send({
        title: '测试任务',
        description: '用于测试的任务'
      });

    expect(createResponse.status).toBe(201);
    const taskId = createResponse.body.data.id;

    // 2. 确认获取任务
    const getResponse = await request(app)
      .get(`/api/tasks/${taskId}`);

    expect(getResponse.status).toBe(200);
    expect(getResponse.body.data.title).toBe('测试任务');

    // 3. 更新任务
    const updateResponse = await request(app)
      .put(`/api/tasks/${taskId}`)
      .send({
        completed: true
      });

    expect(updateResponse.status).toBe(200);
    expect(updateResponse.body.data.completed).toBe(true);

    // 4. 删除任务
    const deleteResponse = await request(app)
      .delete(`/api/tasks/${taskId}`);

    expect(deleteResponse.status).toBe(204);

    // 5. 确认删除
    const getAfterDeleteResponse = await request(app)
      .get(`/api/tasks/${taskId}`);

    expect(getAfterDeleteResponse.status).toBe(404);
  });
});
```

### 性能测试

**负载测试**:
```typescript
describe('性能测试', () => {
  test('并发请求处理', async () => {
    const requests = Array.from({ length: 100 }, (_, i) =>
      request(app)
        .post('/api/tasks')
        .send({
          title: `并行任务${i}`,
          description: '并行处理测试'
        })
    );

    const startTime = Date.now();
    const responses = await Promise.all(requests);
    const endTime = Date.now();

    responses.forEach(response => {
      expect(response.status).toBe(201);
    });

    expect(endTime - startTime).toBeLessThan(5000); // 5秒以内
  });
});
```

## 错误处理的最佳实践

### 全面的错误处理

```typescript
// middleware/errorHandler.ts
export const errorHandler = (
  error: any,
  req: Request,
  res: Response,
  next: NextFunction
) => {
  // 日志输出
  console.error('API Error:', {
    error: error.message,
    stack: error.stack,
    method: req.method,
    url: req.url,
    body: req.body,
    timestamp: new Date().toISOString()
  });

  // 按错误类型处理
  if (error.name === 'TaskNotFoundError') {
    return res.status(404).json({
      success: false,
      error: {
        message: '找不到任务',
        code: 'TASK_NOT_FOUND',
        statusCode: 404
      }
    });
  }

  if (error.name === 'ValidationError') {
    return res.status(400).json({
      success: false,
      error: {
        message: '验证错误',
        code: 'VALIDATION_ERROR',
        statusCode: 400,
        details: error.details
      }
    });
  }

  // 未知错误
  res.status(500).json({
    success: false,
    error: {
      message: '发生服务器内部错误',
      code: 'INTERNAL_SERVER_ERROR',
      statusCode: 500
    }
  });
};
```

## 实践中的学习效果

### API开发中的AITDD效果

**开发速度的体验**:
- 传统API开发:几天~1周
- 使用AITDD:几小时
- 实现**大幅提高效率**

**质量的稳定性**:
- 通过全面测试保证质量
- 错误处理的标准化
- API文档的自动生成

**实践技能的掌握**:
- Web开发中的有效AI活用
- 应对复杂的集成处理
- 生产质量的实现

### 与传统开发的区别

**设计阶段**:
- 传统:花时间进行详细设计
- AITDD:与AI协作进行阶段性设计

**实现阶段**:
- 传统:手动详细实现
- AITDD:AI生成代码的质量管理

**测试阶段**:
- 传统:实现后创建测试
- AITDD:测试优先方法

## 为下一章做准备

通过这次API开发体验,将掌握以下内容:

1. **Web开发中的AI活用技术**
2. **复杂集成处理的管理方法**
3. **生产质量的实现技法**
4. **错误处理和调试技术**

下一章将学习在这些实现过程中发生的错误和故障的具体处理方法。

## 总结

通过API开发掌握了以下内容:

**技术成长**:
- RESTful API设计的实践
- 异步处理和错误处理
- TypeScript的类型安全实现
- 全面的测试策略

**AITDD活用技术**:
- 复杂实现中的AI协作
- 阶段性功能添加的管理
- 质量管理的自动化
- 文档生成的活用

**实践开发能力**:
- 生产质量的实现
- 考虑性能的设计
- 安全对策的实现
- 考虑运维的设计

通过这些经验,建立了在实际产品开发中也能有效活用AITDD的基础。
