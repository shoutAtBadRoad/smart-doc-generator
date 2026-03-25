# 智能 Word 文档生成系统 - 全面改造方案

## 📋 项目概述

基于现有项目的功能基础，完全重构为一个**多步骤、可交互、AI 驱动**的智能文档生成平台。

### 核心改造目标

1. **模板化管理**：文档格式模板的全生命周期管理（CRUD）
2. **架构智能化**：文档架构的 AI 分析与生成
3. **格式自动化**：文档元素格式的自动匹配与应用
4. **内容生成**：基于架构的智能内容填充
5. **交互式流程**：分步确认、进度追踪、任务恢复
6. **前端界面**：美观友好的 HTML 操作界面

---

## 🏗️ 系统架构设计

### 整体架构图

```
┌─────────────────────────────────────────────────────────────┐
│                     前端界面层 (HTML/JS/CSS)                  │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐       │
│  │ 模板管理 │ │ 架构设计 │ │ 内容生成 │ │ 文件管理 │       │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘       │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│                      REST API 控制层                          │
│  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐        │
│  │TemplateCtrl  │ │ArchCtrl      │ │ContentCtrl   │        │
│  └──────────────┘ └──────────────┘ └──────────────┘        │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│                       业务服务层                              │
│  ┌────────────┐ ┌────────────┐ ┌────────────┐ ┌─────────┐  │
│  │TemplateSvc │ │ArchSvc     │ │ContentSvc  │ │TaskMgr  │  │
│  └────────────┘ └────────────┘ └────────────┘ └─────────┘  │
│  ┌────────────┐ ┌────────────┐ ┌────────────┐              │
│  │FormatMatch │ │JSONValidator│ │RetryHandler│              │
│  └────────────┘ └────────────┘ └────────────┘              │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│                      数据持久层                               │
│  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐        │
│  │TemplateRepo  │ │ArchRepo      │ │TaskCacheRepo │        │
│  └──────────────┘ └──────────────┘ └──────────────┘        │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│                     AI 引擎层 (Ollama)                        │
│  ┌────────────┐ ┌────────────┐ ┌────────────┐               │
│  │TemplateGen │ │ArchAnalyzer│ │ContentFill │               │
│  └────────────┘ └────────────┘ └────────────┘               │
└─────────────────────────────────────────────────────────────┘
```

---

## 📦 核心功能模块设计

### 1. 文档格式模板管理模块

#### 功能清单
- ✅ 模板列表查询（支持分类筛选）
- ✅ 模板详情查看（JSON 预览 + 可视化预览）
- ✅ 模板创建（手动编辑 + AI 智能生成）
- ✅ 模板编辑（实时预览）
- ✅ 模板删除（软删除机制）
- ✅ 模板导入/导出（JSON 格式）

#### 数据模型
```json
{
  "id": "uuid",
  "name": "模板名称",
  "description": "模板描述",
  "category": "技术文档|合同|报告|论文|其他",
  "formatSpec": {
    "pageSetup": {
      "paperSize": "A4",
      "orientation": "portrait|landscape",
      "marginTop": 2.54,
      "marginBottom": 2.54,
      "marginLeft": 3.17,
      "marginRight": 3.17
    },
    "fonts": {
      "defaultFont": "宋体",
      "defaultSize": 12,
      "heading1Font": "黑体",
      "heading1Size": 16,
      "heading2Font": "黑体",
      "heading2Size": 14,
      "heading3Font": "黑体",
      "heading3Size": 12
    },
    "paragraphs": {
      "lineSpacing": 1.5,
      "spacingBefore": 0,
      "spacingAfter": 6,
      "indentFirstLine": true,
      "alignment": "justify"
    },
    "headerFooter": {
      "showHeader": true,
      "headerText": "",
      "showFooter": true,
      "showPageNumber": true,
      "pageNumberPosition": "center"
    },
    "tableStyle": {
      "borderStyle": "single",
      "borderWidth": 0.5,
      "headerRowStyle": {
        "bold": true,
        "backgroundColor": "#D3D3D3"
      }
    },
    "coverPage": {
      "enabled": true,
      "titleFont": "微软雅黑",
      "titleSize": 24,
      "showAuthor": true,
      "showDate": true,
      "showDepartment": true
    }
  },
  "createdAt": "timestamp",
  "updatedAt": "timestamp",
  "isDefault": false
}
```

