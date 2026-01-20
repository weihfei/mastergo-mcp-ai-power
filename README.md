# MasterGo Design to Code

根据 MasterGo 设计图自动生成前端页面代码的 Kiro Power。

## ✨ 功能特点

- 🎨 支持多种技术栈：React、Vue 2/3、Angular、原生 HTML
- 🧩 支持主流组件库：Ant Design、Element Plus、Arco Design 等
- 💅 支持多种样式方案：CSS Modules、Tailwind、SCSS、Less
- 🔄 交互式问答，确保代码符合项目规范
- ⚡ 智能分析设计稿交互元素，按需生成交互逻辑

## 📦 安装

### 方式一：本地目录安装

1. 将此文件夹复制到本地
2. 打开 Kiro → Powers 面板
3. 点击 "Add Custom Power"
4. 选择 "Local Directory"
5. 输入文件夹完整路径

### 方式二：Git 仓库安装

1. 打开 Kiro → Powers 面板
2. 点击 "Add Custom Power"
3. 输入 Git 仓库 URL

## ⚙️ 配置

安装后需要配置 MasterGo Token：

1. 登录 [MasterGo](https://mastergo.com)
2. 进入账号设置 → API/开发者选项
3. 生成或复制 API Token
4. 编辑 `mcp.json`，将 `YOUR_MASTERGO_TOKEN` 替换为你的 Token

```json
{
  "mcpServers": {
    "mastergo": {
      "args": [
        "@mastergo/magic-mcp",
        "--token=你的Token",  // ← 替换这里
        "--url=https://mastergo.com"
      ]
    }
  }
}
```

## 🚀 使用方法

1. 在 MasterGo 中打开设计文件
2. 选择要转换的图层，复制链接
3. 在 Kiro 聊天中发送链接
4. 回答技术栈问题（框架、组件库、样式方案）
5. 选择需要的交互逻辑级别
6. 获得生成的代码

## 📝 使用示例

```
用户：https://mastergo.com/file/xxx?layer_id=xxx

Kiro：请问项目使用什么技术框架？
用户：Vue 3 + Element Plus + SCSS + TS

Kiro：我分析了设计稿，识别到以下交互元素...
      请问需要实现哪些交互逻辑？
用户：基础交互

Kiro：[生成代码]
```

## 📁 文件结构

```
mastergo-design-to-code/
├── POWER.md                    # 主文档
├── mcp.json                    # MCP 服务器配置
├── README.md                   # 安装说明（本文件）
└── steering/
    └── interactive-workflow.md # 交互流程指南
```

## ❓ 常见问题

**Q: Token 无效怎么办？**
A: 登录 MasterGo 重新生成 Token，更新 mcp.json 配置

**Q: 生成的代码不符合预期？**
A: 重新告诉 Kiro 你的技术栈，会重新生成

**Q: 如何修改交互逻辑？**
A: 告诉 Kiro 具体需要哪些交互，支持自定义选择

## 📄 License

MIT
