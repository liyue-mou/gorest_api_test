# GoRest API Testing Project

[![Postman](https://img.shields.io/badge/Postman-FF6C37?style=flat&logo=postman&logoColor=white)](https://www.postman.com/)
[![Newman](https://img.shields.io/badge/Newman-00B0B9?style=flat&logo=postman&logoColor=white)](https://github.com/postmanlabs/newman)
[![GitHub last commit](https://img.shields.io/github/last-commit/liyue-mou/gorest_api_test)](https://github.com/liyue-mou/gorest_api_test)

## 📌 项目简介

对公开 API [GoRest](https://gorest.co.in/) 的用户管理模块进行的**系统级接口功能测试**。覆盖增、删、查三个核心接口，包含边界值、参数校验、鉴权、异常场景及跨接口数据一致性验证。测试用 Postman 实现，并通过 Newman 命令行工具生成 HTML 测试报告。

## 🗂 项目结构
gorest_api_test/
├── README.md # 项目说明文档
├── .gitignore # Git 忽略配置
├── GoRest API 接口测试.postman_collection.json # Postman 测试集合（可直接导入）
├── GoRest.postman_environment.json # Postman 环境变量模板
├── GoRest 接口测试用例模板.xlsx # 详细测试用例（Excel 版本，67 条）
└── report/ # 测试报告目录
└── GoRest_Test_Report.html # Newman 生成的 HTML 测试报告

text

> **说明**：项目最初分两个独立仓库探索，于 2024-04-24 整合为统一结构，以便集中维护和展示。

## 🔧 环境与工具

| 工具/技术 | 用途 |
|----------|------|
| **Postman** | 接口请求编写、集合管理、脚本调试 |
| **Newman** | 命令行执行 Postman 集合，生成报告 |
| **GoRest API (v2)** | 被测系统 |
| **Bearer Token** | 认证方式（需从 [GoRest](https://gorest.co.in/consumer/login) 获取） |

## 🚀 快速开始

### 1. 获取 Token
前往 [GoRest 官网](https://gorest.co.in/consumer/login) 注册并获取 API Token。

### 2. 克隆项目
```bash
git clone https://github.com/liyue-mou/gorest_api_test.git
cd gorest_api_test
3. 导入 Postman 文件（GUI 方式）
打开 Postman

点击 Import → 选择 GoRest API 接口测试.postman_collection.json 和 GoRest.postman_environment.json

在 GoRest 环境中，将 token 变量的值替换为你的个人 Token

4. 运行测试（Newman 命令行方式）
bash
# 安装 Newman 及报告插件（首次使用）
npm install -g newman newman-reporter-htmlextra

# 执行测试并生成 HTML 报告
newman run "GoRest API 接口测试.postman_collection.json" \
  -e "GoRest.postman_environment.json" \
  --env-var "token=<你的GoRest Token>" \
  -r htmlextra \
  --reporter-htmlextra-export "./report/GoRest_Test_Report.html"
执行成功后，在浏览器中打开 report/GoRest_Test_Report.html 即可查看详细测试报告。

📊 测试概览
接口	用例数	覆盖类型	执行结果
创建用户	29	功能、参数校验、边界值、鉴权、异常、特殊字符	2 个 Bug
删除用户	8	功能、参数校验、鉴权、异常	1 个 Bug
获取用户列表	28	功能、分页、过滤、边界值、幂等性、异常	2 个 Bug
跨接口测试	2	数据一致性（增后查、删后查）	通过
详细用例数据见 GoRest 接口测试用例模板.xlsx。

🐞 缺陷报告
测试共发现 5 个缺陷，已在 Excel 用例的"缺陷编号"栏标记：

编号	关联用例	问题描述	状态
Bug-001	USER_CREATE_014	email 为空时接口返回 401 而非 422	待修复
Bug-002	USER_CREATE_016	email 长度为 201 时接口未校验限制，仍返回 201	待修复
Bug-003	USER_DELETE_008	可删除其他用户创建的数据，返回 204 而非 404（越权风险）	待修复
Bug-004	USER_LIST_020	per_page 超出最大值时应返回 422，实际返回 404 HTML 页面	待修复
Bug-005	USER_LIST_027	使用 DELETE 方法时应返回 405，实际返回 404 HTML 页面	待修复
📝 测试策略亮点
参数设计覆盖全面：对 name、email、gender、status 进行了必填缺失、非法值、空值、边界值（长度 0, 1, 100, 200, 201）、特殊符号等测试。

鉴权场景：涵盖无 Token、错误 Token、过期 Token 以及 Token 置于 URL 参数等场景。

分页与过滤：校验了 page/per_page 参数的边界及组合，并对非法参数值的容错处理进行了验证。

幂等性：对获取列表接口执行 5 次请求，验证返回数据总条数一致。

跨接口数据一致性：验证创建后查询、删除后查询的数据准确性。

隐性特性记录：对 API 未明确文档化的行为（如 name 支持特殊字符、非法参数回退默认值）进行了记录和风险说明。

📧 联系方式
作者：李玥 (Li Yue)

GitHub：liyue-mou

项目历程：本项目是个人 API 测试学习与实践作品，从最初拆分独立仓库到重构为统一规范仓库，全程使用 Git 进行版本管理。项目着重展示了测试工程师的用例设计能力、缺陷分析能力以及工程化文档规范。
