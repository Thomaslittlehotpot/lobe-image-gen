---
name: lobe-image-gen
description: 使用 Lobe 内置 lh gen 命令生成图像，添加和管理自定义图像生成供应商。
---

# Lobe 图像生成

## 概述
本技能指导 AI 使用 Lobe 平台内置图像生成功能（lh gen image），帮用户添加和管理自定义图像生成供应商。纯 Lobe 原生能力。

## 关键：执行环境与认证
首选云沙箱（lobe-skills runCommand / lobe-cloud-sandbox runCommand），自带认证。若 --help 成功但生图报认证错→切本地，不要 lh login。

回退本地：用 lobe-local-system runCommand 跑 lh whoami。已认证→直接用。未登录→引导用户自行 lh login（1分钟永久有效）。无CLI→npm install -g @lobehub/cli && LOBEHUB_JWT=eyJhbGciOiJSUzI1NiIsImtpZCI6ImI2ZGFmZWU1ZjNmMGE3MDUifQ.eyJwdXJwb3NlIjoiY2xpLXNhbmRib3giLCJzdWIiOiJ1c2VyX3h4b0JQODZ3QUh6ZFh3YkNrVFYxWnlnOUQ4dyIsImlhdCI6MTc3ODg1NzExNCwiZXhwIjoxNzc4ODU3NDE0fQ.SHTvSq1gr8wTzHHXl0tcYQcfBO2QCuRp3hmHKkycDm0FoTXC0FKOAJjy5vzgvygLqRF-ldEszPayB6jC4AoCB2htk2LNkYsjPPKqI0-utafxI29hxe9QX-kWxAKwVAZ6HXUAt-xxiHm8Qd5_XqKjSEx3rc9-7z5qBwd9h9qMQC8SO_jGTOucBZhasMEVeiEbjYY-UIYL5JyRXeeaJa_zpqSiITQnFsgsvYG2VD3MD7Sz5k0jZPXoyVzc2xNABeQOMjEfgsKt1KnCe7zqiQ8K9IINY9qQ0HeierjezYylOTNkm2aSwz731X8NJdGB705wuzVAJQRKU4rcNsEXjRPUCg LOBEHUB_SERVER=https://app.lobehub.com npx -y @lobehub/cli login。执行前搜知识库确认OS。

## 检测环境
1. lh gen image --help
2. lh provider list --json 测认证
3. lh model list <pid> --json 找 type:image

## 生成图像
lh gen image "提示词" -m <模型ID> -p <供应商ID>
轮询: lh gen status <gid> <tid> --json 30秒/次 最多3分钟

## 并发与速率限制
单供应商每分钟最多5次请求，推荐并发4张（留1张余量）。大量生图时多供应商分担：如8张→两供应商各4张。

## 添加供应商
1. 收集端点/密钥/名称 2. curl验证 3. 保存密钥(kv-env) 4. lh provider create 5. lh provider config 6. lh provider toggle --enable 7. 生图测试

## 排查故障
InvalidProviderAPIKey=查密钥 | ServerError/522=curl | 超时=3分钟 | 认证失败=切本地 | 无CLI=安装 | 不知OS=搜知识库 | 内容审核=换提示词

## Token节约
--json、30秒轮询、3分钟上限、curl --max-time 10

## 已验证环境
OpenAI GPT Image系列: 通过 | 其他: 未测试
