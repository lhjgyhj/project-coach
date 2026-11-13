# hmac.compare_digest

- 语言: Python
- 库/模块: 标准库 `hmac`
- 导入: `import hmac`
- 用途: 密码学等价的 `a == b`:比较长度固定、不按内容提前短路,避免通过响应时间泄露签名信息
- 官方文档: https://docs.python.org/3/library/hmac.html#hmac.compare_digest

## 词源

- `compare` -> compare(比较)
- `digest` -> digest(摘要)= 被比较的对象通常是摘要
- `compare_digest` -> 比较摘要 = 专为「比较摘要/签名」设计的比较函数
- `timing` -> timing(计时)= 时序攻击靠的就是比较耗时

## 最小例子

```python
import hashlib
import hmac

secret = "my-secret"
payload = b'{"action":"opened"}'
expected = hmac.new(secret.encode("utf-8"), payload, hashlib.sha256).hexdigest()

received = "sha256=" + expected          # 模拟 Header 里收到的签名
expected_header = "sha256=" + expected

# 对:耗时与内容无关
if not hmac.compare_digest(received, expected_header):
    raise ValueError("signature mismatch")

# 错:== 会按内容短路,泄漏前缀匹配长度
# if received != expected_header:
#     raise ValueError("signature mismatch")

# bytes 与 bytes 也可以
ok = hmac.compare_digest(
    hmac.new(b"secret", payload, hashlib.sha256).digest(),
    hmac.new(b"secret", payload, hashlib.sha256).digest(),
)
```

## 参数（常用在前）

| 参数 | 必填 | 类型 | 默认 | 词源 | 含义 | 易错点 |
|------|------|------|------|------|------|--------|
| `a` | 是 | str(仅 ASCII) / bytes-like | — | a(第一个操作数) | 比较的一侧,通常是收到的签名 | 与 `b` **必须同类型**:str 对 bytes 直接 `TypeError: a bytes-like object is required, not 'str'` |
| `b` | 是 | str(仅 ASCII) / bytes-like | — | b(第二个操作数) | 另一侧,通常是本地算出的签名 | 同上;两个 str 只能是 ASCII,含非 ASCII 字符会报 `TypeError` |

行为说明:

- 返回 `bool`,语义就是 `a == b`,只是不按内容短路径。
- 长度不同**理论上**可能从耗时泄露「长度」,但不泄露值;所以别把长度当秘密。
- 参数是位置-only(`/`),不会接受 `a=` / `b=` 关键字。

## 和它一起出现的

- [`HMAC.hexdigest`](hexdigest.md):返回的 hex str 是 ASCII,正好可以直接喂给 `compare_digest`。
- [`hmac.new`](new.md):本地签名由它算出来,再交给 `compare_digest`。
- `secrets.compare_digest`:`hmac.compare_digest` 的别名(同一个 C 实现),比较 token、API key 时也会用。
- `HMAC.digest`:想比较 bytes 而不是 hex str 时用它,两侧都是 bytes 即可。

## 入库记录

| 日期 | 动作 | 说明 |
|------|------|------|
| 2026-11-13 | 新建 | 按官方文档核对:返回 `a == b`,避免按内容短路,适合密码学比较;`a`、`b` 必须同类型(ASCII str 或 bytes-like),长度不同理论上可能泄露长度而非值。纠正用户想用 `==` 比较签名的做法,改 `compare_digest` |
