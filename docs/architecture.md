# 架构总览

## 运行结构

TrendPublish 启动后会同时运行两条主链路：

1. 定时任务链路（cron）
2. JSON-RPC 手动触发链路（HTTP API）

## 核心模块

- 入口：`src/index.ts`
- 定时调度：`src/controllers/cron.ts`
- API 服务：`src/server.ts`
- 手动触发控制器：`src/controllers/workflow.controller.ts`
- 工作流实现：`src/services/*.workflow.ts`
- 抓取器：`src/modules/scrapers/*`
- 摘要与排序：`src/modules/summarizer`、`src/modules/content-rank`
- 渲染与模板：`src/modules/render/weixin/*`
- 发布器：`src/modules/publishers/weixin.publisher.ts`
- 通知器：`src/modules/notify/*`

## 执行流程

1. 采集数据（多源抓取）
2. 内容排序与去重（可选）
3. LLM 摘要生成
4. 模板渲染为图文
5. 发布到公众号
6. 发送通知（Bark/钉钉/飞书）

## 工作流类型

- `weixin-article-workflow`
- `weixin-aibench-workflow`
- `weixin-hellogithub-workflow`

这些类型既可被 cron 调用，也可通过 `triggerWorkflow` API 手动触发。

## 时区与调度

- 内置 cron: 每天 `03:00`
- 时区: `Asia/Shanghai`
- 每日工作流通过 `N_of_week_workflow`（1-7）读取
