# Request.body

- 语言: Python
- 库/模块: Starlette(`starlette.requests.Request`);FastAPI 复用它
- 导入: `from starlette.requests import Request` 或 `from fastapi import Request`
- 用途: 读 HTTP 请求的**原始字节**。Webhook 验签必须用原文,不能先 JSON 解析再序列化
- 官方文档: https://www.starlette.io/requests/ 与 https://fastapi.tiangolo.com/advanced/using-request-directly/
- 核实状态: **部分待核实**(见下)

## 词源

- `Request` -> request(请求)= 一次进来的 HTTP 请求
- `body` -> body(主体/正文)= 请求体,区别于 header / query / path
- `raw` -> raw(原始的)= 未经解析的字节流,验签要的就是它

## 核实状态（不要编造）

| 项 | 结论 | 依据 |
|----|------|------|
| 用途是读原始请求体 | 已确认 | FastAPI 文档明说直接读 body 不会被校验/转换/写进 OpenAPI |
| 是否 `async` | **待核实** | Starlette 官方页本轮 TLS 失败(`ECONNRESET`)未能打开;FastAPI 页未提。按 ASGI 惯例应是可 await 的协程方法,但**未从官方原文确认** |
| 返回类型 | **待核实** | 应为 `bytes`(原始字节),但同上未经官方原文确认 |
| 是否缓存(多次调用返回同一份) | **待核实** | 未从官方原文确认 |
| 参数 / 默认值 | **待核实** | 未从官方原文确认;不要写「默认值」之类字段,官方未核实就不填 |

想确认时:装好 starlette 后 `python -c "import inspect; from starlette.requests import Request; print(inspect.signature(Request.body), inspect.iscoroutinefunction(Request.body))"`,或改用 `request.stream()` 自行聚合。

## 最小例子

```python
# 示例写法按常见用法给出;async 与返回类型标注为待核实,不要当成官方原文
import hashlib
import hmac

from fastapi import FastAPI, Request   # 等价 from starlette.requests import Request

app = FastAPI()


@app.post("/webhook")
async def receive_webhook(request: Request):
    raw = await request.body()                       # 原始 bytes,验签的唯一正确输入
    signature = request.headers.get("x-hub-signature-256", "")

    secret = "my-secret"                             # 从配置读,不要硬编码
    expected = "sha256=" + hmac.new(
        secret.encode("utf-8"), raw, hashlib.sha256
    ).hexdigest()

    # 用耗时固定比较,不要 ==
    if not hmac.compare_digest(signature, expected):
        raise ValueError("signature mismatch")

    return {"ok": True}
```

反例(易错,不要这么写):

```python
# 错:JSON 反序列化 → 再序列化,字节序列可能和 GitHub 签名的原文不一致
# (键顺序、空格、\u 转义、数字格式都可能变)
data = await request.json()
raw = json.dumps(data).encode("utf-8")   # 拿去验签会随机失败
```

## 参数（常用在前）

| 参数 | 必填 | 类型 | 默认 | 词源 | 含义 | 易错点 |
|------|------|------|------|------|------|--------|
| — | — | — | 待核实 | — | 待核实:本轮未取得 Starlette 官方原文,不填默认值 | 不要凭印象写「无参数」以外的结论;拿到官方页后再补 |

## 和它一起出现的

- [`hmac.compare_digest`](../hmac/compare_digest.md):验签比较签名,别用 `==`。
- [`hmac.new`](../hmac/new.md):用 `raw` + 密钥算本地签名。
- [`hashlib.sha256`](../hashlib/sha256.md):`digestmod` 传构造器。
- [`str.encode`](../stdtypes/str-encode.md):密钥是 str,先 encode;`raw` 已经是 bytes,不要再 encode。
- `Request.json()`:解析成 Python 对象。**验签别用它**——重新序列化后的字节和签名原文通常不一致。
- `Request.stream()` / `Request.body()` 的上游是 ASGI `receive`;需要边读边处理大 body 时用 `stream()`。
- `Request.headers`:签名通常在 header 里,如 `x-hub-signature-256`。

## 入库记录

| 日期 | 动作 | 说明 |
|------|------|------|
| 2026-11-13 | 新建 | 用途与导入按 FastAPI 官方「Using Request directly」页确认;Starlette 官方页本轮 TLS 失败,故 `async`、返回类型、是否缓存、参数默认值一律标「待核实」,不编造。易错点来自带教:不要 `request.json()` 再 encode 去验签,编码结果可能与签名原文不一致 |