#### AI 生成 Prompt 示例
```
请根据以下自然语言描述，生成一个完整的 Word 文档格式模板（JSON 格式）：

用户需求：「我需要一份标准的学术论文模板，A4 纸，页边距上下 2.54cm，左右 3.17cm。
标题使用黑体，正文使用宋体小四号字，1.5 倍行距。需要封面和页码。」

要求：
1. 输出严格的 JSON 格式
2. 包含所有页面设置、字体、段落、页眉页脚配置
3. 符合学术论文的格式规范
4. temperature: 0.3（确保格式一致性）
```

---

### 2. 文档架构管理模块

#### 功能清单
- ✅ 架构列表查询
- ✅ 架构详情查看（树形结构展示）
- ✅ 架构创建（手动构建 + AI 智能分析）
- ✅ 架构编辑（拖拽式层级调整）
- ✅ 架构删除
- ✅ 架构导入/导出（JSON 格式）

#### 数据模型
```json
{
  "id": "uuid",
  "name": "架构名称",
  "templateId": "关联的模板 ID",
  "structure": {
    "metadata": {
      "title": "文档标题",
      "author": "作者",
      "date": "2024-01-01",
      "version": "1.0",
      "department": "部门"
    },
    "sections": [
      {
        "id": "section-1",
        "title": "第一章 引言",
        "level": 1,
        "order": 1,
        "description": "介绍研究背景和意义",
        "wordCount": 1000,
        "elementType": "chapter",
        "children": [
          {
            "id": "section-1-1",
            "title": "1.1 研究背景",
            "level": 2,
            "order": 1,
            "description": "阐述当前领域的现状",
            "wordCount": 500,
            "elementType": "section"
          }
        ]
      }
    ],
    "tables": [
      {
        "id": "table-1",
        "caption": "表 1-1 数据统计表",
        "sectionId": "section-1",
        "columns": [
          {"name": "指标", "width": 100},
          {"name": "数值", "width": 80}
        ],
        "rowCount": 10
      }
    ],
    "figures": [
      {
        "id": "figure-1",
        "caption": "图 1-1 技术路线图",
        "sectionId": "section-1",
        "type": "image|chart|diagram"
      }
    ],
    "lists": [
      {
        "id": "list-1",
        "type": "ordered|unordered",
        "sectionId": "section-1",
        "items": ["项 1", "项 2", "项 3"]
      }
    ]
  },
  "createdAt": "timestamp",
  "updatedAt": "timestamp"
}
```

#### AI 分析 Prompt 示例
```
请根据以下需求描述，生成一个合理的文档架构（JSON 格式）：

用户需求：「我需要写一份关于人工智能在医疗领域应用的技术报告，
包括行业现状、技术分析、应用场景、挑战与展望。」

要求：
1. 输出严格的 JSON 格式
2. 包含至少 5 个主要章节，每个章节有 2-3 个小节
3. 标注每个章节的建议字数
4. 识别可能需要表格或图表的位置
5. temperature: 0.3
```

---

### 3. 文档架构格式智能分析模块

#### 功能清单
- ✅ 格式自动匹配（根据模板规格）
- ✅ 格式预览（带格式的架构展示）
- ✅ 格式手动调整
- ✅ 格式冲突检测与提示

#### 处理流程
```
1. 读取已选择的文档格式模板
2. 遍历文档架构的每个元素
3. 根据元素类型（标题、段落、表格等）匹配对应格式
4. 生成带格式标注的架构树
5. 提供可视化预览
```

#### 格式映射规则示例
```json
{
  "elementTypeMappings": {
    "chapter": {
      "fontFamily": "黑体",
      "fontSize": 16,
      "bold": true,
      "spacingBefore": 18,
      "spacingAfter": 6,
      "pageBreakBefore": true
    },
    "section": {
      "fontFamily": "黑体",
      "fontSize": 14,
      "bold": true,
      "spacingBefore": 12,
      "spacingAfter": 6
    },
    "paragraph": {
      "fontFamily": "宋体",
      "fontSize": 12,
      "lineSpacing": 1.5,
      "indentFirstLine": true
    },
    "table": {
      "borderStyle": "single",
      "borderWidth": 0.5,
      "headerBold": true,
      "headerBackgroundColor": "#D3D3D3"
    }
  }
}
```

---

### 4. 文档内容智能填充模块

