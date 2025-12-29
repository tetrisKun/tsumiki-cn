# 2.1 必要的工具与配置

说明实践 AITDD 所需的工具和环境配置步骤。AITDD 不需要特殊工具，只需在现有开发环境中添加 AI 辅助工具即可开始。

## 必要工具清单

### 必备工具

#### 1. Claude Sonnet 4 + Claude Code
**作用**: 主要的 AI 开发辅助工具
**用途**: 代码生成、测试创建、重构、验证
**访问方式**: 通过 Claude Code

#### 2. VS Code
**作用**: 集成开发环境
**用途**: 代码编辑、调试、项目管理
**特点**: 可与 Claude Code 集成

#### 3. Git + GitHub
**作用**: 版本控制
**用途**: 代码管理、历史追踪、恢复
**重要性**: AI 未达到预期结果时的恢复手段

### 辅助工具

#### Gemini（可选）
**作用**: 调研·信息收集用 AI
**用途**: 库调研、技术信息收集
**特点**: 利用长上下文处理大量信息

## 配置步骤

### 步骤 1: 订阅 Claude Pro 计划

1. **创建 Claude 账号**
   - 访问 https://claude.ai
   - 创建账号

2. **升级到 Pro 计划**
   - 加入月费 $20 的 Pro 计划
   - 可设置最高 $200 上限
   - 避免像 API 费用那样的无上限成本

3. **启用 Claude Code**
   - Pro 计划可访问
   - 可自由用于开发用途

### 步骤 2: VS Code 配置

1. **安装 VS Code**
   ```bash
   # Windows
   winget install Microsoft.VisualStudioCode

   # macOS
   brew install --cask visual-studio-code

   # Linux
   sudo apt install code
   ```

2. **安装基本扩展**
   - Git Graph（Git 操作可视化）
   - GitLens（Git 信息显示增强）
   - 语言特定扩展（JavaScript、Python 等）

3. **设置 Claude Code 集成**
   - VS Code 插件或 Claude Code 设置
   - 与项目目录的联动设置

### 步骤 3: Git 环境构建

1. **安装和设置 Git**
   ```bash
   # 基本设置
   git config --global user.name "Your Name"
   git config --global user.email "your.email@example.com"

   # 默认分支设置
   git config --global init.defaultBranch main
   ```

2. **准备 GitHub 账号**
   - 创建 GitHub 账号
   - 设置 SSH 密钥或 Personal Access Token
   - 准备仓库创建

3. **AITDD 用分支策略**
   ```bash
   # 基本工作流
   git checkout -b feature/new-functionality
   # 实践 AITDD
   git add .
   git commit -m "Implement feature with AITDD"
   git push origin feature/new-functionality
   ```

## AI 工具比较与选择标准

### 选择 Claude Sonnet 4 的理由

在实践 AITDD 时，在众多 AI 工具中选择 Claude Sonnet 4 作为主力工具的理由说明。

#### 基于综合评价的选择

**主要考虑要素:**
1. **成本效率**: AITDD 需要频繁试用，必须保持合理成本水平
2. **编码性能**: 重视稳定性能而非最高性能
3. **可访问性**: 可在限制较少的计划中自由使用
4. **集成性**: 与开发环境的联动和工作流的一致性

**Claude Sonnet 4 的优势:**
- **Claude Code 集成**: 通过 VS Code 插件深度集成
- **Pro 计划**: 月费 $20，可设置上限 $200（避免 API 无上限成本）
- **AITDD 适性**: 针对重视试用的开发风格优化
- **综合平衡**: 性能、成本、易用性的最佳平衡

#### 与其他工具的比较结果

**已考虑的 AI 工具:**
- ChatGPT: 性能高但成本方面有问题
- GitHub Copilot: 专注于代码补全，对整个 AITDD 不够
- 其他 AI 工具: 试用结果，综合上收敛于 Claude Sonnet 4

**收敛理由:**
```
项目               Claude Sonnet 4    其他工具
──────────────────────────────────────────
成本效率           ◎                △
编码性能           ○                ◎
可访问性           ◎                △
集成性             ◎                ○
AITDD 适性         ◎                △
──────────────────────────────────────────
综合评价           最优              有问题
```

### 与 Gemini 的分工策略

以 Claude Sonnet 4 为主力，在特定用途上辅助性地使用 Gemini。

#### 分工基本方针

**Claude Sonnet 4（主力工具）:**
- 实现阶段的全部（设计～测试～实现～重构～Validation）
- 一贯执行整个 AITDD 流程
- 兼顾代码生成和质量检查

**Gemini（辅助工具）:**
- 技术调研·信息收集
- 利用长上下文处理大量信息
- 必要库的调研
- 需要大量调查的任务

#### 具体联动模式

