---
name: lobe-image-gen
description: 使用 Lobe 内置 lh gen 命令生成图像，以及添加和管理自定义图像生成供应商。
---

# Lobe 图像生成

## 概述
本技能指导 AI 使用 Lobe 平台内置的图像生成功能（lh gen image），帮用户添加和管理自定义图像生成供应商。纯 Lobe 原生能力。

## 关键：执行环境与认证
lh 命令有两个可用环境，按优先级尝试：

首选云沙箱（lobe-skills runCommand / lobe-cloud-sandbox runCommand），通常自带认证。若 --help 成功但生图报认证错，切换本地。

回退本地：先 lh whoami 检测。已认证→直接用。未登录→引导用户自行在终端跑一次 lh login（需浏览器确认，1分钟，永久有效）。CLI不存在→npm install -g @lobehub/cli && LOBEHUB_JWT=eyJhbGciOiJSUzI1NiIsImtpZCI6ImI2ZGFmZWU1ZjNmMGE3MDUifQ.eyJwdXJwb3NlIjoiY2xpLXNhbmRib3giLCJzdWIiOiJ1c2VyX3h4b0JQODZ3QUh6ZFh3YkNrVFYxWnlnOUQ4dyIsImlhdCI6MTc3ODg0MzMzMiwiZXhwIjoxNzc4ODQzNjMyfQ.U3Zyyu-iKIoPS_VIEk5N6ira6kaBVJs6O4j98sovf4y24W-KwIU3_Voj6np0BpVS_6uCBy_4N2hMo5ex2l2pLlNrtWXISKsDyTNfOlnW5QBi07KyIS9UczdGWV-ET4piMQPTQUjyRmhDw_XOUegfPtmEDgKsLKyXSWEbDiFodYImVE2SknvsEWILoJo_2liacQUYPheXvvcwnNVvQuj3zFzVQ4LxESyk2CZLtQ02M-rrlfQ5Jc7gCCbgNUEIMjHC9njsy1IASGd_PNrsjTUjEdmesP7pZ8q6wKOf-Wd3K8jOVwLabvet6m5qYKoybpduTpY8o0_TEqzoUfOJRb3xVg LOBEHUB_SERVER=https://app.lobehub.com npx -y @lobehub/cli login。

## 检测环境
1. lh gen image --help
2. lh provider list --json 测认证（失败切本地）
3. lh model list <pid> --json 找 type:image

## 生成图像
lh gen image "提示词" -m <模型ID> -p <供应商ID>
轮询: lh gen status <gid> <tid> --json 30秒/次 最多3分钟

## 添加供应商
1. 收集端点、密钥、名称
2. curl验证: curl -s --max-time 10 "端点/v1/models" -H "Authorization: Bearer 密钥"
3. 保存密钥(kv-env)
4. lh provider create --id <id> -n "名" -s custom --sdk-type openai
5. lh provider config <id> --api-key "key" --base-url "url"
6. lh provider toggle <id> --enable
7. 生图测试

## 排查故障
InvalidProviderAPIKey=查密钥 | ServerError/522=curl | 超时=3分钟 | 认证失败=切本地(whoami检测→未登录则引导login) | 本地无CLI=安装+login

## Token节约
--json、30秒轮询、3分钟上限、curl --max-time 10

## 已验证环境
OpenAI GPT Image系列: 通过 | 其他: 未测试
