# hmac.new

- 语言: Python
- 库/模块: 标准库 `hmac`
- 导入: `import hmac`(常配 `import hashlib`)
- 用途: 用密钥 + 消息 + 摘要算法创建一个 HMAC 对象;Webhook 验签、签名计算的标准入口
- 官方文档: https://docs.python.org/3/library/hmac.html#hmac.new

## 词源

- `hmac` -> HMAC(Hash-based Message Authentication Code,基于哈希的消息认证码)
- `new` -> new(新的)= 新建一个 HMAC 对象(工厂函数,不是类构造)
- `key` -> key(钥匙/密钥)= 共享密钥
- `msg` -> message 的缩写 = 待认证的消息
- `digestmod` -> digest(摘要)+ mod(module/模块、修饰)= 用哪个摘要算法/构造器
- `hexdigest` -> hex(十六进制)+ digest(摘要)= 十六进制摘要
- `update` -> update(更新/追加)= 往对象里追加消息

## 最小例子

```python
import hashlib
import hmac

secret = "my-secret"          # str,来自配置
payload = b'{"action":"opened"}'  # 请求的原始字节

# key 必须是 bytes/bytearray:str 要先 encode
mac = hmac.new(secret.encode("utf-8"), payload, hashlib.sha256)
signature = mac.hexdigest()   # 64 位十六进制字符串

# 不给 msg,后面手动 update,两者等价
mac2 = hmac.new(secret.encode("utf-8"), digestmod=hashlib.sha256)
mac2.update(b'{"action":')    # 分多次 update 等于一次 update 拼接结果
mac2.update(b'"opened"}')
assert mac2.hexdigest() == signature

# 校验外部签名:不要用 ==
received = "sha256=" + signature
expected = "sha256=" + signature
assert hmac.compare_digest(received, expected)
```

## 参数（常用在前）

| 参数 | 必填 | 类型 | 默认 | 词源 | 含义 | 易错点 |
|------|------|------|------|------|------|--------|
| `key` | 是 | bytes / bytearray | — | key(密钥) | 共享密钥的原始字节 | **不是 str**。传 str 直接 `TypeError: string argument without an encoding`;正确写法 `secret.encode("utf-8")`,不要写 `bytes(secret)`(同样 TypeError) |
| `msg` | 否 | bytes-like | `None` | message(消息) | 初始消息;给了就等价于创建后立刻 `update(msg)` | 关键字名叫 `msg`,不叫 `message` / `messages`;传 str 会报错,要先 encode |
| `digestmod` | 是 | 名字 str / 构造器 / 模块 | 无(3.8 起必填) | digest(摘要)+ mod(算法) | 用哪个哈希算法 | 关键字名是 `digestmod`,写成 `shema` / `digest` 之类会 `TypeError`;推荐传构造器 `hashlib.sha256`,传 `"sha256"` 也可 |

## 和它一起出现的

- [`HMAC.hexdigest`](hexdigest.md):`hmac.new(...)` 返回的是 **HMAC 对象**,要取字符串得调 `.hexdigest()`;直接 `.encode()` / `.encode(16)` 会报 `AttributeError`。
- [`hmac.compare_digest`](compare_digest.md):验签时比较外部签名和本地计算的签名,别用 `==`。
- [`hashlib.sha256`](../hashlib/sha256.md):最常用的 `digestmod`,传构造器本身;`"SHA256"` 这种大小写写法不保证可用,统一写 `hashlib.sha256` 或 `"sha256"`。
- [`str.encode`](../stdtypes/str-encode.md):把配置里的字符串密钥转成 `bytes`,是 `hmac.new` 之前的必备一步。
- `hmac.HMAC`:官方文档里的 HMAC 对象类。常规入口用 `hmac.new(...)`,不要直接 `hmac.HMAC(...)` 当工厂用。
- `hmac.digest(key, msg, digest)`:一次性算完直接返回 bytes,等价 `HMAC(key, msg, digest).digest()`,消息能全部放进内存时更快。

## 入库记录

| 日期 | 动作 | 说明 |
|------|------|------|
| 2026-11-13 | 新建 | 按 Python 3 官方 `hmac` 文档与本机 3.10 实测核对。纠正:①`key` 必须 bytes/bytearray,用户曾写 `bytes(secret)` 会 TypeError,正确 `secret.encode("utf-8")`;②参数名是 `key` / `msg` / `digestmod`,用户曾写 `shema="SHA256"`、`messages=payload`;③`hmac.new` 返回 HMAC 对象不是字符串,用户曾 `.encode` / `.encode(16)`,正确 `.hexdigest()`;④`digestmod` 自 3.8 起必填;⑤入口是 `hmac.new`,不把 `hmac.HMAC` 当常规构造入口 |
