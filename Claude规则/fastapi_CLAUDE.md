<!-- OMC:START -->
<!-- OMC:VERSION:4.13.5 -->

# CLAUDE.md - AI 开发指南与 Claude Code 配置

本文档包含两部分：
1. Claude Code 多代理编排配置
2. FastAPI 项目开发指南

---

## Claude Code 配置

<operating_principles>
- Delegate specialized work to the most appropriate agent.
- Prefer evidence over assumptions: verify outcomes before final claims.
- Choose the lightest-weight path that preserves quality.
- Consult official docs before implementing with SDKs/frameworks/APIs.
- 优先使用中文进行交流和文档编写
- 遵循项目分层架构规范，不跨层调用
- 确保所有代码都有类型提示和中文 docstring
</operating_principles>

<delegation_rules>
Delegate for: multi-file changes, refactoring, debugging, reviews, planning, research, verification.
Work directly for: trivial ops, small clarifications, single commands.
Route code to `executor` (use `model=opus` for complex work). Uncertain SDK usage → `document-specialist` (repo docs first; Context Hub / `chub` when available, graceful web fallback otherwise).
- 多文件新增功能 → 委托给 executor
- 代码重构 → 委托给 code-review 进行审查
- 数据库相关更改 → 先验证再执行
- 添加新的 API 端点 → 遵循分层架构规范
</delegation_rules>

<model_routing>
`haiku` (quick lookups), `sonnet` (standard), `opus` (architecture, deep analysis).
Direct writes OK for: `~/.claude/**`, `.omc/**`, `.claude/**`, `CLAUDE.md`, `AGENTS.md`.
- 简单代码审查和修改 → haiku
- 标准功能开发 → sonnet
- 架构设计和复杂问题分析 → opus
</model_routing>

