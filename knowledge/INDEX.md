# 个人代码库索引

跨项目复用的代码知识库总索引。入库前先读 [PROTOCOL.md](PROTOCOL.md),新条目按 [_TEMPLATE.md](_TEMPLATE.md) 写。

去重键:`语言 + 库/模块 + 符号名`。带教时做 L2 提示先查这里。

## Python

### SQLAlchemy

| 符号 | 一句话用途 | 条目 |
|------|------------|------|
| `mapped_column` | 2.0 声明式映射里给属性绑定数据库列 | [python/sqlalchemy/mapped_column.md](python/sqlalchemy/mapped_column.md) |

### hmac

| 符号 | 一句话用途 | 条目 |
|------|------------|------|
| `hmac.new` | 创建 HMAC 对象 | [python/hmac/new.md](python/hmac/new.md) |
| `HMAC.hexdigest` | HMAC 结果转十六进制字符串 | [python/hmac/hexdigest.md](python/hmac/hexdigest.md) |
| `hmac.compare_digest` | 耗时固定比较,防时序攻击 | [python/hmac/compare_digest.md](python/hmac/compare_digest.md) |

### hashlib

| 符号 | 一句话用途 | 条目 |
|------|------------|------|
| `hashlib.sha256` | SHA-256 构造器,常作 HMAC 的 digestmod | [python/hashlib/sha256.md](python/hashlib/sha256.md) |

### 内置类型

| 符号 | 一句话用途 | 条目 |
|------|------------|------|
| `str.encode` | 字符串编码为 bytes | [python/stdtypes/str-encode.md](python/stdtypes/str-encode.md) |

### Starlette

| 符号 | 一句话用途 | 条目 |
|------|------------|------|
| `Request.body` | 读取请求原始字节（部分待核实） | [python/starlette/request-body.md](python/starlette/request-body.md) |
