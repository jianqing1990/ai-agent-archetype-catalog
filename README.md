# Maven Archetype 脚手架目录

本仓库用于统一维护个人或团队可使用的 Maven Archetype（项目脚手架）目录。

它的作用可以简单理解为：**让 IntelliJ IDEA 知道目前有哪些项目模板，以及应该到哪里下载这些模板。** 使用者选择某个脚手架并填写新项目名称、包名和版本后，Maven 会自动生成一套结构统一、可以继续开发的多模块工程。

> 本仓库只保存脚手架目录文件，不保存脚手架源码和 JAR。真正的脚手架制品存放在阿里云私有 Maven 仓库中。

## 工作流程

```text
IntelliJ IDEA
      ↓ 读取
GitHub Pages 上的 archetype-catalog.xml
      ↓ 获得脚手架坐标和 Maven 仓库地址
本机 Maven 使用 settings.xml 中的授权信息访问阿里云仓库
      ↓ 下载 Archetype JAR
按照填写的 GroupId、ArtifactId、Version、Package 生成新工程
```

IDEA 使用的 Catalog 地址：

```text
https://jianqing1990.github.io/ai-agent-archetype-catalog/archetype-catalog.xml
```

目录源文件：[docs/archetype-catalog.xml](docs/archetype-catalog.xml)

## 当前提供的脚手架

| 脚手架 | Maven 坐标 | 运行环境 | 主要用途 |
|---|---|---|---|
| AI Agent 脚手架 | `cn.bugstack.ai:ai-agent-scaffold-archetype:1.0` | Java 17、Spring Boot 3.4.3 | 快速创建包含 AI 模型接入、Agent 编排、MCP 工具调用等基础能力的多模块工程 |
| DDD 基础脚手架 | `cn.bugstack.ai:ddd-scaffold-lite-archetype:1.0` | Java 8、Spring Boot 2.7.12 | 快速创建采用 DDD 分层思想的通用多模块工程，适合作为普通业务系统的基础框架 |

两个脚手架都会生成以下核心模块：

| 模块 | 作用 |
|---|---|
| `*-api` | 对外接口、请求/响应对象及公共 API 定义 |
| `*-app` | 应用启动、Spring 配置、资源文件和工程装配 |
| `*-domain` | 核心业务规则、领域模型和领域服务 |
| `*-trigger` | HTTP、任务、消息监听等业务入口 |
| `*-infrastructure` | 数据库、外部服务及其他基础设施的具体实现 |
| `*-types` | 公共常量、枚举、异常等通用类型 |

其中 AI Agent 脚手架是在这套 DDD 多模块结构上，继续加入智能体工作流、模型、工具等 AI 相关代码；DDD 基础脚手架更轻量，适合从通用业务工程开始开发。

## 使用前准备

1. 安装 IntelliJ IDEA。
2. 安装 Maven，建议使用 Maven 3.9.x。
3. 根据所选脚手架安装对应的 JDK。
4. 取得阿里云私有 Maven 仓库的下载权限。
5. 从阿里云下载属于自己的 `settings.xml`，不要与其他人共用或提交到 Git。

## 在 IntelliJ IDEA 中使用

### 1. 配置阿里云 Maven 认证

打开 IDEA 的 Maven 设置：

```text
File
→ Settings
→ Build, Execution, Deployment
→ Build Tools
→ Maven
```

在 `User settings file` 中选择从阿里云下载的 `settings.xml`，并启用对应的覆盖选项。该文件包含个人仓库认证信息，只应保存在本机。

### 2. 添加脚手架目录

打开新建项目窗口：

```text
File
→ New
→ Project
→ Maven Archetype
→ Manage Catalogs
```

新增一个 Catalog：

```text
Name: ai-agent-archetype-catalog
URL:  https://jianqing1990.github.io/ai-agent-archetype-catalog/archetype-catalog.xml
```

### 3. 选择脚手架

在 `Catalog` 中选择刚添加的 `ai-agent-archetype-catalog`，再按项目用途选择：

