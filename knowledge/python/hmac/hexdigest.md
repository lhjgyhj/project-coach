# HMAC.hexdigest

- 语言: Python
- 库/模块: 标准库 `hmac`(`hmac.HMAC` 的方法)
- 导入: `import hmac`;对象来自 `hmac.new(...)`
- 用途: 把 HMAC 对象的摘要转成只含十六进制数字的字符串,便于放进 Header、JSON、邮件等纯文本通道
- 官方文档: https://docs.python.org/3/library/hmac.html#hmac.HMAC.hexdigest

## 词源

- `hex` -> hexadecimal(十六进制)的缩写
- `digest` -> digest(摘要)= 哈希运算的固定长度结果
- `hexdigest` -> 十六进制 + 摘要 = 摘要的十六进制写法

## 最小例子

```python
import hashlib
import hmac

mac = hmac.new(b"secret", b"payload", hashlib.sha256)
raw = mac.digest()        # bytes,32 字节
text = mac.hexdigest()    # str,64 个字符
assert len(text) == 2 * len(raw)
assert set(text) <= set("0123456789abcdef")

# 常见用法:拼成 GitHub 风格的 header 值
header_value = "sha256=" + text

# 校验外部传来的 hexdigest
assert hmac.compare_digest(header_value, "sha256=" + text)
```

## 参数（常用在前）

| 参数 | 必填 | 类型 | 默认 | 词源 | 含义 | 易错点 |
|------|------|------|------|------|------|--------|
| 无 | — | — | — | — | 不加参数,直接返回 `str` | 它是 HMAC 对象的**方法**,要在 `hmac.new(...)` 的返回值上调用;对返回值再 `.encode()` 是错的,已经拿到 str,要 bytes 用 `.digest()` |

返回值约定:

- 类型 `str`,只含 `0-9a-f`,长度是 `.digest()` 的**两倍**(SHA-256 为 32 字节 → 64 字符)。
- 小写十六进制,不带 `sha256=` 前缀,前缀要自己拼。

## 和它一起出现的

- [`hmac.new`](new.md):`hexdigest` 只能挂在它的返回值上。
- [`hmac.compare_digest`](compare_digest.md):拿 `hexdigest` 和外部提供的摘要比较时用它,不要用 `==`。
- `HMAC.digest`:`hexdigest` 的 bytes 版本。要写二进制或做进一步编码时用它。
- `HMAC.update`:先 `update` 完所有分片,再 `hexdigest`;`hexdigest` 本身是终结动作。

## 入库记录

| 日期 | 动作 | 说明 |
|------|------|------|
| 2026-11-13 | 新建 | 按官方文档核对:无参数、返回 hex str、长度为 digest 两倍。纠正用户把 `hmac.new(...)` 的返回值当字符串去 `.encode()` / `.encode(16)` 的写法,正确是 `.hexdigest()`;并强调用 `compare_digest` 而非 `==` 做校验 |
