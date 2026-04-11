---
name: cq:status
description: 显示 cq 知识库统计信息 — 各层级数量（local/private/public）、领域分布、最近本地添加和可信度分布。
---

# /cq:status

显示 cq 知识库的摘要信息。

## 指令

1. 调用 `status` MCP 工具（无需参数）。
2. 按照以下格式将响应结果整理为可读摘要。

## 输出格式

使用以下结构展示结果：

```
## cq 知识库

### 层级分布
local: {count} | private: {count} | public: {count}

*local* = 仅在本机; *private* = 与同一 `CQ_ADDR` 的所有人共享; *public* = 开放知识库（尚未开放）。

### 领域分布
{domain}: {count} | {domain}: {count} | ...

### 最近本地添加
- {id}: "{summary}" ({relative time})
- ...

### 可信度分布
■ 0.7-1.0: {count} 个单元
■ 0.5-0.7: {count} 个单元
■ 0.3-0.5: {count} 个单元
■ 0.0-0.3: {count} 个单元
```

`tier_counts` 字段包含层级分布。展示响应中存在的所有层级，省略数量为 0 的层级。

`recent` 字段仅反映本地存储。当配置了远端且可达时，通过 `Client.Propose` 提交的知识单元会直接发送到远端，不会出现在此处。如果 `recent` 为空，将该部分渲染为 `（无最近本地添加）`，让用户了解范围。

如果响应中包含 `promoted_to_remote`，在层级分布之后添加以下行：

```
启动时已将 {promoted_to_remote} 个知识单元推送至远端知识库。
```

## 空知识库

当所有层级数量均为 0（或 `tier_counts` 不存在）时：

- **包含 `promoted_to_remote`：** 显示标题、层级分布行和推送行。省略领域分布、最近本地添加和可信度分布部分（无数据可展示）。
- **不包含 `promoted_to_remote`：** 仅显示："cq 知识库为空。知识单元可通过 `propose` 或 `/cq:reflect` 命令添加。"
