# AiMenu 智能点餐系统

AiMenu 是一个基于 FastAPI、Vue 3 和大语言模型的智能餐厅点餐系统。项目支持 AI 对话、菜品推荐、菜单展示和配送范围查询，适合作为智能点餐、餐厅助手或大模型应用练习项目。

## 功能特性

- 智能问答：用户可以咨询营业时间、餐厅信息、菜品推荐等问题。
- 菜品推荐：根据用户口味和需求调用智能助手返回推荐结果。
- 菜单展示：从 MySQL 数据库读取菜品列表，展示价格、分类、辣度、描述、是否素食等信息。
- 配送查询：调用高德地图 API，将地址转为经纬度并判断是否在配送范围内。
- 前后端分离：后端提供 REST API，前端通过 Axios 调用接口。

## 技术栈

后端：

- Python
- FastAPI
- Uvicorn
- LangChain
- DashScope / 通义千问兼容接口
- MySQL
- 高德地图 API
- Pinecone

前端：

- Vue 3
- Vite
- Element Plus
- Axios

## 项目结构

```text
smart_diancan/
├── agent/              # 智能助手与意图分析逻辑
├── api/                # FastAPI 接口入口
├── prompt/             # 提示词文件
├── service/            # 业务服务封装
├── tools/              # 数据库、地图、LLM、向量库工具
├── ui/                 # Vue 前端项目
├── .env.example        # 环境变量示例
├── requirements.txt    # Python 依赖
└── run.py              # 后端启动入口
```

## 环境准备

### 1. 克隆项目

```bash
git clone https://github.com/sshuo476-ux/data_agent.git
cd data_agent
```

### 2. 配置后端环境

建议使用虚拟环境：

```bash
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
```

复制环境变量示例：

```bash
copy .env.example .env
```

然后在 `.env` 中填写真实配置：

```env
AMAP_API_KEY=your_amap_api_key
MERCHANT_LONGITUDE=114.401934
MERCHANT_LATITUDE=30.465295
DELIVERY_RADIUS=2500
DEFAULT_PATH_MODE=2

DASHSCOPE_API_KEY=your_dashscope_api_key
DASHSCOPE_API_BASE=https://dashscope.aliyuncs.com/compatible-mode/v1/
DASHSCOPE_MODEL_NAME=qwen2.5-14b-instruct

MYSQL_HOST=localhost
MYSQL_PORT=3306
MYSQL_USER_NAME=root
MYSQL_USER_PASSWORD=your_mysql_password
MYSQL_DB_NAME=menu
```

注意：`.env` 包含密钥和数据库密码，不要提交到 GitHub。

### 3. 配置数据库

项目默认读取 MySQL 数据库 `menu` 中的 `menu_items` 表。表字段需包含：

```text
id, dish_name, price, description, category,
spice_level, flavor, main_ingredients, cooking_method,
is_vegetarian, allergens, is_available
```

其中 `is_available = 1` 的菜品会展示到前端。

## 启动项目

### 启动后端

在项目根目录执行：

```bash
python run.py
```

后端默认运行在：

```text
http://127.0.0.1:8000
```

接口文档：

```text
http://127.0.0.1:8000/docs
```

健康检查：

```text
http://127.0.0.1:8000/healthy
```

### 启动前端

```bash
cd ui
npm install
npm run dev
```

前端默认运行在：

```text
http://127.0.0.1:3000
```

前端开发环境会将 `/api` 请求代理到 `http://127.0.0.1:8000`。

## 主要接口

### 健康检查

```http
GET /healthy
GET /health
```

### 菜品列表

```http
GET /menu/list
```

返回当前可用菜品列表。

### 智能问答

```http
POST /chat
Content-Type: application/json

{
  "query": "你们几点营业"
}
```

### 配送范围查询

```http
POST /delivery
Content-Type: application/json

{
  "address": "武汉市洪山区武汉大学",
  "travel_mode": "2"
}
```

`travel_mode` 可选值：

- `1`：步行
- `2`：电动车 / 骑行
- `3`：驾车

## 构建前端

```bash
cd ui
npm run build
```

构建产物会输出到 `ui/dist`。该目录已被 `.gitignore` 忽略。

## 安全说明

- 不要提交 `.env`、API Key、数据库密码、个人访问令牌。
- 如果密钥曾经泄露，请立刻到对应平台撤销并重新生成。
- 当前仓库只提交 `.env.example`，真实配置需要在本地自行填写。

## 常见问题

### 1. 配送查询返回 `RESULTS_ARE_EMPTY`

通常是高德地图无法解析输入地址，或地址和商户坐标不在同一区域。可以尝试输入更完整的地址，例如包含省、市、区。

### 2. 前端请求后端失败

确认后端已经运行在：

```text
http://127.0.0.1:8000
```

并检查 `ui/vite.config.js` 中的代理配置。

### 3. 菜品列表为空

确认 MySQL 已启动，`.env` 数据库配置正确，并且 `menu_items` 表中存在 `is_available = 1` 的数据。

## 项目状态

当前项目已完成基础智能点餐流程，核心功能包括：

- 菜品列表展示
- 智能问答
- 菜品推荐
- 配送范围查询
- 前后端联调

