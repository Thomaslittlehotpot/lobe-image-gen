---
name: lobe-image-gen
description: 使用 Lobe 内置 lh gen 命令生成图像，以及添加和管理自定义图像生成供应商。全程不依赖第三方 skill，纯 Lobe 原生能力。适用于需要帮用户接入生图 API 并出图的场景。
---

# Lobe 图像生成

## 概述

本技能指导 AI 使用 Lobe 平台内置的图像生成功能（`lh gen image`），以及帮用户添加和管理自定义图像生成供应商。**全程不依赖第三方 skill，纯 Lobe 原生能力。**

## ⚠️ 关键：执行环境

所有 `lh` 命令必须在**云沙箱**中执行（`lobe-skills____runCommand` 或 `lobe-cloud-sandbox____runCommand`），不是在用户本地电脑上。云沙箱已自带 CLI 认证，无需额外登录。

---

## 第一部分：检测环境

### 1.1 确认 Lobe 生图功能存在
```bash
lh gen image --help
```
若返回帮助信息则功能可用。

### 1.2 列出已有供应商
```bash
lh provider list --json
```
关注其中 `enabled: true` 且来源为 `custom` 或支持图像类型的供应商。

### 1.3 查看供应商下的模型
```bash
lh model list <providerId> --json
```
筛选 `"type": "image"` 的模型——这些是可用于生图的模型。记下模型 ID。

---

## 第二部分：生成图像

### 2.1 基础生图
```bash
lh gen image "你的提示词" -m <模型ID> -p <供应商ID>
```

### 2.2 高级选项
| 参数 | 说明 | 示例 |
|------|------|------|
| `-m, --model` | 模型 ID（必填） | `-m <图像模型ID>` |
| `-p, --provider` | 供应商 ID（必填） | `-p <供应商ID>` |
| `-n, --num` | 生成数量 | `-n 2` |
| `--width` / `--height` | 像素尺寸 | `--width 1024 --height 1024` |
| `--json` | 输出结构化数据 | `--json` |

### 2.3 轮询结果（关键！）
生图是异步的。提交后获取 `generationId` 和 `asyncTaskId`，然后轮询：

```bash
lh gen status <generationId> <asyncTaskId> --json
```

轮询策略：
- 每 30 秒查一次
- 最多查 6 次（3 分钟上限）
- `status: "success"` → 从 `asset.url` 获取图片链接
- `status: "error"` → 检查 `error` 字段获取原因
- 3 分钟后仍 `processing` → 判定失败，报告用户

### 2.4 一键下载
```bash
lh gen download <generationId> <asyncTaskId>
```
注意：此命令会阻塞等待，建议设 timeout。

---

## 第三部分：添加新供应商

当用户有新的图像生成 API 需要接入时使用。

### 3.1 收集必要信息
向用户确认以下三项：
- **API 端点 URL**（如 `https://api.example.com` 或 `https://api.example.com/v1`）
- **API 密钥**
- **供应商显示名称**（用户自定义，如"我的生图站"）

### 3.2 先验证 API 可用性
```bash
curl -s --max-time 10 "<端点>/v1/models" -H "Authorization: Bearer <密钥>"
```

### 3.3 安全保存密钥
使用 Lobe 凭证系统，绝不在对话中明文暴露密钥。保存为 kv-env 类型凭证。

### 3.4 创建供应商
```bash
lh provider create --id <简短英文ID> -n "<显示名称>" -s custom --sdk-type openai -d "<描述>"
```

### 3.5 配置供应商
```bash
lh provider config <providerId> --api-key "<密钥>" --base-url "<端点>" --check-model <模型ID>
```

### 3.6 启用供应商
```bash
lh provider toggle <providerId> --enable
```

### 3.7 验证：做一次生图测试

---

## 第四部分：排查故障

| 症状 | 可能原因 | 检查方法 |
|------|---------|---------|
| InvalidProviderAPIKey | 密钥错误或未配置 | lh provider config 查看 |
| ServerError / 522 | API 后端宕机 | curl 直接访问端点 |
| 提交成功但轮询超时 | 异步任务卡住 | 3 分钟后报告用户 |
| 模型不显示 | 供应商未启用 | 启用后重试 |
| CLI 命令找不到 | AI 用错执行环境 | 确认在云沙箱执行 |

---

## 第五部分：Token 节约原则

1. 列供应商/模型用 --json 避免大段文本输出
2. 生图轮询每 30 秒一次，不要更频繁
3. 3 分钟没出图直接判定失败，不等
4. curl 测试 API 时用 --max-time 10 避免挂死

---

## 已验证环境

| 模型系列 | 供应商类型 | 测试结果 |
|---------|-----------|---------|
| OpenAI GPT Image 系列 | OpenAI 兼容 API | ✅ 通过 |
| 其他 OpenAI 兼容图像模型 | OpenAI 兼容 API | ⚠️ 未测试 |

> 本技能的方法论适用于所有 OpenAI SDK 兼容的图像生成 API。如果使用了其他模型并测试成功，欢迎反馈。
