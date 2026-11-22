# Spring Boot GitHub Actions 持续集成

工作流位于 `.github/workflows/ci.yml`，在推送到 `main` 分支时运行。

步骤依次为 `actions/checkout@v4` 检出代码、`actions/setup-java@v4` 安装 Temurin JDK 21、`mvn -B test` 执行测试、`docker build -t springboot-demo:1.0 .` 构建镜像。

项目使用 Spring Boot 3.5.0 和 Java 21。`DemoApplicationTest` 使用 JUnit 5，通过 `MockMvcBuilders.standaloneSetup(new DemoApplication())` 构建独立 MockMvc，向 `/` 发起请求，验证状态码为 200，且响应为 `Spring Boot Docker demo is running` 加换行。

## 本地验证

```bash
mvn -B test
docker build -t springboot-actions-ci:1.0 .
```

本地使用独立镜像标签 `springboot-actions-ci:1.0`。GitHub 工作流在隔离的 runner 中构建 `springboot-demo:1.0`。

## 提交到作业仓库

在新解压的目录中，使用本次作业的 GitHub 仓库地址执行。

```bash
git init -b main
git add .github .gitignore .dockerignore Dockerfile pom.xml src README.md
git commit -m "Add Java 21 tests and Docker CI"
git remote add origin <作业仓库HTTPS地址>
git push -u origin main
```

推送后打开仓库的 Actions 页面，找到 `Spring Boot CI`，核对触发分支为 `main`，运行结果为 `success`，并检查 `Run Maven tests` 和 `Build Docker image` 的日志。截图和报告中的每项验证结果均以实际执行记录为准。