<skills>
Invoke via `/oh-my-claudecode:<name>`. Trigger patterns auto-detect keywords.
Tier-0 workflows include `autopilot`, `ultrawork`, `ralph`, `team`, and `ralplan`.
Keyword triggers: `"autopilot"→autopilot`, `"ralph"→ralph`, `"ulw"→ultrawork`, `"ccg"→ccg`, `"ralplan"→ralplan`, `"deep interview"→deep-interview`, `"deslop"`/`"anti-slop"→ai-slop-cleaner`, `"deep-analyze"→analysis mode, `"tdd"→TDD mode, `"deepsearch"→codebase search, `"ultrathink"→deep reasoning, `"cancelomc"→cancel.
Team orchestration is explicit via `/team`.
Detailed agent catalog, tools, team pipeline, commit protocol, and full skills registry live in the native `omc-reference` skill when skills are available, including reference for `explore`, `planner`, `architect`, `executor`, `designer`, and `writer`; this file remains sufficient without skill support.
</skills>

<verification>
Verify before claiming completion. Size appropriately: small→haiku, standard→sonnet, large/security→opus.
If verification fails, keep iterating.
项目特定验证：
- 运行 ruff 检查通过：`ruff check app/`
- 代码符合分层架构规范
- 所有 API 使用统一的 Result 响应格式
- 所有函数有类型提示和中文 docstring
</verification>

<execution_protocols>
Broad requests: explore first, then plan. 2+ independent tasks in parallel. `run_in_background` for builds/tests.
Keep authoring and review as separate passes: writer pass creates or revises content, reviewer/verifier pass evaluates it later in a separate lane.
Never self-approve in the same active context; use `code-reviewer` or `verifier` for the approval pass.
Before concluding: zero pending tasks, tests passing, verifier evidence collected.
项目特定流程：
- 添加新功能遵循：Schema → Model → DAO → Service → Controller → 注册路由
- 数据库更改先创建迁移脚本再执行
</execution_protocols>

<worktree_paths>
State: `.omc/state/`, `.omc/state/sessions/{sessionId}/`, `.omc/notepad.md`, `.omc/project-memory.json`, `.omc/plans/`, `.omc/research/`, `.omc/logs/`
项目关键路径：
- 控制器：`app/api/controller/`
- 服务层：`app/api/service/`
- 数据访问：`app/api/dao/`
- 模型：`app/api/models/`
- 验证模型：`app/api/schemas/`
- 配置：`app/config.py`
- 主入口：`app/main.py`
</worktree_paths>

---

## 项目概述

这是一个基于 FastAPI 的高性能 Python 后端服务模板，采用分层架构设计。

## 项目概述

这是一个基于 FastAPI 的高性能 Python 后端服务模板，采用分层架构设计。

**项目路径**: /data/code/backend

## 技术栈

| 技术 | 版本 | 用途 |
|------|------|------|
| FastAPI | 0.104+ | Web 框架 |
| Uvicorn | 0.24+ | ASGI 服务器 |
| Pydantic | 2.5+ | 数据验证 |
| SQLAlchemy | 2.0+ | ORM |
| PyMySQL | 1.1+ | MySQL 驱动 |
| Alembic | 1.13+ | 数据库迁移 |
| uv | - | 包管理工具 |

## 项目架构

### 目录结构

```
backend/
├── app/
│   ├── __init__.py
│   ├── config.py              # 配置管理（使用 pydantic-settings）
│   ├── database.py            # 数据库配置（SQLAlchemy 2.0）
│   ├── main.py                # FastAPI 应用入口
│   ├── api/
│   │   ├── controller/        # 控制器层 - 处理 HTTP 请求
│   │   ├── service/           # 服务层 - 业务逻辑
│   │   ├── dao/               # DAO 层 - 数据访问
│   │   ├── models/            # Model 层 - 数据库模型
│   │   └── schemas/           # Schema 层 - Pydantic 验证模型
│   ├── middleware/            # 中间件
│   │   ├── auth.py            # JWT 认证
│   │   └── cors.py            # CORS 配置
│   └── common/
│       ├── result/            # 统一响应格式
│       │   ├── code.py        # 状态码定义
│       │   └── result.py      # Result 类
│       └── util/              # 工具函数
├── tests/                     # 测试目录
├── scripts/                   # 脚本目录
├── pyproject.toml             # uv 项目配置
├── requirements.txt           # 依赖列表
└── .env.example               # 环境变量示例
```

### 分层架构规范

#### 1. Controller 层
- **位置**: `app/api/controller/`
- **职责**:
  - 接收和验证 HTTP 请求
  - 调用 Service 层
  - 返回统一响应格式
- **规范**:
  - 使用 FastAPI 的 `APIRouter`
  - 使用 Pydantic 模型进行参数验证
  - 所有接口必须有 docstring（用于生成 API 文档）
  - 使用 `Result.success()` 或 `Result.failed()` 返回响应
- **示例**:
```python
from fastapi import APIRouter
from app.common.result import Result
from app.api.schemas.demo import DemoRequest, DemoResponse

router = APIRouter(prefix="/demo", tags=["示例接口"])

@router.post("/create", summary="创建示例")
def create_example(request: DemoRequest) -> Result[DemoResponse]:
    """创建示例接口文档"""
    data = demo_service.create(request)
    return Result.success(data=data)
