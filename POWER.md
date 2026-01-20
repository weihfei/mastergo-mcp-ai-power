---
name: "mastergo-design-to-code"
displayName: "MasterGo Design to Code"
description: "根据 MasterGo 设计图自动生成前端页面代码，支持多种框架和组件库，通过交互式问答确保代码符合项目规范"
keywords: ["mastergo", "design", "figma", "code-generation", "frontend", "ui"]
author: "Kiro User"
---

# MasterGo Design to Code

## Overview

这个 Power 帮助你将 MasterGo 设计图转换为符合项目规范的前端代码。通过交互式问答，确保生成的代码：

## Available Steering Files

- **interactive-workflow** - 详细的交互式代码生成流程指南

## 核心功能

通过交互式问答，确保生成的代码：

- 使用正确的技术栈和框架
- 集成项目指定的组件库
- 遵循团队的代码规范
- 适配项目的样式方案

## 交互式代码生成流程

**重要：当用户提供 MasterGo 设计链接时，必须按以下流程执行。**

### 第一步：确认项目环境

在获取设计数据之前，向用户询问以下问题：

#### 1. 技术框架
```
请问你的项目使用什么技术框架？
- React
- Vue 2
- Vue 3
- Angular
- 原生 HTML/CSS/JS
- 其他（请说明）
```

#### 2. 组件库
```
项目是否使用 UI 组件库？
- Ant Design (antd)
- Element UI / Element Plus
- Arco Design
- TDesign
- Material UI
- 无，使用原生组件
- 其他（请说明）
```

#### 3. 样式方案
```
项目使用什么样式方案？
- CSS Modules
- Styled Components
- Tailwind CSS
- Less
- Sass/SCSS
- 原生 CSS
- 其他（请说明）
```

#### 4. 代码规范（可选）
```
是否有特殊的代码规范要求？
- 组件命名规范（如 PascalCase）
- 文件组织结构
- 是否需要 TypeScript
- 其他要求
```

### 第二步：获取设计数据

收集完项目信息后：

1. 使用 `mcp__getDsl` 获取设计的 DSL 数据
2. 检查 `componentDocumentLinks` 是否有组件文档
3. 如有组件文档，使用 `mcp__getComponentLink` 获取

### 第三步：分析交互元素（重要！）

**获取 DSL 后，必须分析页面中的交互元素，并向用户确认需要实现哪些交互逻辑。**

#### 交互元素识别

根据 DSL 识别以下元素：
- 按钮（搜索、重置、创建、提交等）
- 输入框、下拉选择、日期选择器
- 开关、复选框、单选框
- 列表、表格、卡片
- 弹窗、抽屉
- 分页、标签页

#### 向用户展示并确认

```
我分析了设计稿，识别到以下可交互元素：

**表单交互：**
- 搜索按钮 → 点击触发搜索
- 重置按钮 → 清空筛选条件
- 输入框/下拉框 → 数据绑定

**列表交互：**
- 开关切换 → 启用/禁用（可选二次确认）
- 编辑链接 → 跳转/弹窗

**其他：**
- 创建按钮 → 打开创建弹窗

请问需要实现哪些交互逻辑？
1. 全部实现（含 API 调用、加载状态、错误处理）
2. 基础交互（表单绑定、按钮点击事件）
3. 不需要，只要静态页面
4. 自定义选择
```

### 第四步：根据选择生成代码

| 用户选择 | 生成内容 |
|---------|---------|
| 全部实现 | 完整交互逻辑 + API 占位 + 状态管理 + 错误处理 |
| 基础交互 | 事件处理函数 + 数据绑定 |
| 静态页面 | 只有 UI 结构 + 静态 mock 数据 |
| 自定义 | 按用户指定的交互项生成 |

### 第五步：生成代码

根据用户选择的技术栈和交互需求生成对应代码：

#### React + Ant Design 示例（含交互）
```jsx
import React, { useState, useEffect } from 'react';
import { Button, Input, Select, Card, Switch, message } from 'antd';
import styles from './AgentList.module.css';

const AgentList = () => {
  const [loading, setLoading] = useState(false);
  const [dataList, setDataList] = useState([]);
  const [filterForm, setFilterForm] = useState({ name: '', status: '' });

  // 搜索
  const handleSearch = async () => {
    setLoading(true);
    try {
      // const res = await api.search(filterForm);
      // setDataList(res.data);
    } finally {
      setLoading(false);
    }
  };

  // 重置
  const handleReset = () => {
    setFilterForm({ name: '', status: '' });
  };

  return (
    <div className={styles.container}>
      {/* 根据 DSL 生成的组件结构 */}
    </div>
  );
};

export default AgentList;
```

