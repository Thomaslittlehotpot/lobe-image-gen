# 🎨 Lobe Image Gen — LobeHub Skill

> 使用 Lobe 平台内置 `lh gen image` 命令生成图像，以及添加和管理自定义图像生成供应商。

## 🚀 功能

- **图像生成**：使用 Lobe 原生 `lh gen image` 命令出图
- **供应商管理**：帮用户接入新的生图 API 供应商
- **故障排查**：常见生图错误诊断与修复
- **全 Lobe 原生**：不依赖任何第三方 skill

## 📦 安装

在 Lobe 技能商店搜索 `lobe-image-gen` 一键安装。

或手动安装：
```bash
npx @lobehub/market-cli skills install lobe-image-gen
```

## 📋 使用

安装后，任何支持 Lobe 技能的 AI Agent 都可以调用本技能来：

1. 检测 Lobe 是否具备生图能力
2. 帮用户添加新的图像生成 API 供应商
3. 生成图像并处理异步轮询
4. 诊断 `InvalidProviderAPIKey`、`ServerError`、`522` 等常见错误

## 🔗 链接

- [LobeHub Skills Marketplace](https://lobehub.com/skills)
- [LobeHub](https://lobehub.com)