```

#### 2. Service 层
- **位置**: `app/api/service/`
- **职责**:
  - 实现业务逻辑
  - 调用 DAO 层进行数据操作
  - 事务处理
- **规范**:
  - 业务逻辑不应该依赖 HTTP 相关对象
  - 函数名使用动词开头（如 `create_user`, `get_order`）
  - 每个函数不超过 50 行

#### 3. DAO 层
- **位置**: `app/api/dao/`
- **职责**:
  - 封装所有数据库操作
  - 提供数据访问接口
- **规范**:
  - 使用 SQLAlchemy 2.0 的声明式语法
  - 不编写原始 SQL（除非必要）
  - 命名格式: `{Model}DAO`

#### 4. Model 层
- **位置**: `app/api/models/`
- **职责**:
  - 定义数据库 ORM 模型
- **规范**:
  - 继承自 `app.database.Base`
  - 使用类型注解
  - 所有模型必须有中文 docstring

#### 5. Schema 层
- **位置**: `app/api/schemas/`
- **职责**:
  - 定义请求和响应的数据验证模型
- **规范**:
  - 使用 Pydantic 2.0 语法
  - 使用 `Field` 提供描述和示例
  - 使用 `field_validator` 进行自定义验证
  - 命名: `{Name}Request`, `{Name}Response`, `{Name}Query`

## 代码规范

### 命名规范

| 类型 | 规范 | 示例 |
|------|------|------|
| 文件/目录 | snake_case | `user_controller.py`, `user_service.py` |
| 类 | PascalCase | `UserService`, `UserDAO` |
| 函数/方法 | snake_case | `get_user()`, `create_order()` |
| 变量 | snake_case | `user_id`, `order_list` |
| 常量 | UPPER_SNAKE_CASE | `MAX_PAGE_SIZE`, `JWT_SECRET` |
| 私有函数 | 下划线开头 | `_calculate_price()` |

### 注释规范

- 所有模块、类、函数必须有中文 docstring
- 使用 Google 风格或 NumPy 风格的 docstring
- 复杂逻辑必须有行内注释
- 注释应该解释"为什么"而不是"是什么"

```python
def calculate_price(items: List[Item]) -> float:
    """
    计算订单总价格

    参数:
        items: 商品列表

    返回:
        float: 总价格

    异常:
        ValueError: 当商品列表为空时
    """
    if not items:
        raise ValueError("商品列表不能为空")  # 防止空列表导致价格计算错误
    return sum(item.price for item in items)
```

### 类型提示

所有函数必须有类型提示：

```python
from typing import List, Optional, Dict, Any
from pydantic import EmailStr

def get_user_by_email(
    db: Session,
    email: EmailStr,
    include_deleted: bool = False
) -> Optional[User]:
    """根据邮箱获取用户"""
    pass
```

## 配置管理

### 获取配置

使用 `app.config.get_settings()` 获取配置：

```python
from app.config import get_settings

settings = get_settings()

# 使用配置
db_url = settings.DATABASE_URL
jwt_secret = settings.JWT_SECRET_KEY
```

### 环境变量

- 复制 `.env.example` 为 `.env`
- 修改 `.env` 中的配置值
- 不要提交 `.env` 文件到 Git

## 数据库操作

### 获取数据库会话

使用 FastAPI 的依赖注入：

```python
from fastapi import Depends
from sqlalchemy.orm import Session
from app.database import get_db

@router.get("/users/{user_id}")
def get_user(
    user_id: int,
    db: Session = Depends(get_db)
):
    pass
```

### 定义模型

```python
from sqlalchemy import Column, Integer, String, Boolean, DateTime
from sqlalchemy.sql import func
from app.database import Base

class User(Base):
    """用户模型"""
    __tablename__ = "users"

    id = Column(Integer, primary_key=True, index=True)
    username = Column(String(50), unique=True, index=True, nullable=False)
    email = Column(String(100), unique=True, index=True, nullable=False)
    is_active = Column(Boolean, default=True)
    created_at = Column(DateTime(timezone=True), server_default=func.now())
    updated_at = Column(DateTime(timezone=True), onupdate=func.now())
```

### DAO 层操作

```python
from sqlalchemy.orm import Session
from sqlalchemy import select
from app.api.models.user import User

class UserDAO:
    """用户数据访问对象"""

    @staticmethod
    def get_by_id(db: Session, user_id: int) -> Optional[User]:
        """根据 ID 获取用户"""
        return db.execute(select(User).where(User.id == user_id)).scalar_one_or_none()

    @staticmethod
    def get_by_email(db: Session, email: str) -> Optional[User]:
        """根据邮箱获取用户"""
        return db.execute(select(User).where(User.email == email)).scalar_one_or_none()

    @staticmethod
    def create(db: Session, user_data: dict) -> User:
        """创建用户"""
        user = User(**user_data)
        db.add(user)
        db.commit()
        db.refresh(user)
        return user
```

## 统一响应格式

所有 API 必须使用 `Result` 类返回响应：

```python
from app.common.result import Result

# 成功响应
return Result.success(data=user_data, message="获取成功")