#### 功能清单
- ✅ 章节内容生成（调用 AI）
- ✅ 表格数据填充
- ✅ 列表内容生成
- ✅ 内容质量校验
- ✅ Word 文档生成与下载

#### AI 内容生成 Prompt 示例
```
请根据以下章节信息，生成详细的文档内容：

章节标题：「1.1 研究背景」
章节描述：「阐述当前人工智能在医疗领域的应用现状」
建议字数：500 字
相关关键词：["人工智能", "医疗", "诊断", "影像识别"]

要求：
1. 内容专业、准确、逻辑清晰
2. 使用学术性语言
3. 避免空泛描述，提供具体案例
4. temperature: 0.5（平衡创造性与准确性）
```

---

### 5. 任务管理与缓存模块

#### 功能清单
- ✅ 任务状态追踪（待处理→进行中→已完成/失败）
- ✅ 步骤缓存（记录每步执行结果）
- ✅ 任务恢复（断点续传）
- ✅ 失败重试（自动 + 手动）
- ✅ 进度实时推送

#### 任务状态模型
```json
{
  "taskId": "uuid",
  "userId": "user-uuid",
  "taskType": "document_generation",
  "status": "pending|running|completed|failed|paused",
  "currentStep": "content_generation",
  "progress": 65,
  "steps": [
    {
      "stepName": "template_selection",
      "status": "completed",
      "result": {"templateId": "xxx"},
      "executedAt": "timestamp",
      "apiCalls": [
        {
          "api": "/api/ollama/generate",
          "method": "POST",
          "params": {"model": "qwen2.5:7b", "prompt": "..."},
          "response": "{...}",
          "duration": 2345
        }
      ]
    },
    {
      "stepName": "architecture_analysis",
      "status": "completed",
      "result": {"architectureId": "yyy"},
      "executedAt": "timestamp"
    },
    {
      "stepName": "content_generation",
      "status": "running",
      "progress": 30,
      "startedAt": "timestamp"
    }
  ],
  "retries": 0,
  "maxRetries": 3,
  "createdAt": "timestamp",
  "updatedAt": "timestamp",
  "errorMessage": null
}
```

---

### 6. 前端界面设计

#### 页面结构
```
首页（Dashboard）
├── 模板管理
│   ├── 模板列表
│   ├── 模板创建
│   └── 模板详情/编辑
├── 架构设计
│   ├── 架构列表
│   ├── 架构创建
│   └── 架构详情/编辑
├── 文档生成向导
│   ├── 步骤 1：选择模板
│   ├── 步骤 2：设计架构
│   ├── 步骤 3：生成内容
│   └── 步骤 4：预览下载
├── 任务中心
│   ├── 进行中的任务
│   ├── 已完成的任务
│   └── 失败的任务
└── 文件管理
    ├── 我的文档
    └── 文档下载
```

#### 关键技术特性
- **响应式设计**：适配桌面和移动端
- **实时更新**：WebSocket 推送任务进度
- **拖拽编辑**：架构树拖拽排序
- **代码高亮**：JSON 预览语法高亮
- **富文本预览**：Markdown 渲染

---

## 🔧 技术栈选型

### 后端技术
- **框架**: Spring Boot 2.7.x
- **数据库**: H2 / MySQL（可选）
- **缓存**: Redis / Caffeine
- **AI 集成**: Ollama API Client
- **文档处理**: Apache POI 5.x
- **JSON 处理**: Jackson
- **重试机制**: Spring Retry

### 前端技术
- **框架**: Vue.js 3.x（轻量级）
- **UI 组件**: Element Plus
- **HTTP 客户端**: Axios
- **状态管理**: Pinia
- **代码高亮**: Prism.js
- **Markdown 渲染**: marked

---

## 📝 数据库设计

### 核心表结构

#### 1. template_registry（模板注册表）
```sql
CREATE TABLE template_registry (
    id VARCHAR(36) PRIMARY KEY,
    name VARCHAR(200) NOT NULL,
    description TEXT,
    category VARCHAR(50),
    format_spec JSON NOT NULL,
    is_default BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted BOOLEAN DEFAULT FALSE
);
```

#### 2. document_architecture（文档架构表）
```sql
CREATE TABLE document_architecture (
    id VARCHAR(36) PRIMARY KEY,
    name VARCHAR(200) NOT NULL,
    template_id VARCHAR(36),
    structure JSON NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (template_id) REFERENCES template_registry(id)
);
```

