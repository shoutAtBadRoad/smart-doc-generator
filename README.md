# 智能 Word 文档生成系统

<div align="center">

**AI 驱动的多步骤智能文档生成平台**

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-2.7.x-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![Vue.js](https://img.shields.io/badge/Vue.js-3.x-green.svg)](https://vuejs.org/)

📋 [改造方案](TRANSFORMATION_PLAN.md) | 🚀 [实施计划](IMPLEMENTATION_PHASE1.md) | 📖 [执行摘要](README_EXECUTIVE_SUMMARY.md)

</div>

---

## 🌟 项目简介

这是一个**企业级智能文档生成系统**，利用 AI 大模型（Ollama）实现从需求描述到完整 Word 文档的全自动生成。

### 核心特性

✨ **模板智能管理** - 支持 CRUD 操作 + AI 自动生成文档格式模板  
🏗️ **架构自动分析** - 根据自然语言描述生成合理的文档架构  
🎨 **格式自动匹配** - 基于模板为文档元素智能应用格式  
📝 **内容智能填充** - 根据架构章节自动生成专业内容  
🔄 **流程可控制** - 分步确认、进度追踪、断点续传  
🖥️ **友好界面** - 响应式 HTML 前端，简洁美观  

---

## 🎯 功能模块

### 1. 文档格式模板管理
- ✅ 模板列表查询与分类筛选
- ✅ 模板详情查看（JSON + 可视化预览）
- ✅ 模板创建（手动编辑 + AI 智能生成）
- ✅ 模板编辑与实时预览
- ✅ 模板删除（软删除机制）
- ✅ 模板导入/导出（JSON 格式）
- ✅ 预置 5+ 种常用模板（技术报告、学术论文、商务合同等）

### 2. 文档架构管理
- ✅ 架构树形结构展示
- ✅ 架构创建（手动构建 + AI 分析生成）
- ✅ 拖拽式层级调整
- ✅ 章节字数分配与建议
- ✅ 表格/图表位置标识
- ✅ 架构导入/导出

### 3. 格式智能匹配
- ✅ 基于模板自动匹配格式
- ✅ 元素格式预览
- ✅ 格式冲突检测
- ✅ 手动格式调整

### 4. 内容智能生成
- ✅ 章节内容 AI 填充
- ✅ 表格数据生成
- ✅ 列表内容生成
- ✅ 内容质量校验
- ✅ Word 文档生成

### 5. 任务管理中心
- ✅ 任务状态追踪（待处理→进行中→已完成）
- ✅ 步骤缓存与断点恢复
- ✅ 失败重试（自动 + 手动）
- ✅ 实时进度推送（WebSocket）
- ✅ API 调用日志记录

### 6. 文件管理
- ✅ 生成文件列表
- ✅ 文件下载
- ✅ 下载次数统计
- ✅ 文件预览（可选）

---

## 🏗️ 系统架构

```
┌─────────────────────────────────────────┐
│   前端界面层 (Vue.js 3 + Element Plus)    │
└──────────────┬──────────────────────────┘
               │ REST API
┌──────────────▼──────────────────────────┐
│   Spring Boot 控制层 (Controllers)       │
└──────────────┬──────────────────────────┘
               │
┌──────────────▼──────────────────────────┐
│   业务服务层 (Services + Retry)          │
│   ├─ TemplateService                    │
│   ├─ ArchitectureService                │
│   ├─ ContentGenerationService           │
│   └─ TaskManagerService                 │
└──────────────┬──────────────────────────┘
               │
┌──────────────▼──────────────────────────┐
│   数据持久层 (JPA Repository)            │
│   ├─ H2 Database (开发)                  │
│   └─ MySQL (生产)                        │
└──────────────┬──────────────────────────┘
               │
┌──────────────▼──────────────────────────┐
│   AI 引擎层 (Ollama API)                 │
│   ├─ qwen2.5:7b (默认)                   │
│   └─ 其他模型（可配置）                   │
└─────────────────────────────────────────┘
```

---

## 🛠️ 技术栈

### 后端
- **框架**: Spring Boot 2.7.x
- **数据库**: H2 (开发) / MySQL 8.0+ (生产)
- **ORM**: Spring Data JPA
- **AI 集成**: Ollama API Client (Apache HttpClient)
- **文档处理**: Apache POI 5.x
- **JSON**: Jackson
- **重试**: Spring Retry
- **工具**: Lombok, Validation API

### 前端（规划中）
- **框架**: Vue.js 3.x
- **UI**: Element Plus
- **HTTP**: Axios
- **状态**: Pinia
- **高亮**: Prism.js
- **Markdown**: marked

---

## 📦 快速开始（当前阶段）

### 前置要求

- JDK 1.8+
- Maven 3.6+
- IDE（推荐 IntelliJ IDEA / VS Code）

### 克隆现有代码

```bash
cd c:\workspace\java\demo\demo
```

当前项目已具备基础功能：
- ✅ Ollama API 集成
- ✅ 基础文档生成服务
- ✅ SmartDocumentService（智能文档生成）
- ✅ WordDocumentService（Word 文档处理）

### 下一步改造

按照 [IMPLEMENTATION_PHASE1.md](IMPLEMENTATION_PHASE1.md) 的指引逐步实施：

1. **添加数据库依赖** - 修改 `pom.xml`
2. **配置数据库连接** - 修改 `application.yml`
3. **创建实体类** - 4 个核心实体
4. **创建 Repository** - 数据访问层
5. **初始化数据** - 加载默认模板

---

## 📊 数据库设计

### 核心表结构

#### template_registry - 模板注册表
```sql
CREATE TABLE template_registry (
    id VARCHAR(36) PRIMARY KEY,
    name VARCHAR(200) NOT NULL,
    description TEXT,
    category VARCHAR(50),
    format_spec JSON NOT NULL,
    is_default BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP,
    updated_at TIMESTAMP,
    deleted BOOLEAN DEFAULT FALSE
);
```

#### document_architecture - 文档架构表
```sql
CREATE TABLE document_architecture (
    id VARCHAR(36) PRIMARY KEY,
    name VARCHAR(200) NOT NULL,
    template_id VARCHAR(36),
    structure JSON NOT NULL,
    created_at TIMESTAMP,
    updated_at TIMESTAMP
);
```

#### task_cache - 任务缓存表
```sql
CREATE TABLE task_cache (
    id VARCHAR(36) PRIMARY KEY,
    user_id VARCHAR(36),
    task_type VARCHAR(50),
    status VARCHAR(20),
    current_step VARCHAR(50),
    progress INT,
    steps JSON,
    retries INT,
    error_message TEXT,
    created_at TIMESTAMP,
    updated_at TIMESTAMP
);
```

#### generated_files - 生成文件表
```sql
CREATE TABLE generated_files (
    id VARCHAR(36) PRIMARY KEY,
    task_id VARCHAR(36),
    filename VARCHAR(500),
    file_path VARCHAR(1000),
    file_size BIGINT,
    download_count INT,
    created_at TIMESTAMP
);
```

---

## 🔧 配置说明

### Ollama 配置（已有）

```yaml
ollama:
  api-url: http://localhost:11434/api/generate
  model: qwen2.5:7b
  username: your_username
  password: your_password
```

### 数据库配置（新增）

```yaml
spring:
  datasource:
    url: jdbc:h2:mem:docdb;DB_CLOSE_DELAY=-1
    driver-class-name: org.h2.Driver
    username: sa
    password: 
  
  h2:
    console:
      enabled: true
      path: /h2-console
  
  jpa:
    database-platform: org.hibernate.dialect.H2Dialect
    hibernate:
      ddl-auto: update
```

---

## 📁 项目结构

```
demo/
├── src/main/java/com/example/docgen/
│   ├── config/              # 配置类
│   │   └── OllamaConfig.java
│   ├── controller/          # 控制器
│   │   └── DocumentController.java
│   ├── entity/              # 实体类（新增）
│   │   ├── TemplateRegistry.java
│   │   ├── DocumentArchitecture.java
│   │   ├── TaskCache.java
│   │   └── GeneratedFile.java
│   ├── repository/          # Repository（新增）
│   │   ├── TemplateRepository.java
│   │   ├── ArchitectureRepository.java
│   │   ├── TaskCacheRepository.java
│   │   └── GeneratedFileRepository.java
│   ├── dto/                 # DTO（新增）
│   │   └── TemplateDTO.java
│   ├── service/             # 服务层
│   │   ├── OllamaService.java
│   │   ├── SmartDocumentService.java
│   │   ├── WordDocumentService.java
│   │   ├── TemplateService.java（新增）
│   │   ├── ArchitectureService.java（新增）
│   │   └── ContentGenerationService.java（新增）
│   ├── util/                # 工具类
│   │   └── TextChunker.java
│   └── DocumentGeneratorApplication.java
├── src/main/resources/
│   ├── static/              # 静态资源
│   │   └── index.html
│   ├── data/                # 初始化数据
│   │   └── templates/       # 默认模板 JSON
│   └── application.yml      # 配置文件
└── pom.xml                  # Maven 配置
```

---

## 🚀 实施路线图

### Phase 1 - 基础设施搭建 🔴 当前阶段
- [ ] 添加数据库依赖
- [ ] 配置数据库连接
- [ ] 创建实体类和 Repository
- [ ] 初始化默认数据
- **预计时间**: 3 天

### Phase 2 - 模板管理模块
- [ ] 实现 TemplateService
- [ ] 实现 TemplateController
- [ ] AI 模板生成功能
- [ ] 模板预览接口
- **预计时间**: 3 天

### Phase 3 - 架构管理模块
- [ ] 实现 ArchitectureService
- [ ] 实现 ArchitectureController
- [ ] AI 架构分析功能
- [ ] 架构树形编辑器
- **预计时间**: 4 天

### Phase 4 - 格式匹配引擎
- [ ] 实现 FormatMatcherService
- [ ] 格式映射规则引擎
- [ ] 格式预览接口
- **预计时间**: 3 天

### Phase 5 - 内容生成模块
- [ ] 实现 ContentGenerationService
- [ ] AI 内容填充
- [ ] Word 文档增强
- **预计时间**: 4 天

### Phase 6 - 任务管理系统
- [ ] 实现 TaskManagerService
- [ ] 重试机制
- [ ] WebSocket 进度推送
- **预计时间**: 4 天

### Phase 7 - 前端界面开发
- [ ] Vue.js 项目搭建
- [ ] 各功能页面开发
- [ ] UI/UX 优化
- **预计时间**: 7 天

### Phase 8 - 测试与优化
- [ ] 集成测试
- [ ] 性能优化
- [ ] Bug 修复
- **预计时间**: 4 天

**总预计**: 32 天完成全部改造

---

## 📚 文档索引

### 设计文档
- [完整改造方案](TRANSFORMATION_PLAN.md) - 详细的技术设计和实现方案
- [第一阶段实施](IMPLEMENTATION_PHASE1.md) - 具体的每日任务清单
- [执行摘要](README_EXECUTIVE_SUMMARY.md) - 快速了解项目概况

### 现有文档（项目原有）
- OLLAMA_README.md - Ollama 集成说明
- SMART_DOCUMENT_GUIDE.md - 智能文档指南
- WORD_TEMPLATE_GUIDE.md - Word 模板指南

---

## 🔒 安全性

- SQL 注入防护（JPA 参数化查询）
- XSS 攻击防护（输入过滤）
- CORS 跨域限制
- 文件上传校验
- 敏感信息加密存储

---

## 🤝 贡献指南

欢迎提交 Issue 和 Pull Request！

---

## 📄 License

MIT License

---

## 📞 联系方式

GitHub: [@shoutAtBadRoad](https://github.com/shoutAtBadRoad)

---

<div align="center">

**开始改造之旅** → [查看实施计划](IMPLEMENTATION_PHASE1.md)

</div>
