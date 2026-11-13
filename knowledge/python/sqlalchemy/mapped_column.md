# mapped_column

- 语言: Python
- 库/模块: SQLAlchemy 2.0(`sqlalchemy.orm`)
- 导入: `from sqlalchemy.orm import mapped_column`
- 用途: 在 Declarative 类里把 Python 属性映射成数据库列,自带 `Mapped[...]` 类型与可空性推断
- 官方文档: https://docs.sqlalchemy.org/en/20/orm/mapping_api.html#sqlalchemy.orm.mapped_column

## 词源

- `mapped_column` -> mapped(map 的过去分词,被映射的)+ column(列)= 被映射的列
- `primary_key` -> primary(主要的)+ key(钥匙/键)= 主键
- `nullable` -> null(空)+ able(能够)= 能不能为空
- `index` -> index(索引)= 索引
- `unique` -> unique(唯一的)= 唯一约束
- `default` -> default(默认)= 默认值
- `default_factory` -> default(默认)+ factory(工厂)= 默认值的工厂函数
- `server_default` -> server(服务器)+ default(默认)= 数据库服务端默认
- `insert_default` -> insert(插入)+ default(默认)= 插入时的默认
- `onupdate` -> on(在……时)+ update(更新)= 更新时
- `autoincrement` -> auto(自动)+ increment(递增)= 自动递增
- `use_existing_column` -> use(使用)+ existing(已存在的)+ column(列)= 复用已有列
- `deferred` -> deferred(被推迟的)= 延迟加载的列
- `comment` -> comment(注释)= 列注释
- `name` / `type_` -> 列名 / 列类型的关键字形式

## 最小例子

```python
from datetime import datetime
from typing import Optional

from sqlalchemy import ForeignKey, String, func
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column, relationship


class Base(DeclarativeBase):
    pass


class User(Base):
    __tablename__ = "user_account"

    id: Mapped[int] = mapped_column(primary_key=True)          # 主键,整数默认自增
    name: Mapped[str] = mapped_column(String(30), unique=True)  # 注解非 Optional -> NOT NULL
    nickname: Mapped[Optional[str]] = mapped_column(default=None)  # 注解含 Optional -> 可空
    created_at: Mapped[datetime] = mapped_column(server_default=func.now())  # 数据库端默认
    addresses: Mapped[list["Address"]] = relationship(back_populates="user")


class Address(Base):
    __tablename__ = "address"

    id: Mapped[int] = mapped_column(primary_key=True)
    email: Mapped[str] = mapped_column(String(100), unique=True)
    user_id: Mapped[int] = mapped_column(ForeignKey("user_account.id"))  # 类型从外键列推断
    user: Mapped["User"] = relationship(back_populates="addresses")
```

位置参数说明:

- 第一个位置参数是列名,不写就用 Python 属性名。
- 第二个位置参数是类型,不写就从 `Mapped[...]` 推断;有 `ForeignKey` 时从被引用的列推断。
- 后面的位置参数(`*args`)放 `ForeignKey`、`CheckConstraint`、`Identity` 这类 schema 构造。

## 参数（常用在前）

| 参数 | 必填 | 类型 | 默认 | 词源 | 含义 | 易错点 |
|------|------|------|------|------|------|--------|
| `__name_pos` | 否 | str | None | name(名字) | 位置参数:列名 | 不写就用属性名;写成关键字 `name=` 也可以 |
| `__type_pos` | 否 | TypeEngine / 类型类 | None | type(类型) | 位置参数:列类型 | 必须排在列名之后;不写就靠 `Mapped[...]` 或外键推断 |
| `primary_key` | 否 | bool | `False` | primary(主要的)+ key(键)= 主键 | True 表示该列属于主键 | 多列都写 True 就是复合主键;主键列默认 NOT NULL |
| `nullable` | 否 | bool | 省略时推断 | null(空)+ able(能够)= 能不能为空 | 该列是否允许 NULL | 省略时:`Mapped[Optional[T]]` / `T \| None` 推断为可空;否则非主键列默认 **True**,主键列默认 **False** |
| `index` | 否 | bool | `None` | index(索引)= 索引 | True 时自动给该列建 `Index` | 默认是 `None` 不是 `False`;`None` 表示不显式指定 |
| `unique` | 否 | bool | `None` | unique(唯一的)= 唯一 | True 时自动建 `UniqueConstraint` | 同时 `index=True` 时建的是唯一索引,不是 UniqueConstraint |
| `default` | 否 | 任意值 / 可调用 | 未指定 | default(默认)= 默认值 | 客户端 INSERT 默认值 | `default=None` 表示该列没有 default generator,不等于「默认值就是 NULL」 |
| `server_default` | 否 | str / TextClause / FetchedValue | `None` | server(服务器)+ default(默认)= 服务端默认 | 生成 DDL 的 `DEFAULT` | 只在建表时生效,表已存在不会回改 |
| `onupdate` | 否 | 任意值 / 可调用 | `None` | on(在……时)+ update(更新)= 更新时 | UPDATE 语句里没显式给值时自动填入 | 由 SQLAlchemy 生成值,不是数据库行为 |
| `server_onupdate` | 否 | 同上 | `None` | server + onupdate = 服务端更新时 | 声明「数据库自己会在 UPDATE 时填」 | 只是告诉 ORM 更新后要重新取,不会生成触发器 |
| `insert_default` | 否 | 任意值 / 可调用 | 未指定 | insert(插入)+ default(默认)= 插入默认 | 只设置 `Column.default` | 会覆盖 `default` 传到 Column 的那一份;dataclass 的 `__init__` 仍用 `default` |
| `default_factory` | 否 | Callable[[], T] | 未指定 | factory(工厂)= 产值的工厂 | 只给 dataclass `__init__` 用的默认值工厂 | 不是数据库默认,库里不会有任何 DEFAULT |
| `autoincrement` | 否 | bool / `'auto'` | `'auto'` | auto(自动)+ increment(递增)= 自动递增 | 整数主键自动 SERIAL/IDENTITY | 非整数主键要显式 `autoincrement=False` |
| `*args` | 否 | SchemaEventTarget | — | — | 额外的列级构造 | 常放 `ForeignKey("table.col")` |
| `comment` | 否 | str | `None` | comment(注释)= 列注释 | 列注释写进 DDL | 只影响建表,ORM 查询读不到 |
| `name` | 否 | str | `None` | name(名字) | 关键字形式指定列名 | 和位置参数列名二选一 |
| `type_` | 否 | TypeEngine | `None` | type(类型)+ `_`(避内置名) | 关键字形式指定类型 | 和位置参数类型二选一;写成 `type` 会撞 Python 内置函数 |
| `use_existing_column` | 否 | bool | `False` | use(使用)+ existing(已存在的)+ column(列) | 单表继承 / mixin 里复用父类同名列 | 只在继承或 mixin 场景有用,普通表写了没效果 |

