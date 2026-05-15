---
name: lobe-image-gen
description: 使用 Lobe 内置 lh gen 命令生成图像，以及添加和管理自定义图像生成供应商。全程不依赖第三方 skill，纯 Lobe 原生能力。
---

# Lobe 图像生成

## 概述
本技能指导 AI 使用 Lobe 平台内置的图像生成功能（lh gen image），帮用户添加和管理自定义图像生成供应商。纯 Lobe 原生能力。

## 关键：执行环境与认证
lh 命令有两个可用的执行环境，按优先级尝试：

首选云沙箱（lobe-skills runCommand 或 lobe-cloud-sandbox runCommand），通常自带认证。如果 lh gen image --help 成功但实际生图报认证错误，说明沙箱未携带凭证（平台偶尔漏注），不要 lh login（无浏览器），切换至本地执行。

回退本地：npm install -g @lobehub/cli && LOBEHUB_JWT=eyJhbGciOiJSUzI1NiIsImtpZCI6ImI2ZGFmZWU1ZjNmMGE3MDUifQ.eyJwdXJwb3NlIjoiY2xpLXNhbmRib3giLCJzdWIiOiJ1c2VyX3h4b0JQODZ3QUh6ZFh3YkNrVFYxWnlnOUQ4dyIsImlhdCI6MTc3ODg0MDA5MiwiZXhwIjoxNzc4ODQwMzkyfQ.jlPMQRtt6ugp2eYezo0puSaUVLeaW0mtkJwZCvXGtqo1TrVu1SSS-Qurh0EZ7Dld5ugCxS5teob05bN8YfNnLxCzUCogKQxt-6P_nBESsisQ-LzzsPazqdUKP-1TT5NJgbRLkRwrNt67WiEKwYKjqTFlkK5ctNbABTQGwZzo4-0a64AUjCIdFnT2XkT8zNFxnU8nr4mQhyaJutVmE69QpyPcKg7zoIXl0kTfF2MkyevWYUKS4pyQNFfPmQnD4eylbrhkCnYBqTQLRXfXn48Ttw1X6I9_qk2aGnHFcdH5-NYbK7P-zWEvH7rB9vDASikiERaZlwFPiq1-opUo1F42Jw LOBEHUB_SERVER=https://app.lobehub.com npx -y @lobehub/cli login。登录后永久有效，不受沙箱状态影响。

## 检测环境
1. lh gen image --help 确认CLI存在
2. lh provider list --json 测试认证（成功=认证OK，失败=走本地）
3. lh model list <providerId> --json 筛选 type: image 的模型

## 生成图像
lh gen image "提示词" -m <模型ID> -p <供应商ID>
轮询: lh gen status <gid> <tid> --json 每30秒最多3分钟

## 添加供应商
1. 收集端点URL、密钥、名称
2. curl验证: curl -s --max-time 10 "<端点>/v1/models" -H "Authorization: Bearer <密钥>"
3. 保存密钥(kv-env)
4. lh provider create --id <id> -n "<名>" -s custom --sdk-type openai
5. lh provider config <id> --api-key "<key>" --base-url "<url>"
6. lh provider toggle <id> --enable
7. 生图测试

## 排查故障
InvalidProviderAPIKey=查密钥 | ServerError/522=curl测试 | 超时=3分钟 | 认证失败(云沙箱)=切本地 | CLI找不到(本地)=安装cli+login

## Token节约
--json、30秒轮询、3分钟上限、curl --max-time 10

## 已验证环境
OpenAI GPT Image系列: OpenAI兼容API 通过 | 其他: 未测试
