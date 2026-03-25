# 第一阶段实施清单 - 基础设施搭建

## 📋 任务列表

### Day 1: 项目初始化与数据库配置

#### 1.1 创建项目分支结构
- [x] 创建 GitHub 仓库
- [ ] 创建开发分支 `develop`
- [ ] 创建功能分支：
  - `feature/template-management`
  - `feature/architecture-management`
  - `feature/content-generation`
  - `feature/task-management`
  - `feature/frontend-ui`

#### 1.2 添加数据库依赖
```xml
<!-- pom.xml -->
<!-- Spring Data JPA -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<!-- H2 Database (开发环境) -->
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>

<!-- MySQL Connector (生产环境可选) -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.33</version>
</dependency>

<!-- Lombok (简化实体类) -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
</dependency>

<!-- Validation API -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

#### 1.3 配置数据库连接
```yaml
# application.yml
spring:
  datasource:
    url: jdbc:h2:mem:docdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
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
    show-sql: false
    properties:
      hibernate:
        format_sql: true
  
  # 生产环境 MySQL 配置（注释备用）
  # datasource:
  #   url: jdbc:mysql://localhost:3306/smart_doc?useSSL=false&serverTimezone=UTC&characterEncoding=utf8
  #   driver-class-name: com.mysql.cj.jdbc.Driver
  #   username: root
  #   password: your_password
```

#### 1.4 创建数据库初始化脚本
```sql
-- data.sql (可选，用于初始化默认数据)
INSERT INTO template_registry (id, name, description, category, format_spec, is_default) VALUES
('tpl-001', '技术报告模板', '标准的技术报告格式', 'REPORT', '{...}', TRUE),
('tpl-002', '学术论文模板', '学术论文标准格式', 'THESIS', '{...}', TRUE);
```

---

### Day 2: 实体类与 Repository 层

#### 2.1 创建实体类

##### TemplateRegistry.java
```java
package com.example.docgen.entity;

import lombok.*;
import javax.persistence.*;
import java.time.LocalDateTime;

@Entity
@Table(name = "template_registry")
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class TemplateRegistry {
    
    @Id
    @Column(length = 36)
    private String id;
    
    @Column(nullable = false, length = 200)
    private String name;
    
    @Column(columnDefinition = "TEXT")
    private String description;
    
    @Column(length = 50)
    private String category;
    
    @Column(columnDefinition = "JSON", nullable = false)
    private String formatSpec;
    
    @Column(name = "is_default")
    private Boolean isDefault = false;
    
    @Column(name = "created_at")
    private LocalDateTime createdAt;
    
    @Column(name = "updated_at")
    private LocalDateTime updatedAt;
    
    @Column(name = "deleted")
    private Boolean deleted = false;
    
    @PrePersist
    protected void onCreate() {
        createdAt = LocalDateTime.now();
        updatedAt = LocalDateTime.now();
        if (id == null) {
            id = java.util.UUID.randomUUID().toString();
        }
    }
    
    @PreUpdate
    protected void onUpdate() {
        updatedAt = LocalDateTime.now();
    }
}
```

##### DocumentArchitecture.java
```java
package com.example.docgen.entity;

import lombok.*;
import javax.persistence.*;
import java.time.LocalDateTime;

@Entity
@Table(name = "document_architecture")
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class DocumentArchitecture {
    
    @Id
    @Column(length = 36)
    private String id;
    
    @Column(nullable = false, length = 200)
    private String name;
    
    @Column(name = "template_id", length = 36)
    private String templateId;
    
    @Column(columnDefinition = "JSON", nullable = false)
    private String structure;
    
    @Column(name = "created_at")
    private LocalDateTime createdAt;
    
    @Column(name = "updated_at")
    private LocalDateTime updatedAt;
    
    @PrePersist
    protected void onCreate() {
        createdAt = LocalDateTime.now();
        updatedAt = LocalDateTime.now();
        if (id == null) {
            id = java.util.UUID.randomUUID().toString();
        }
    }
    
    @PreUpdate
    protected void onUpdate() {
        updatedAt = LocalDateTime.now();
    }
}
```

##### TaskCache.java
```java
package com.example.docgen.entity;

import lombok.*;
import javax.persistence.*;
import java.time.LocalDateTime;

@Entity
@Table(name = "task_cache")
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class TaskCache {
    
    @Id
    @Column(length = 36)
    private String id;
    
    @Column(name = "user_id", length = 36)
    private String userId;
    
    @Column(name = "task_type", length = 50)
    private String taskType;
    
    @Column(length = 20)
    private String status;
    
    @Column(name = "current_step", length = 50)
    private String currentStep;
    
    @Column
    private Integer progress = 0;
    
    @Column(columnDefinition = "JSON")
    private String steps;
    
    @Column
    private Integer retries = 0;
    
    @Column(name = "error_message", columnDefinition = "TEXT")
    private String errorMessage;
    
    @Column(name = "created_at")
    private LocalDateTime createdAt;
    
    @Column(name = "updated_at")
    private LocalDateTime updatedAt;
    
    @PrePersist
    protected void onCreate() {
        createdAt = LocalDateTime.now();
        updatedAt = LocalDateTime.now();
        if (id == null) {
            id = java.util.UUID.randomUUID().toString();
        }
    }
    
    @PreUpdate
    protected void onUpdate() {
        updatedAt = LocalDateTime.now();
    }
}
```

##### GeneratedFile.java
```java
package com.example.docgen.entity;

import lombok.*;
import javax.persistence.*;
import java.time.LocalDateTime;