后续可以继续扩展下单、购物车、用户登录、订单管理和后台管理等功能。

---

# AiMenu Smart Ordering System

AiMenu is an intelligent restaurant ordering system built with FastAPI, Vue 3, and large language model capabilities. It supports AI chat, dish recommendations, menu browsing, and delivery range checks. The project is suitable for smart ordering demos, restaurant assistant prototypes, and LLM application practice.

## Features

- AI chat: Users can ask about opening hours, restaurant information, dish recommendations, and general questions.
- Dish recommendations: The assistant analyzes user preferences and returns suitable dish suggestions.
- Menu display: The backend reads menu items from MySQL and the frontend displays price, category, spice level, description, vegetarian status, and other details.
- Delivery range check: The backend uses Amap API to geocode addresses and calculate whether an address is within the delivery radius.
- Frontend-backend separation: The backend provides REST APIs, and the frontend calls them through Axios.

## Tech Stack

Backend:

- Python
- FastAPI
- Uvicorn
- LangChain
- DashScope / Qwen-compatible API
- MySQL
- Amap API
- Pinecone

Frontend:

- Vue 3
- Vite
- Element Plus
- Axios

## Project Structure

```text
smart_diancan/
├── agent/              # Assistant and intent analysis logic
├── api/                # FastAPI API entry
├── prompt/             # Prompt files
├── service/            # Business service layer
├── tools/              # Database, map, LLM, and vector database tools
├── ui/                 # Vue frontend project
├── .env.example        # Example environment variables
├── requirements.txt    # Python dependencies
└── run.py              # Backend startup entry
```

## Setup

### 1. Clone The Repository

```bash
git clone https://github.com/sshuo476-ux/data_agent.git
cd data_agent
```

### 2. Configure Backend Environment

Create and activate a virtual environment:

```bash
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
```

Copy the example environment file:

```bash
copy .env.example .env
```

Then fill in your real configuration in `.env`:

```env
AMAP_API_KEY=your_amap_api_key
MERCHANT_LONGITUDE=114.401934
MERCHANT_LATITUDE=30.465295
DELIVERY_RADIUS=2500
DEFAULT_PATH_MODE=2

DASHSCOPE_API_KEY=your_dashscope_api_key
DASHSCOPE_API_BASE=https://dashscope.aliyuncs.com/compatible-mode/v1/
DASHSCOPE_MODEL_NAME=qwen2.5-14b-instruct

MYSQL_HOST=localhost
MYSQL_PORT=3306
MYSQL_USER_NAME=root
MYSQL_USER_PASSWORD=your_mysql_password
MYSQL_DB_NAME=menu
```

Do not commit `.env` to GitHub because it contains secrets and database credentials.

### 3. Configure The Database

The project reads menu data from the `menu_items` table in the MySQL database named `menu`. The table should include these fields:

```text
id, dish_name, price, description, category,
spice_level, flavor, main_ingredients, cooking_method,
is_vegetarian, allergens, is_available
```

Only dishes with `is_available = 1` are displayed on the frontend.

## Run The Project

### Start The Backend

Run this command in the project root:

```bash
python run.py
```

The backend runs at:

```text
http://127.0.0.1:8000
```

API docs:

```text
http://127.0.0.1:8000/docs
```

Health check:

```text
http://127.0.0.1:8000/healthy
```

### Start The Frontend

```bash
cd ui
npm install
npm run dev
```

The frontend runs at:

```text
http://127.0.0.1:3000
```

In development, Vite proxies `/api` requests to `http://127.0.0.1:8000`.

## Main APIs

### Health Check

```http
GET /healthy
GET /health
```

### Menu List

```http
GET /menu/list
```

Returns the list of available dishes.

### AI Chat

```http
POST /chat
Content-Type: application/json

{
  "query": "What time do you open?"
}
```

### Delivery Range Check

```http
POST /delivery
Content-Type: application/json

{
  "address": "Wuhan University, Hongshan District, Wuhan",
  "travel_mode": "2"
}
```

`travel_mode` options:

- `1`: walking
- `2`: electric bike / cycling
- `3`: driving

## Build The Frontend

```bash
cd ui
npm run build
```

The build output is generated in `ui/dist`, which is ignored by `.gitignore`.

## Security Notes

- Do not commit `.env`, API keys, database passwords, or personal access tokens.
- If a secret has been exposed, revoke it immediately and generate a new one.
- This repository only includes `.env.example`; real configuration should be created locally.

## FAQ

### 1. Delivery API Returns `RESULTS_ARE_EMPTY`

This usually means Amap cannot parse the input address, or the address is not in the same region as the merchant coordinates. Try using a more complete address, including province, city, and district.

### 2. Frontend Cannot Reach The Backend

Make sure the backend is running at:

```text
http://127.0.0.1:8000
```

Also check the proxy settings in `ui/vite.config.js`.

### 3. Menu List Is Empty

Make sure MySQL is running, `.env` contains the correct database settings, and the `menu_items` table has records where `is_available = 1`.

## Project Status

The project currently implements the core smart ordering workflow:

- Menu list display
- AI chat
- Dish recommendations
- Delivery range check
- Frontend-backend integration

Future improvements may include ordering, cart management, user login, order management, and an admin dashboard.
