# str.encode

- 语言: Python
- 库/模块: 内置类型 `str` 的方法(内置类型总览 `stdtypes`)
- 导入: 无需导入
- 用途: 把 `str` 按指定编码转成 `bytes`;凡是要喂给 `hmac` / `hashlib` 的文本密钥或消息,都要先过这一步
- 官方文档: https://docs.python.org/3/library/stdtypes.html#str.encode

## 词源

- `str` -> string(字符串)= 文本类型
- `encode` -> en(使…)+ code(编码)= 把文本编码成字节
- `encoding` -> encoding(编码方式)= 用哪张字符集/编码表
- `errors` -> errors(错误处理策略)= 编不动的时候怎么办
- `utf-8` -> UCS Transformation Format 8-bit,UTF(通用转换格式)= 变长 Unicode 编码,兼容 ASCII

## 最小例子

```python
secret = "my-secret"

key = secret.encode()                    # 省略参数 == encode("utf-8")
key2 = secret.encode("utf-8")            # 显式写 utf-8
key3 = secret.encode(encoding="utf-8", errors="strict")
assert key == key2 == key3 == b"my-secret"

# 等价写法:bytes(str, encoding=...)
assert bytes(secret, encoding="utf-8") == secret.encode("utf-8")

# 拿到 bytes 才能交给 hmac / hashlib
import hashlib
import hmac
print(hmac.new(secret.encode("utf-8"), b"payload", hashlib.sha256).hexdigest())

# 反过来:bytes -> str
assert key.decode("utf-8") == secret
```

## 参数（常用在前）

| 参数 | 必填 | 类型 | 默认 | 词源 | 含义 | 易错点 |
|------|------|------|------|------|------|--------|
| `encoding` | 否 | str | `'utf-8'` | encoding(编码) | 目标编码名,如 `'utf-8'`、`'gbk'`、`'ascii'` | 位置-only 之前的常规参数,可用关键字;写错编码名抛 `LookupError` |
| `errors` | 否 | str | `'strict'` | errors(错误策略) | 编码出错时的处理方式 | 默认 `'strict'` 会抛 `UnicodeError`;`'ignore'` / `'replace'` 会静默丢字或换字符,签名场景**不要用** |

`errors` 常见取值(官方标准错误处理):

| 值 | 行为 |
|----|------|
| `'strict'`(默认) | 遇错抛 `UnicodeError` |
| `'ignore'` | 丢掉编不了的字符 |
| `'replace'` | 换成 `?` 等替代字符 |
| `'surrogateescape'` | 把无法编码的字节映射到 `U+DC80`–`U+DCFF`,可无损往返 |

## 和它一起出现的

- [`hmac.new`](../hmac/new.md):`key` 只收 bytes/bytearray,所以先 `secret.encode("utf-8")` 再传进去。
- [`hashlib.sha256`](../hashlib/sha256.md):`data` 同理只收 bytes-like,字符串要先 `encode`。
- `bytes(...)`:`bytes("abc")` 无 encoding 会 `TypeError: string argument without an encoding`;`bytes(s, encoding="utf-8")` 与 `s.encode("utf-8")` 等价,但 `bytes(n)` 是「n 个零字节」,语义容易混淆,推荐 `encode`。
- `bytes.decode`:`encode` 的逆运算,bytes → str。
- `str` 的编码属性:源文件默认 UTF-8(PEP 3120),所以 `encode()` 默认值就是 `'utf-8'`。

## 入库记录

| 日期 | 动作 | 说明 |
|------|------|------|
| 2026-11-13 | 新建 | 按官方文档核对签名 `str.encode(encoding='utf-8', errors='strict')`、返回 bytes、默认 `'strict'` 抛 `UnicodeError`。纠正用户曾写 `bytes(secret)` 导致 `TypeError: string argument without an encoding`,正确是 `secret.encode("utf-8")`;并说明 `bytes(s, encoding='utf-8')` 与之等价 |