# 失败响应
return Result.failed(code=501, message="创建失败")
```

## 常用命令

### 开发相关

```bash
# 启动开发服务器（自动重载）
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000

# 使用启动脚本
python run.py
```

### 依赖管理

```bash
# 使用 uv 安装依赖
uv pip install -r requirements.txt

# 添加新依赖
uv pip install package-name

# 导出依赖
uv pip freeze > requirements.txt
```

### 数据库迁移

```bash
# 创建迁移脚本
alembic revision --autogenerate -m "描述信息"

# 执行迁移
alembic upgrade head

# 回滚上一次迁移
alembic downgrade -1

# 查看迁移历史
alembic history
```

### 测试

```bash
# 运行所有测试
pytest

# 运行测试并显示覆盖率
pytest --cov=app tests/

# 运行特定测试文件
pytest tests/test_user.py
```

### 代码检查

```bash
# 使用 ruff 检查代码
ruff check app/

# 使用 ruff 格式化代码
ruff format app/
```

## 开发流程

### 添加新功能的步骤

1. **创建 Schema 模型** - 在 `app/api/schemas/` 中创建请求和响应模型
2. **创建 Model 模型** - 在 `app/api/models/` 中创建数据库模型（如需要）
3. **创建 DAO 层** - 在 `app/api/dao/` 中创建数据访问对象
4. **创建 Service 层** - 在 `app/api/service/` 中实现业务逻辑
5. **创建 Controller 层** - 在 `app/api/controller/` 中创建控制器
6. **注册路由** - 在 `app/main.py` 中注册新路由
7. **编写测试** - 在 `tests/` 中编写测试用例

### Git 提交规范

使用 Conventional Commits 格式：

```
<type>(<scope>): <subject>

类型:
- feat: 新功能
- fix: 修复 bug
- docs: 文档更新
- style: 代码格式调整
- refactor: 重构
- test: 测试相关
- chore: 构建/工具相关
```

## 注意事项

### 必须遵循

- ✅ 使用中文注释和文档
- ✅ 使用类型提示
- ✅ 使用统一的响应格式 `Result`
- ✅ 分层清晰，不跨层调用
- ✅ 函数不超过 50 行，文件不超过 500 行
- ✅ 使用 uv 管理依赖
- ✅ 配置通过环境变量管理

### 禁止的做法

- ❌ 在 Controller 层直接操作数据库
- ❌ 在 Service 层使用 HTTP 相关对象
- ❌ 编写原始 SQL（除非确实必要）
- ❌ 直接修改 .env.example 文件（应该复制为 .env）
- ❌ 提交 .env 文件到 Git
- ❌ 修改已存在的迁移文件
- ❌ 在代码中硬编码配置值

## 调试技巧

### 查看 API 文档

启动服务后访问：
- Swagger UI: http://127.0.0.1:8000/apidocs
- Redoc: http://127.0.0.1:8000/redoc

### 日志调试

```python
from app.common.util.LogHandler import LogHandler

logger = LogHandler("debug", level="DEBUG")
logger.debug(f"调试信息: {variable}")
logger.info("正常信息")
logger.error("错误信息")
```

### 数据库查询调试

在 `.env` 中设置 `APP_DEBUG=true`，SQLAlchemy 会输出执行的 SQL 语句。

## 常见问题

### Q: 如何添加新的配置项？

A: 在 `app/config.py` 的 `Settings` 类中添加字段，并在 `.env.example` 中添加示例。

### Q: 如何添加新的中间件？

A: 在 `app/middleware/` 中创建中间件文件，然后在 `app/main.py` 的 `create_app()` 函数中添加。

### Q: 如何处理数据库事务？

A: 在 Service 层使用数据库会话，操作完成后调用 `db.commit()`，出错时调用 `db.rollback()`。

### Q: 如何添加自定义状态码？

A: 在 `app/common/result/code.py` 的 `codes` 字典中添加。

## 联系与支持

- 作者: 南宫乘风
- 邮箱: 1794748404@qq.com

---

**最后更新**: 2024-05-01

<!-- OMC:END -->