#### 3. task_cache（任务缓存表）
```sql
CREATE TABLE task_cache (
    id VARCHAR(36) PRIMARY KEY,
    user_id VARCHAR(36),
    task_type VARCHAR(50),
    status VARCHAR(20),
    current_step VARCHAR(50),
    progress INT DEFAULT 0,
    steps JSON,
    retries INT DEFAULT 0,
    error_message TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### 4. generated_files（生成文件表）
```sql
CREATE TABLE generated_files (
    id VARCHAR(36) PRIMARY KEY,
    task_id VARCHAR(36),
    filename VARCHAR(500),
    file_path VARCHAR(1000),
    file_size BIGINT,
    download_count INT DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (task_id) REFERENCES task_cache(id)
);
```

---

## 🚀 实施步骤

### 第一阶段：基础设施搭建（预计 3 天）
1. ✅ 创建项目分支
2. ✅ 添加数据库依赖（H2/JPA）
3. ✅ 创建实体类和 Repository
4. ✅ 配置数据库连接
5. ✅ 初始化默认模板数据

### 第二阶段：模板管理模块（预计 3 天）
1. ✅ 实现 TemplateService
2. ✅ 实现 TemplateController
3. ✅ 开发 AI 模板生成功能
4. ✅ 实现模板预览接口
5. ✅ 编写单元测试

### 第三阶段：架构管理模块（预计 4 天）
1. ✅ 实现 ArchitectureService
2. ✅ 实现 ArchitectureController
3. ✅ 开发 AI 架构分析功能
4. ✅ 实现架构树形结构编辑
5. ✅ 编写单元测试

### 第四阶段：格式匹配引擎（预计 3 天）
1. ✅ 实现 FormatMatcherService
2. ✅ 开发格式映射规则引擎
3. ✅ 实现格式预览接口
4. ✅ 开发格式冲突检测
5. ✅ 编写测试用例

### 第五阶段：内容生成模块（预计 4 天）
1. ✅ 实现 ContentGenerationService
2. ✅ 实现 ContentController
3. ✅ 开发 AI 内容填充功能
4. ✅ 增强 Word 文档生成逻辑
5. ✅ 实现文件下载接口

### 第六阶段：任务管理系统（预计 4 天）
1. ✅ 实现 TaskManagerService
2. ✅ 实现 RetryHandler
3. ✅ 开发任务缓存机制
4. ✅ 实现进度追踪接口
5. ✅ 开发任务恢复功能

### 第七阶段：前端界面开发（预计 7 天）
1. ✅ 搭建 Vue.js 项目结构
2. ✅ 开发模板管理页面
3. ✅ 开发架构管理页面
4. ✅ 开发文档生成向导
5. ✅ 开发任务中心页面
6. ✅ 开发文件管理页面
7. ✅ UI/UX优化

### 第八阶段：集成测试与优化（预计 4 天）
1. ✅ 端到端测试
2. ✅ 性能优化
3. ✅ Bug 修复
4. ✅ 文档编写

---

## 🎯 关键功能实现细节

### 1. AI 生成的 JSON 校验与重试机制

```java
@Service
public class JsonValidationService {
    
    private static final int MAX_RETRIES = 3;
    private final ObjectMapper objectMapper;
    private final OllamaService ollamaService;
    
    public <T> T generateWithRetry(String prompt, Class<T> clazz) {
        for (int i = 0; i < MAX_RETRIES; i++) {
            try {
                String jsonResponse = ollamaService.generate(prompt);
                
                // 清洗 JSON（移除 Markdown 标记）
                jsonResponse = cleanJsonResponse(jsonResponse);
                
                // 严格校验
                T result = objectMapper.readValue(jsonResponse, clazz);
                validateStructure(result);
                
                return result;
                
            } catch (Exception e) {
                logger.warn("第 {} 次生成失败：{}", i + 1, e.getMessage());
                if (i == MAX_RETRIES - 1) {
                    throw new GenerationFailedException("多次重试后仍无法生成有效的 JSON");
                }
            }
        }
        return null;
    }
    