#### Vue 3 + Element Plus 示例（含交互）
```vue
<template>
  <div class="container">
    <!-- 根据 DSL 生成的模板结构 -->
  </div>
</template>

<script setup lang="ts">
import { ref, reactive, onMounted } from 'vue';
import { ElMessage, ElMessageBox } from 'element-plus';

const loading = ref(false);
const dataList = ref([]);
const filterForm = reactive({ name: '', status: '' });

// 搜索
const handleSearch = async () => {
  loading.value = true;
  try {
    // const res = await api.search(filterForm);
    // dataList.value = res.data;
  } finally {
    loading.value = false;
  }
};

// 重置
const handleReset = () => {
  Object.assign(filterForm, { name: '', status: '' });
};

// 开关切换（带确认）
const handleSwitchChange = async (item, value) => {
  try {
    await ElMessageBox.confirm(`确定要${value ? '启用' : '禁用'}吗？`);
    // await api.updateStatus(item.id, value);
    ElMessage.success('操作成功');
  } catch {
    item.enabled = !value;
  }
};

onMounted(() => {
  handleSearch();
});
</script>

<style scoped>
/* 根据 DSL 生成的样式 */
</style>
```

#### 静态页面示例（不含交互）
```vue
<template>
  <div class="container">
    <!-- 只有 UI 结构，无交互逻辑 -->
  </div>
</template>

<script setup lang="ts">
// 静态 mock 数据，无交互逻辑
const dataList = [
  { id: 1, name: '示例', enabled: true }
];
</script>
```

## 组件映射规则

根据设计元素自动映射到对应组件库：

| 设计元素 | Ant Design | Element Plus | 原生 HTML |
|---------|------------|--------------|-----------|
| 按钮 | `<Button>` | `<el-button>` | `<button>` |
| 输入框 | `<Input>` | `<el-input>` | `<input>` |
| 下拉选择 | `<Select>` | `<el-select>` | `<select>` |
| 开关 | `<Switch>` | `<el-switch>` | `<input type="checkbox">` |
| 卡片 | `<Card>` | `<el-card>` | `<div class="card">` |
| 表格 | `<Table>` | `<el-table>` | `<table>` |

## 样式生成规则

### CSS Modules
```css
/* ComponentName.module.css */
.container { ... }
.header { ... }
```

### Tailwind CSS
```jsx
<div className="w-full p-4 bg-white rounded-lg shadow">
```

### Styled Components
```jsx
const Container = styled.div`
  width: 100%;
  padding: 16px;
`;
```

## 代码质量检查清单

生成代码后，确保：

- [ ] 使用了用户指定的组件库
- [ ] 样式方案与项目一致
- [ ] 组件命名符合规范
- [ ] 颜色值提取为变量/Token
- [ ] 响应式适配（如需要）
- [ ] 无障碍属性（aria-label 等）

## Onboarding

### 前置条件

1. **MasterGo 账号** - 需要有 MasterGo 账号和设计文件访问权限
2. **API Token** - 从 MasterGo 获取 API Token

### 获取 API Token

1. 登录 [MasterGo](https://mastergo.com)
2. 进入账号设置
3. 找到 API/开发者选项
4. 生成或复制你的 API Token

### 安装配置

在 `.kiro/settings/mcp.json` 中添加以下配置：

```json
{
  "mcpServers": {
    "mastergo": {
      "command": "npx",
      "args": [
        "@mastergo/magic-mcp",
        "--token=YOUR_MASTERGO_TOKEN",
        "--url=https://mastergo.com"
      ]
    }
  }
}
```

## MCP 工具参考

### mcp__getDsl
获取设计文件的 DSL 数据。

**参数：**
- `shortLink` (可选): 短链接
- `fileId` (可选): 设计文件 ID
- `layerId` (可选): 图层 ID

### mcp__getComponentLink
获取组件文档数据。

**参数：**
- `url` (必需): 组件文档链接 URL

### mcp__getMeta
获取站点和页面元信息。

**参数：**
- `fileId` (必需): 设计文件 ID
- `layerId` (必需): 图层 ID

### mcp__getComponentGenerator
获取组件开发工作流。

**参数：**
- `rootPath` (必需): 项目根路径
- `fileId` (必需): 设计文件 ID
- `layerId` (必需): 图层 ID

## Troubleshooting

### 生成的代码不符合预期

**问题：** 组件库或样式方案不对
**解决：** 重新告诉我你的技术栈，我会重新生成

### 缺少组件映射

**问题：** 某些设计元素没有正确映射到组件
**解决：** 告诉我具体哪个元素需要用什么组件，我会调整

### Token 无效

**问题：** 提示 Token 无效或过期
**解决：** 登录 MasterGo 重新生成 Token，更新配置

## Best Practices

- **先回答问题** - 完成技术栈问答后再生成代码，确保一次到位
- **提供项目示例** - 如果有现有代码，可以分享给我参考风格
- **分步生成** - 复杂页面可以分组件逐步生成
- **及时反馈** - 如果生成结果不满意，告诉我具体问题

## MCP Config Placeholders

- **`YOUR_MASTERGO_TOKEN`**: 你的 MasterGo API Token
  - **获取方式：** 登录 MasterGo → 账号设置 → API/开发者选项 → 生成 Token

---

**Package:** `@mastergo/magic-mcp`
**MCP Server:** mastergo
