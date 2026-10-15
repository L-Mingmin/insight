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