    private String cleanJsonResponse(String response) {
        // 移除 ```json 和 ``` 标记
        response = response.replaceAll("```json\\s*", "");
        response = response.replaceAll("```\\s*", "");
        return response.trim();
    }
}
```

### 2. 任务进度实时推送（WebSocket）

```java
@ServerEndpoint("/ws/progress/{taskId}")
@Component
public class ProgressWebSocket {
    
    private static ConcurrentHashMap<String, Session> sessions = new ConcurrentHashMap<>();
    
    @OnOpen
    public void onOpen(Session session, @PathParam("taskId") String taskId) {
        sessions.put(taskId, session);
    }
    
    public void sendProgress(String taskId, int progress, String message) {
        Session session = sessions.get(taskId);
        if (session != null && session.isOpen()) {
            JsonObject msg = new JsonObject();
            msg.addProperty("taskId", taskId);
            msg.addProperty("progress", progress);
            msg.addProperty("message", message);
            session.getAsyncRemote().sendText(msg.toString());
        }
    }
}
```

### 3. 低显存优化（分块处理）

```java
@Service
public class ContentChunkingService {
    
    private static final int CHUNK_SIZE = 2500; // 汉字阈值
    private static final int OVERLAP_SIZE = 300;
    
    public List<String> chunkSections(List<SectionConfig> sections) {
        List<String> chunks = new ArrayList<>();
        
        for (SectionConfig section : sections) {
            String content = section.getDescription();
            
            if (content.length() > CHUNK_SIZE) {
                // 智能分块（保持段落完整性）
                List<String> sectionChunks = intelligentChunk(content);
                chunks.addAll(sectionChunks);
            } else {
                chunks.add(content);
            }
        }
        
        return chunks;
    }
    
    private List<String> intelligentChunk(String text) {
        // 按段落分割，合并到合适大小
        // 设置重叠区保持上下文连续性
    }
}
```

---

## 📊 默认模板库

### 预置模板清单

#### 1. 技术报告模板
- A4 纸张，纵向
- 封面 + 目录 + 正文
- 三级标题体系
- 包含图表索引

#### 2. 学术论文模板
- A4 纸张，双面打印设置
- 中英文摘要
- 参考文献格式
- 致谢部分

#### 3. 商务合同模板
- 条款编号格式
- 签署区域布局
- 附件管理规范

#### 4. 项目周报模板
- 简洁单页设计
- 进度表格
- 风险标识区域

#### 5. 产品说明书模板
- 多栏排版
- 安全警示框
- 附录索引

---

## 📈 性能优化策略

### 1. 缓存策略
- 模板数据：Redis 缓存（1 小时）
- 任务状态：Caffeine 本地缓存（实时）
- 生成内容：文件系统缓存（永久）

### 2. 异步处理
- 耗时操作：@Async 异步执行
- 批量生成：线程池并行处理
- 文件下载：流式传输

### 3. 资源管理
- 大文件：分块上传/下载
- 内存控制：流式处理避免 OOM
- 连接池：HTTP 连接复用

---

## 🔒 安全性考虑

### 1. API 安全
- CORS 跨域限制
- JWT Token 认证（可选）
- 请求频率限制

### 2. 数据安全
- SQL 注入防护（JPA 参数化）
- XSS 攻击防护（输入过滤）
- 文件上传校验（类型 + 大小）

### 3. 隐私保护
- 用户数据隔离
- 敏感信息加密存储
- 日志脱敏处理

---

## 📚 交付成果清单

### 代码仓库
- [x] 完整的源代码
- [x] 数据库迁移脚本
- [x] Docker 配置文件
- [x] CI/CD 配置（GitHub Actions）

### 文档
- [x] 系统设计文档
- [x] API 接口文档（Swagger）
- [x] 部署指南
- [x] 用户操作手册
- [x] 开发规范文档

### 测试
- [x] 单元测试（覆盖率>80%）
- [x] 集成测试
- [x] 性能测试报告

---

## 🎉 总结

本次改造将现有单一功能的文档生成工具，升级为**企业级智能文档生成平台**，具备以下核心优势：

1. **模块化设计**：各功能模块解耦，易于扩展和维护
2. **AI 深度集成**：充分利用大模型能力，实现智能化生成
3. **用户体验优先**：交互式流程、实时反馈、可视化预览
4. **生产就绪**：完善的错误处理、重试机制、任务恢复
5. **可扩展性**：支持自定义模板、插件化架构

通过分阶段实施，确保每一步都经过充分测试，最终交付高质量的生产系统。