- 需要开发 AI Agent、模型调用或 MCP 工具能力：选择 `ai-agent-scaffold-archetype:1.0`。
- 需要一个通用的 DDD 多模块业务工程：选择 `ddd-scaffold-lite-archetype:1.0`。

### 4. 填写新项目参数

例如：

```text
GroupId:    com.example
ArtifactId: demo-project
Version:    1.0-SNAPSHOT
Package:    com.example
```

> 当前两个模板的启动类配置使用 `${groupId}.Application`，因此请让 `Package` 与 `GroupId` 保持一致，避免生成后 Maven 找不到启动类。

选择与脚手架匹配的 JDK 后点击 `Create`。IDEA 会读取目录、访问阿里云仓库、下载 Archetype JAR，并生成完整工程。

### 5. 启动生成后的工程

1. 等待 IDEA 完成 Maven 依赖加载。
2. 检查 `*-app/src/main/resources/application-dev.yml` 中的数据源等本地配置。
3. AI Agent 工程如需调用相关能力，请按实际使用场景配置 `DEEPSEEK_API_KEY`、`BAIDU_APPBUILDER_API_KEY`、`BAIDU_MAP_AK` 等环境变量。
4. 运行 `*-app` 模块中的 `Application.java`。
5. 两个模板当前的默认服务端口均为 `8091`。

## 访问权限与安全说明

当前的权限关系是：

```text
GitHub Catalog：公开，用于展示可选脚手架
阿里云 Maven 仓库：私有，用于保存真正的 JAR 和 POM
settings.xml：个人私有，仅保存在授权使用者本机
```

因此，外部用户即使看到了本仓库和脚手架坐标，没有阿里云仓库权限及有效的 `settings.xml`，也无法下载真正的脚手架制品。

请勿向本仓库提交以下内容：

- 阿里云 `settings.xml`；
- 用户名、密码、AccessKey、Token；
- `.env` 或其他包含密钥的本地配置；
- IDE 本地配置、日志和临时构建文件。

## 如何维护脚手架目录

### 发布已有脚手架的新版本

1. 修改并验证脚手架工程。
2. 使用新的版本号，例如从 `1.0` 升级到 `1.1`。
3. 将新版本的 JAR 和 POM 发布到阿里云 Maven 仓库。
4. 更新 `docs/archetype-catalog.xml` 中对应条目的版本，或者保留旧版本并新增一条记录。
5. 提交到 GitHub，等待 GitHub Pages 自动部署。
6. 在 IDEA 中刷新 Catalog，并实际创建一次测试工程。

> 不建议覆盖已经发布的 Release 版本。发布新版本号可以避免 Maven 本地缓存仍使用旧 JAR。

### 增加新的脚手架

不需要重新创建 GitHub 仓库，只需先把新脚手架发布到阿里云，再在 `docs/archetype-catalog.xml` 的 `<archetypes>` 中增加一个条目：

```xml
<archetype>
    <groupId>脚手架的 groupId</groupId>
    <artifactId>脚手架的 artifactId</artifactId>
    <version>脚手架版本</version>
    <repository>阿里云 Maven 仓库地址</repository>
    <description>脚手架用途说明</description>
</archetype>
```

提交并完成 Pages 部署后，所有使用者继续使用原来的 Catalog 地址，就能看到新增的脚手架。

## 常见问题

### IDEA 中看不到新脚手架

- 确认在线 Catalog 地址已经包含新条目。
- 等待 GitHub Pages 部署完成。
- 关闭并重新打开新建项目窗口，或在 `Manage Catalogs` 中重新添加该地址。

### 能看到脚手架，但下载失败

- 检查 IDEA 是否使用了正确的阿里云 `settings.xml`。
- 检查当前阿里云账号是否拥有仓库下载权限。
- 检查 Catalog 中的 `groupId`、`artifactId`、`version`、仓库地址是否与阿里云制品完全一致。

### 修改模板后仍然生成旧代码

Maven 很可能使用了本地缓存。优先发布新版本，例如从 `1.0` 升级为 `1.1`；如必须排查缓存，只清理本地 Maven 仓库中该脚手架坐标对应的目录，不要直接删除整个 `.m2`。