## 进阶参数（次常用，仍需知道默认值）

| 参数 | 必填 | 类型 | 默认 | 词源 | 含义 | 易错点 |
|------|------|------|------|------|------|--------|
| `deferred` | 否 | bool | 未指定 | deferred(被推迟的) | 默认不查该列,访问属性时才发 SELECT | 它是 ORM 选项,不是 `Column` 参数 |
| `deferred_group` | 否 | str | `None` | deferred + group(组) | 开启 deferred 并挂到同名的组 | 同组列一起加载 |
| `deferred_raiseload` | 否 | bool | `None` | deferred + raise(抛错)+ load(加载) | 开启 deferred,误访问直接报错 | 用于强制发现意外的懒加载 |
| `init` | 否 | bool | 未指定 | init(初始化) | dataclass 是否把这个字段放进 `__init__` | 未指定时交给 dataclass 默认规则 |
| `repr` | 否 | bool | 未指定 | repr(表示) | dataclass 的 `__repr__` 是否包含该字段 | 同上 |
| `compare` | 否 | bool | 未指定 | compare(比较) | dataclass 的 `__eq__` 是否参与比较 | 同上 |
| `kw_only` | 否 | bool | 未指定 | kw(关键字)+ only(仅) | 该字段只能关键字传参 | 同上 |
| `hash` | 否 | bool / None | 未指定 | hash(哈希) | 是否参与 `__hash__` 计算 | 同上 |
| `doc` | 否 | str | `None` | doc(文档) | 属性的文档字符串 | 不进 DDL |
| `key` | 否 | str | `None` | key(键) | 属性在 `__dict__` 里的键名 | 与列名不同;跨表查询时按它引用列 |
| `info` | 否 | dict | `None` | info(信息) | 自定义元数据挂载点 | 框架或迁移工具会读,SQLAlchemy 不用 |
| `quote` | 否 | bool / None | `None` | quote(加引号) | 强制给列名加引号 | 仅影响标识符渲染 |
| `system` | 否 | bool | `False` | system(系统) | 标记系统列 | 数据库迁移等场景会用 |
| `active_history` | 否 | bool | `False` | active(活跃)+ history(历史) | 属性变更前先加载旧值 | 会增加一次查询开销 |
| `sort_order` | 否 | int | 未指定 | sort(排序)+ order(次序) | 控制 ORM 建表时列的先后 | 值相同则按默认次序 |
| `dataclass_metadata` | 否 | Mapping | 未指定 | dataclass + metadata(元数据) | 传给 dataclass 字段的 metadata | 仅与 dataclass 映射配合使用 |

## 和它一起出现的

- `Mapped[...]`:`mapped_column` 的类型和可空性来源。`Mapped[str]` 非空,`Mapped[Optional[str]]` / `Mapped[str | None]` 可空。
- `DeclarativeBase`:2.0 的声明式基类,`class Base(DeclarativeBase): pass` 后所有模型继承它。
- `ForeignKey`:放进 `*args`,如 `mapped_column(ForeignKey("user_account.id"))`,类型和可空性可从被引用列推断。
- `relationship`:在另一侧描述对象关系,常和 `ForeignKey` 配对使用。
- `Column`:1.x 的写法。`mapped_column` 是 2.0 的 ORM 版写法,能吃类型注解;没有注解时也能当 `Column` 用,但新代码优先 `mapped_column`。

## 入库记录

| 日期 | 动作 | 说明 |
|------|------|------|
| 2026-11-13 | 新建 | 按 SQLAlchemy 2.0 官方文档核对;纠正 `nullable` 默认值(省略时从类型注解推断,否则非主键 True / 主键 False,不是一律 False),并把 `index` / `unique` 的默认从 `False` 改为 `None` |
