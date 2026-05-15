---
name: lobe-image-gen
description: 使用 Lobe 内置 lh gen 命令生成图像，以及添加和管理自定义图像生成供应商。
---

# Lobe 图像生成

## 概述
本技能指导 AI 使用 Lobe 平台内置的图像生成功能（lh gen image），帮用户添加和管理自定义图像生成供应商。纯 Lobe 原生能力。

## 关键：执行环境与认证
lh 命令有两个可用环境，按优先级：

首选云沙箱（lobe-skills runCommand / lobe-cloud-sandbox runCommand），通常自带认证。若 --help 成功但生图报认证错→切本地，不要 lh login（沙箱无浏览器）。

回退本地：用 lobe-local-system runCommand 在用户本地执行 lh whoami。已认证→直接用。未登录→引导用户自行在终端跑 lh login（1分钟，永久有效）。CLI不存在→npm install -g @lobehub/cli && LOBEHUB_JWT=eyJhbGciOiJSUzI1NiIsImtpZCI6ImI2ZGFmZWU1ZjNmMGE3MDUifQ.eyJwdXJwb3NlIjoiY2xpLXNhbmRib3giLCJzdWIiOiJ1c2VyX3h4b0JQODZ3QUh6ZFh3YkNrVFYxWnlnOUQ4dyIsImlhdCI6MTc3ODg0Mzk0NywiZXhwIjoxNzc4ODQ0MjQ3fQ.HIrWKW282DRcYnN8bHb6E8WAuGkmRVq9Mb70yD0SHR1Rffyc9qIZFMJVVOTqQDKFCNPWaGQYKVKvjUmnT4iNe2bHu5nr0wlpiKQAgURAisW4HXiFOFDiEL2CaxCeJLipn4atkQsm4mND3xCtIIXZMZW8717QPc_QRmYEjt0kRF_k2kfL6BhxLLKAMIqDkemlgjt18Iu8QdN2hNmjmB9av2iOsW2VCrUPMZBv8P_VukXOeFO6O1fxHDsIc36bnWFHxDZN9bBG9aQyRTsrhxGyoj-LSY5O7Im9slpCi6N5h1XtKXAFS7dB7H0pHVmnS-SWlX8Iaz5pfSbTQi-B1AciLA LOBEHUB_SERVER=https://app.lobehub.com npx -y @lobehub/cli login。

本地执行前先搜知识库「用户系统环境」确认OS和路径。Windows路径用反斜杠。

## 检测环境
1. lh gen image --help
2. lh provider list --json 测认证
3. lh model list <pid> --json 找 type:image

## 生成图像
lh gen image "提示词" -m <模型ID> -p <供应商ID>
轮询: lh gen status <gid> <tid> --json 30秒/次 ≤3分钟

## 添加供应商
1. 收集端点、密钥、名称
2. curl验证: curl -s --max-time 10 "端点/v1/models" -H "Authorization: Bearer 密钥"
3. 保存密钥(kv-env)
4. lh provider create --id <id> -n "名" -s custom --sdk-type openai
5. lh provider config <id> --api-key "key" --base-url "url"
6. lh provider toggle <id> --enable
7. 生图测试

## 排查故障
InvalidProviderAPIKey=查密钥 | ServerError/522=curl | 超时=3分钟 | 认证失败=切本地(lobe-local-system跑whoami) | 本地无CLI=安装+login | 不知系统环境=搜知识库/问用户

## Token节约
--json、30秒轮询、3分钟上限、curl --max-time 10

## 已验证环境
OpenAI GPT Image系列: 通过 | 其他: 未测试
