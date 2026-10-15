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
