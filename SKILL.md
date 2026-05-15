---
name: lobe-image-gen
description: 使用 Lobe 内置 lh gen 命令生成图像，以及添加和管理自定义图像生成供应商。全程不依赖第三方 skill，纯 Lobe 原生能力。
---

# Lobe 图像生成

## 概述
本技能指导 AI 使用 Lobe 平台内置的图像生成功能（lh gen image），以及帮用户添加和管理自定义图像生成供应商。全程不依赖第三方 skill，纯 Lobe 原生能力。

## 关键：执行环境
所有 lh 命令首选在云沙箱中执行（lobe-skills runCommand 或 lobe-cloud-sandbox runCommand），云沙箱已自带 CLI 认证。

如果云沙箱不可用，可引导用户在本地执行：npm install -g @lobehub/cli && LOBEHUB_JWT=eyJhbGciOiJSUzI1NiIsImtpZCI6ImI2ZGFmZWU1ZjNmMGE3MDUifQ.eyJwdXJwb3NlIjoiY2xpLXNhbmRib3giLCJzdWIiOiJ1c2VyX3h4b0JQODZ3QUh6ZFh3YkNrVFYxWnlnOUQ4dyIsImlhdCI6MTc3ODgzODM5NCwiZXhwIjoxNzc4ODM4Njk0fQ.hPT8xbYdUr1IEdkZcx0Ao2p-nNAmVuCU109YRURk-UcknDZDIlDAeqDIfS3A1UGxvaj-OvgKgOWO396i_r7nntmzuwKbCfA8FscfMQzmlREXHIs62cFJNck5QCK4heH-vuGd0GeLJegAVxXfpitJCqFIMDVHKimHiJct6nTsX2BxLJLUnxduyN6AelkJJ__eSh7zuJXDrsmw6ZmGearG0Nm-d9v9ScqRIl7xCKYCZkci3vsKAP6LJVms9v0awEV2L4gWFjUBuWKmznUtFB1BiNis87eadltAu-UPEOIImQGXHvY0M1ENk6h9kY3YIpcqty2oSQZ7W7Vz8yNI4AY8og LOBEHUB_SERVER=https://app.lobehub.com npx -y @lobehub/cli login。登录后本地 lh gen image 与云沙箱一致。

## 第一部分：检测环境
1. lh gen image --help 确认功能存在
2. lh provider list --json 列出供应商
3. lh model list <providerId> --json 筛选 type: image 的模型

## 第二部分：生成图像
lh gen image "提示词" -m <模型ID> -p <供应商ID>

轮询：lh gen status <gid> <tid> --json，每30秒一次，最多3分钟。success取asset.url。

## 第三部分：添加供应商
1. 收集端点URL、密钥、名称
2. curl验证API
3. 保存密钥（kv-env）
4. lh provider create --id <id> -n "<名>" -s custom --sdk-type openai
5. lh provider config <id> --api-key "<key>" --base-url "<url>"
6. lh provider toggle <id> --enable
7. 生图测试验证

## 第四部分：排查故障
InvalidProviderAPIKey=查密钥 | ServerError/522=curl测试 | 轮询超时=3分钟报告 | CLI找不到(云沙箱)=检查执行环境 | CLI找不到(本地)=npm install -g @lobehub/cli && LOBEHUB_JWT=eyJhbGciOiJSUzI1NiIsImtpZCI6ImI2ZGFmZWU1ZjNmMGE3MDUifQ.eyJwdXJwb3NlIjoiY2xpLXNhbmRib3giLCJzdWIiOiJ1c2VyX3h4b0JQODZ3QUh6ZFh3YkNrVFYxWnlnOUQ4dyIsImlhdCI6MTc3ODgzODM5NCwiZXhwIjoxNzc4ODM4Njk0fQ.hPT8xbYdUr1IEdkZcx0Ao2p-nNAmVuCU109YRURk-UcknDZDIlDAeqDIfS3A1UGxvaj-OvgKgOWO396i_r7nntmzuwKbCfA8FscfMQzmlREXHIs62cFJNck5QCK4heH-vuGd0GeLJegAVxXfpitJCqFIMDVHKimHiJct6nTsX2BxLJLUnxduyN6AelkJJ__eSh7zuJXDrsmw6ZmGearG0Nm-d9v9ScqRIl7xCKYCZkci3vsKAP6LJVms9v0awEV2L4gWFjUBuWKmznUtFB1BiNis87eadltAu-UPEOIImQGXHvY0M1ENk6h9kY3YIpcqty2oSQZ7W7Vz8yNI4AY8og LOBEHUB_SERVER=https://app.lobehub.com npx -y @lobehub/cli login

## Token节约
--json、30秒轮询、3分钟上限、curl --max-time 10

## 已验证环境
OpenAI GPT Image系列: OpenAI兼容API ✅通过 | 其他: ⚠️未测试
