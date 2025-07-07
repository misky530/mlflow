# MLflow 开发环境搭建指南

本指南详细介绍如何从源码运行 MLflow UI，以便进行定制化开发。

## 项目概述

MLflow 是一个开源的机器学习生命周期管理平台，包含以下核心组件：

- **实验跟踪** - 记录和比较ML实验
- **模型打包** - 标准化模型格式
- **模型注册** - 中央模型存储
- **模型服务** - 部署到各种平台
- **模型评估** - 自动化评估工具
- **可观察性** - 跟踪和监控

## 技术栈

- **后端**: Python Flask
- **前端**: React + TypeScript
- **UI库**: Databricks Design System
- **构建工具**: Create React App (CRACO)
- **包管理**: Yarn (前端) + pip (后端)

## 开发环境设置

### 方法 1：使用开发脚本（推荐）

**最简单的方法是使用项目提供的开发脚本：**

```bash
# 运行开发服务器（自动启动后端 + 前端）
./dev/run-dev-server.sh

# 或者使用 Python 脚本（包含 Gateway 支持）
python dev/server.py
```

### 方法 2：手动分步启动

#### 1. 环境准备

```bash
# 1. 设置 Python 环境
conda create --name mlflow-dev python=3.9
conda activate mlflow-dev

# 2. 安装 Python 依赖
pip install -e '.[extras]'  # 从源码安装 MLflow
pip install -r requirements/dev-requirements.txt

# 3. 安装系统依赖（macOS）
brew install pixman cairo pango jpeg

# 4. 安装 Node.js 依赖
cd mlflow/server/js
yarn install
cd ../../..
```

#### 2. 启动开发服务器

**在第一个终端启动 MLflow 后端：**
```bash
mlflow server --dev
```

**在第二个终端启动前端开发服务器：**
```bash
cd mlflow/server/js
yarn start
```

#### 3. 可选：启用预提交钩子

```bash
# 配置 Git 用户信息
git config --global user.name "Your Name"
git config --global user.email yourname@example.com

# 安装预提交钩子
pre-commit install --install-hooks
```

## 访问地址

- **前端开发服务器**：http://localhost:3000 （支持热重载）
- **后端 API 服务器**：http://localhost:5000
- **实验数据存储**：./mlruns 目录

## 项目结构

### 前端代码结构

```
mlflow/server/js/
├── src/
│   ├── common/           # 通用组件和工具
│   ├── experiment-tracking/  # 实验跟踪功能
│   ├── model-registry/   # 模型注册功能
│   ├── shared/          # 共享组件
│   ├── i18n/            # 国际化
│   └── lang/            # 语言文件
├── public/              # 静态资源
├── package.json         # 依赖配置
└── craco.config.js      # 构建配置
```

### 后端代码结构

```
mlflow/
├── tracking/            # 实验跟踪
├── models/              # 模型管理
├── data/                # 数据管理
├── server/              # Web 服务器
├── gateway/             # LLM 网关
├── tracing/             # 链路追踪
├── evaluation/          # 模型评估
├── deployments/         # 部署管理
└── utils/               # 工具函数
```

## 开发工作流

### 启动开发环境

```bash
# 使用开发脚本启动完整环境
./dev/run-dev-server.sh

# 或者分别启动
# 终端1: mlflow server --dev
# 终端2: cd mlflow/server/js && yarn start
```

### 前端开发

```bash
cd mlflow/server/js

# 开发服务器（已通过上述方法启动）
yarn start

# 运行测试
yarn test

# 监听模式运行测试
yarn test:watch

# 代码检查
yarn lint
yarn lint:fix

# 代码格式化
yarn prettier:check
yarn prettier:fix

# 类型检查
yarn type-check

# 国际化检查
yarn i18n:check

# 运行所有检查
yarn check-all
```

### 后端开发

```bash
# 运行 Python 测试
pytest tests/

# 运行特定测试
pytest tests/test_tracking.py

# 代码格式化
ruff format .
ruff check --fix .
```

### 构建生产版本

```bash
# 构建前端静态文件
cd mlflow/server/js
yarn build

# 构建完整的 Python 包
python setup.py bdist_wheel
```

## 定制化开发指南

### 1. 修改前端界面

- **位置**: `mlflow/server/js/src/`
- **特点**: 支持热重载，修改后自动刷新浏览器
- **主要文件**:
  - `app.tsx` - 主应用组件
  - `experiment-tracking/` - 实验跟踪相关组件
  - `model-registry/` - 模型注册相关组件

### 2. 修改后端逻辑

- **位置**: `mlflow/` 目录
- **特点**: 修改后需要重启服务器
- **主要模块**:
  - `tracking/` - 实验跟踪 API
  - `models/` - 模型管理 API
  - `server/handlers.py` - HTTP 请求处理

### 3. 添加新功能

1. **前端组件**: 在 `mlflow/server/js/src/` 中创建新组件
2. **后端 API**: 在相应的模块中添加新的处理函数
3. **路由**: 更新 `mlflow/server/js/src/` 中的路由配置

### 4. 样式定制

- 使用 Databricks Design System 组件
- 自定义样式文件位于各组件目录
- 支持 CSS-in-JS 和传统 CSS

## 常用开发命令

```bash
# 环境管理
conda activate mlflow-dev
conda deactivate

# 依赖管理
pip install -e '.[extras]'
yarn install

# 开发服务器
./dev/run-dev-server.sh
python dev/server.py

# 测试
pytest tests/
yarn test

# 代码质量
ruff check --fix .
yarn lint:fix
yarn prettier:fix

# 构建
yarn build
python setup.py bdist_wheel
```

## 故障排除

### 常见问题

1. **端口冲突**: 确保 3000 和 5000 端口未被占用
2. **依赖问题**: 删除 `node_modules` 后重新运行 `yarn install`
3. **Python 环境**: 确保激活了正确的 conda 环境
4. **权限问题**: 确保有足够的文件读写权限

### 调试技巧

1. **前端调试**: 使用浏览器开发者工具
2. **后端调试**: 在 Python 代码中添加断点
3. **网络请求**: 检查浏览器网络选项卡
4. **日志查看**: 检查终端输出的错误信息

## 贡献指南

1. Fork 项目到自己的 GitHub 账号
2. 创建功能分支: `git checkout -b feature/my-feature`
3. 提交代码: `git commit -m "Add my feature"`
4. 推送分支: `git push origin feature/my-feature`
5. 创建 Pull Request

## 相关链接

- [MLflow 官方文档](https://mlflow.org/docs/latest/index.html)
- [MLflow GitHub 仓库](https://github.com/mlflow/mlflow)
- [贡献指南](https://github.com/mlflow/mlflow/blob/master/CONTRIBUTING.md)
- [问题反馈](https://github.com/mlflow/mlflow/issues)

---

*最后更新时间: 2025-01-07*