@Entity
@Table(name = "generated_files")
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class GeneratedFile {
    
    @Id
    @Column(length = 36)
    private String id;
    
    @Column(name = "task_id", length = 36)
    private String taskId;
    
    @Column(name = "filename", length = 500)
    private String filename;
    
    @Column(name = "file_path", length = 1000)
    private String filePath;
    
    @Column(name = "file_size")
    private Long fileSize;
    
    @Column(name = "download_count")
    private Integer downloadCount = 0;
    
    @Column(name = "created_at")
    private LocalDateTime createdAt;
    
    @PrePersist
    protected void onCreate() {
        createdAt = LocalDateTime.now();
        if (id == null) {
            id = java.util.UUID.randomUUID().toString();
        }
    }
}
```

#### 2.2 创建 Repository 接口

##### TemplateRepository.java
```java
package com.example.docgen.repository;

import com.example.docgen.entity.TemplateRegistry;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public interface TemplateRepository extends JpaRepository<TemplateRegistry, String> {
    
    List<TemplateRegistry> findByDeletedFalseOrderByCreatedAtDesc();
    
    List<TemplateRegistry> findByCategoryAndDeletedFalse(String category);
    
    List<TemplateRegistry> findByIsDefaultTrueAndDeletedTrue();
    
    @Query("SELECT t FROM t WHERE t.name LIKE %?1% AND t.deleted = false")
    List<TemplateRegistry> searchByName(String keyword);
}
```

##### ArchitectureRepository.java
```java
package com.example.docgen.repository;

import com.example.docgen.entity.DocumentArchitecture;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public interface ArchitectureRepository extends JpaRepository<DocumentArchitecture, String> {
    
    List<DocumentArchitecture> findAllByOrderByCreatedAtDesc();
    
    List<DocumentArchitecture> findByTemplateId(String templateId);
}
```

##### TaskCacheRepository.java
```java
package com.example.docgen.repository;

import com.example.docgen.entity.TaskCache;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public interface TaskCacheRepository extends JpaRepository<TaskCache, String> {
    
    List<TaskCache> findByUserIdOrderByUpdatedAtDesc(String userId);
    
    List<TaskCache> findByStatusOrderByUpdatedAtDesc(String status);
    
    TaskCache findByTaskId(String taskId);
}
```

##### GeneratedFileRepository.java
```java
package com.example.docgen.repository;

import com.example.docgen.entity.GeneratedFile;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public interface GeneratedFileRepository extends JpaRepository<GeneratedFile, String> {
    
    List<GeneratedFile> findByTaskIdOrderByCreatedAtDesc(String taskId);
}
```

---

### Day 3: 基础服务层与默认数据

#### 3.1 创建 DTO 类

##### TemplateDTO.java
```java
package com.example.docgen.dto;

import lombok.*;
import java.time.LocalDateTime;

@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class TemplateDTO {
    
    private String id;
    private String name;
    private String description;
    private String category;
    private FormatSpecDTO formatSpec;
    private Boolean isDefault;
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
    
    @Data
    @NoArgsConstructor
    @AllArgsConstructor
    public static class FormatSpecDTO {
        private PageSetupDTO pageSetup;
        private FontsDTO fonts;
        private ParagraphsDTO paragraphs;
        private HeaderFooterDTO headerFooter;
        private TableStyleDTO tableStyle;
        private CoverPageDTO coverPage;
    }
    
    // 其他嵌套 DTO 类...
}
```

#### 3.2 创建 Service 基类

##### TemplateService.java
```java
package com.example.docgen.service;

import com.example.docgen.dto.TemplateDTO;
import com.example.docgen.entity.TemplateRegistry;
import com.example.docgen.repository.TemplateRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;
import java.util.stream.Collectors;

@Service
@RequiredArgsConstructor
public class TemplateService {
    
    private final TemplateRepository templateRepository;
    private final ObjectMapper objectMapper;
    
    public List<TemplateDTO> getAllTemplates() {
        return templateRepository.findByDeletedFalseOrderByCreatedAtDesc()
                .stream().map(this::toDTO).collect(Collectors.toList());
    }
    
    public TemplateDTO getTemplateById(String id) {
        TemplateRegistry template = templateRepository.findById(id)
                .orElseThrow(() -> new RuntimeException("模板不存在"));
        return toDTO(template);
    }
    
    @Transactional
    public TemplateDTO createTemplate(TemplateDTO dto) {
        TemplateRegistry entity = toEntity(dto);
        TemplateRegistry saved = templateRepository.save(entity);
        return toDTO(saved);
    }
    
    // 其他方法...
    
    private TemplateDTO toDTO(TemplateRegistry entity) {
        // 转换逻辑
    }
    
    private TemplateRegistry toEntity(TemplateDTO dto) {
        // 转换逻辑
    }
}
```

#### 3.3 初始化默认模板数据

创建 `src/main/resources/data/templates/default-templates.json` 文件，包含预置的 5 种模板。

---

## ✅ 验收标准

- [ ] 数据库表成功创建
- [ ] 实体类编译通过
- [ ] Repository 层可正常访问数据库
- [ ] H2 Console 可访问（http://localhost:8080/h2-console）
- [ ] 默认模板数据加载成功
- [ ] 单元测试通过率 100%

---

## 📝 注意事项

1. **Lombok 配置**：确保 IDE 安装了 Lombok 插件
2. **H2 Console**：仅开发环境启用，生产环境需关闭
3. **JSON 字段**：H2 的 JSON 支持有限，生产环境建议用 MySQL/PostgreSQL
4. **字符编码**：确保数据库使用 UTF-8 编码