```
调研阶段:
Gemini → 技术信息收集 → 向 Claude Sonnet 4 提供信息

实现阶段:
Claude Sonnet 4 → 一贯执行 AITDD 流程
```

**联动实例:**
1. **新库调研**: 用 Gemini 收集信息
2. **调研结果整合**: 向 Claude Sonnet 4 提供调研结果
3. **执行实现**: 用 Claude Sonnet 4 执行 AITDD 流程

### 回退策略

说明 AI 未达到预期结果时的应对方法。

#### 基本回退步骤

**步骤 1: 恢复状态**
```bash
# 回到前一状态
git reset --hard HEAD~1
# 或回到特定提交
git reset --hard <commit-hash>
```

**步骤 2: 调整提示词**
- 明确化·详细化指示
- 添加上下文信息
- 明示约束条件

**步骤 3: 重新执行**
- 用同一工具（Claude Sonnet 4）重试
- 不切换到其他工具
- 保持一致性应对

#### 回退判断标准

**执行 git reset 的时机:**
- 最终代码与预期偏差较大时
- 判断重新制作比修正请求更快时
- 多次修正尝试未见改善时

**提示词调整指引:**
```
# 改善前（模糊）
"修正这个代码"

# 改善后（具体）
"修正这个代码的以下问题：
1. 验证错误未得到适当处理
2. 返回值类型与规格不符
3. 边界情况测试不足"
```

#### 回退策略特征

**简单性:**
- 避免复杂判断逻辑
- 重视快速恢复

**一致性:**
- 以同一工具重试为基本
- 避免因工具切换造成混乱

**学习性:**
- 通过重复磨练感觉
- 积累经验值提升判断标准

### 新 AI 工具评估方法

为持续改进，也制定新 AI 工具的评估方法。

#### 评估流程

**信息收集阶段:**
1. **常时检查**: 持续检查新 AI 工具信息
2. **确认话题性**: 不是一时热潮而是持续关注
3. **社区反应**: 确认开发者社区的评价

**试用判断标准:**
- **持续性**: 话题是否持续数月
- **实用性**: 能否应用于 AITDD 工作流
- **成本效率**: 与当前工具配置相比是否有优势

**谨慎方法:**
- 不立即跟进，实施充分的信息收集
- 保持当前稳定工作流的同时评估
- 仅在确认明确优势时才考虑迁移

## 推荐技术栈

### 编程语言

#### 推荐语言
**JavaScript/TypeScript**
- 包管理: npm/yarn
- 透明性: 通过 package.json 可视化依赖关系
- AI 对应: 可动态调研库

**Python**
- 包管理: pip/poetry
- 透明性: 通过 requirements.txt 可视化依赖关系
- AI 对应: 丰富的库信息可供 AI 使用

#### 需要注意的语言
**Java/C# 等编译型语言**
- 理由: jar 和 ddl 二进制分发导致 AI 难以动态调研依赖关系
- 对应: 可以使用但需要事前准备和人工辅助

### 项目类型

#### 最优项目
- **以 CRUD 操作为中心的应用**
- **Web API 开发**
- **数据库联动应用**
- **比较大规模的项目**

#### 有效的代码模式
- 需要创建大量相似代码的场景
- 容易模板化的处理
- 标准设计模式的实现

## 成本管理

### 预期成本
- **Claude Pro**: $20/月
- **GitHub**: 个人使用免费，团队使用 $4/用户/月
- **VS Code**: 免费
- **其他**: 项目特定依赖成本

### 成本优化要点
1. **Claude Pro 上限设置**: 设置最高 $200
2. **高效使用**: 通过明确目标设定优化 AI 使用量
3. **Git 历史管理**: 通过适当提交策略避免无用试错

## 配置完成确认

检查以下项目，确认配置已完成：

- [ ] Claude Pro 计划已启用
- [ ] 可访问 Claude Code
- [ ] VS Code 已安装
- [ ] 基本 VS Code 扩展已安装
- [ ] Git 已设置
- [ ] GitHub 账号准备完成
- [ ] 已创建项目目录
- [ ] 选定技术栈的基本环境已准备

## 故障排除

### 常见问题与解决方案

**无法访问 Claude Code**
- 确认 Pro 计划有效性
- 清除浏览器缓存
- 确认网络连接

**VS Code 集成有问题**
- 重新安装 Claude Code 插件
- 重启 VS Code
- 确认设置文件

**Git 操作出现错误**
- 重新设置认证信息
- 确认远程仓库 URL
- 确认访问权限

## 下一步

工具配置完成后，在下一章"2.2 Claude Sonnet 4 的使用方法"中学习 AI 工具的具体使用方法。通过掌握有效的提示词设计和与 AI 协作的技巧，能够发挥 AITDD 的真正价值。
