# insight

**项目定位**：LLM 应用能力工程化练习项目 —— 从「能调通模型」到「能算成本、能换 provider、能挂 RAG 与 MCP」的渐进式工程骨架（W01 地基期起点）。

**技术栈**：Spring Boot 4.1.1 · Spring AI 2.0.1（BOM） · JDK 21 · Maven（项目内 `mvnw` 已锁版本） · Actuator。

**启动方式**：

```bash
export OPENAI_API_KEY=sk-xxx     # Windows CMD: set OPENAI_API_KEY=sk-xxx
./mvnw spring-boot:run           # Windows: mvnw.cmd spring-boot:run
curl -s http://localhost:8080/actuator/health   # 期望 {"status":"UP"}
```

> 本机 JDK 21 为便携安装，未改系统 `JAVA_HOME`。若 shell 仍指向旧 JDK，构建前先执行：
> `export JAVA_HOME="F:\app\tools\jdk-21"`（PowerShell: `$env:JAVA_HOME="F:\app\tools\jdk-21"`）

配置约定见 `.env.example`：key 只走环境变量，`.env` 已在 `.gitignore` 中排除。

## 本机环境（已就绪，未改动系统环境）

| 组件 | 位置 | 说明 |
|---|---|---|
| JDK 21 | `F:\app\tools\jdk-21` | Temurin 21.0.12.1 LTS，便携安装，**未改系统 JAVA_HOME**（系统仍为 JDK 8） |
| Maven 3.9.16 | `F:\app\tools\apache-maven-3.9.16` | 便携安装，已配阿里云镜像（`conf/settings.xml`）；系统原 Maven 3.6.2 未动 |

## 常见问题

**启动报 `Port 56864 was already in use` / 端口不是 8080**
说明当前 shell 里存在 `SERVER__PORT` 环境变量（Spring Boot 宽松绑定会把它当作 `server.port`，优先级高于 `application.yml`）。先清掉再启动：

```bash
env -u SERVER__PORT -u SERVER__HOST java -jar target/insight-0.0.1-SNAPSHOT.jar
```

---

# VSCode 运行指南

## 0. 前置条件（本机已就绪）

| 项 | 状态 |
|---|---|
| Extension Pack for Java | 已装（redhat.java / vscode-maven / java-debug / java-test） |
| JDK 21 | `F:\app\tools\jdk-21` |
| Maven 3.9.16 | `F:\app\tools\apache-maven-3.9.16`（已配阿里云镜像） |
| `.vscode/settings.json` | 已生成，指向上述 JDK 与 Maven |

> `.vscode/` 与 `.env` 都在 `.gitignore` 里，属本机配置，不会进仓库。

## 1. 打开项目

`文件 → 打开文件夹` 选 `F:\projects\agent\insight`（**不要**用"添加文件夹到工作区"）。
右下角会弹出 `Importing Maven projects...`，首次导入要下载依赖，走阿里云镜像，等它跑完（进度在「输出 → Maven」面板看）。

## 2. 确认 JDK 生效（关键一步）

`Ctrl+Shift+P` → 输入 `Java: Configure Java Runtime`，确认列表里 **JavaSE-21 → `F:\app\tools\jdk-21`** 且标了 default。
或在 VSCode 集成终端里执行：

```bash
java -version      # 应显示 21.0.12.1
echo $JAVA_HOME    # 应显示 F:\app\tools\jdk-21（由 .vscode/settings.json 注入）
```

> 这两项由 `.vscode/settings.json` 的 `terminal.integrated.env.windows` 注入。
> **如果不生效，`Ctrl+Shift+P` → `Developer: Reload Window`**，改完终端环境变量必须重载窗口。

## 3. 运行（三种方式任选）

**方式 A —— F5 调试（推荐，能打断点）**
按 `F5`，或点左侧「运行和调试」→ 选 `insight: Launch (8080)` → 绿色三角。
日志直接打在集成终端里。

**方式 B —— 编辑器内的 Run 按钮**
打开 `src/main/java/com/insight/InsightApplication.java`，`main` 方法上方会出现 `Run|Debug` 链接，点 `Run`。

**方式 C —— 终端手动跑**
```bash
mvn spring-boot:run          # 或 .\mvnw.cmd spring-boot:run
```

## 4. 验收

另开一个终端：

```bash
curl -s http://localhost:8080/actuator/health
```

期望返回 `"status":"UP"`。

## 5. 排错速查

| 现象 | 原因 | 处理 |
|---|---|---|
| 编译报 `Fatal error compiling: 无效的目标发行版: 21` | `JAVA_HOME` 还是 JDK 8 | 确认 `.vscode/settings.json` 存在且未被覆盖 → `Developer: Reload Window`；终端里 `echo $JAVA_HOME` 核对 |
| Maven 面板空 / 一直 Importing | `maven.settingsFile` 没读到 | 看「输出 → Maven」面板；确认 `F:\app\tools\apache-maven-3.9.16\conf\settings.xml` 存在 |
| 依赖下载极慢 | 走了 Maven Central 而非阿里云镜像 | 确认 settings.json 里 `maven.settingsFile` 与 `java.configuration.maven.userSettings` 都指向便携版 settings.xml |
| 端口不是 8080 / 报端口占用 | `SERVER__PORT` 环境变量 | 见上文「常见问题」 |
| 语言服务报需要 JDK 17+ | `java.jdt.ls.java.home` 被误配 | 本配置**故意不设**这一项，让扩展用它自带的 JRE；若你手动加过，删掉即可 |
| 调模型返回 401 | `.env` 里还是占位 key | 填入真实 key；**不填 key 也能启动**（实测 health UP），key 只在真正调模型时才需要 |

## 6. 换机器 / 重装后

`.vscode/settings.json` 里有本机绝对路径，且不入库。换机器只需改 4 处路径：`java.configuration.runtimes[0].path`、`maven.executable.path`、`maven.settingsFile`、`java.configuration.maven.userSettings`，以及 `maven.terminal.customEnv` 与 `terminal.integrated.env.windows` 里的 `JAVA_HOME`。


