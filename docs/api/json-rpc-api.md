# JSON-RPC API（手动触发工作流）

## 概览

该接口用于立即触发指定工作流，不需要等待定时任务。

- 协议：JSON-RPC 2.0
- 方法：`triggerWorkflow`
- 路径：`POST /api/workflow`
- 默认地址：`http://localhost:8000/api/workflow`

## 认证

请求必须带 Bearer Token：

```text
Authorization: Bearer <SERVER_API_KEY>
```

`SERVER_API_KEY` 来自 `.env`。

## 请求示例

```bash
curl -X POST http://localhost:8000/api/workflow \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your-api-key" \
  -d '{
    "jsonrpc": "2.0",
    "method": "triggerWorkflow",
    "params": {
      "workflowType": "weixin-article-workflow"
    },
    "id": 1
  }'
```

## 请求参数

```json
{
  "jsonrpc": "2.0",
  "method": "triggerWorkflow",
  "params": {
    "workflowType": "weixin-article-workflow"
  },
  "id": 1
}
```

- `jsonrpc`: 固定为 `2.0`
- `method`: 固定为 `triggerWorkflow`
- `params.workflowType`: 工作流类型
- `id`: 请求 ID（数字或字符串）

## 支持的 workflowType

- `weixin-article-workflow`
- `weixin-aibench-workflow`
- `weixin-hellogithub-workflow`

## 响应示例

成功：

```json
{
  "jsonrpc": "2.0",
  "result": {
    "success": true,
    "message": "工作流 weixin-article-workflow 已成功触发"
  },
  "id": 1
}
```

认证失败：

```json
{
  "jsonrpc": "2.0",
  "error": {
    "code": -32001,
    "message": "未授权的访问",
    "data": {
      "error": "缺少有效的 Authorization 请求头"
    }
  }
}
```

参数错误：

```json
{
  "jsonrpc": "2.0",
  "error": {
    "code": -32600,
    "message": "无效的 JSON-RPC 请求"
  },
  "id": "unknown"
}
```

## 常见错误码

| 错误码 | 含义 | 处理建议 |
| --- | --- | --- |
| `-32001` | 未授权 | 检查 `Authorization` 与 `SERVER_API_KEY` |
| `-32600` | 请求格式错误 | 检查 JSON-RPC 字段完整性 |
| `-32601` | 方法或路径错误 | 检查 `method` 与 `/api/workflow` |
| `-32603` | 服务内部错误 | 查看服务日志定位具体异常 |

## 联调建议

- 先用 `weixin-article-workflow` 做冒烟测试。
- 通过后再联调 `aibench` 与 `hellogithub`。
- 若部署在公网，建议在网关层增加来源限制与限流。
