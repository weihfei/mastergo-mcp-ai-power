# 交互式代码生成工作流

当用户提供 MasterGo 设计链接要求生成代码时，**必须按以下流程执行**。

## 流程概览

```
用户提供设计链接
    ↓
询问技术栈问题（4个问题）
    ↓
获取 DSL 数据
    ↓
分析页面交互元素
    ↓
向用户展示可实现的交互逻辑，询问是否需要
    ↓
根据用户选择生成代码（含/不含交互逻辑）
    ↓
确认是否满意
```

## 第一阶段：收集项目信息

### 必问问题（按顺序）

**问题 1：技术框架**
```
在生成代码之前，我需要了解一下你的项目环境。

请问项目使用什么技术框架？
1. React
2. Vue 2
3. Vue 3
4. Angular
5. 原生 HTML/CSS/JS
6. 其他（请说明）
```

**问题 2：组件库**
```
项目是否使用 UI 组件库？
1. Ant Design (antd)
2. Element UI / Element Plus
3. Arco Design
4. TDesign
5. Material UI
6. Naive UI
7. 不使用，用原生组件
8. 其他（请说明）
```

**问题 3：样式方案**
```
项目使用什么样式方案？
1. CSS Modules
2. Styled Components / Emotion
3. Tailwind CSS
4. Less
5. Sass/SCSS
6. 原生 CSS
7. 其他（请说明）
```

**问题 4：其他要求（可选，可合并到前面问题中）**
```
还有其他要求吗？比如：
- 是否需要 TypeScript？
- 组件命名规范？
- 文件结构偏好？
- 响应式需求？

没有特殊要求可以直接说"没有"。
```

### 快捷回答

如果用户一次性提供了所有信息，如：
> "React + Ant Design + CSS Modules + TypeScript"

则无需逐个询问，直接进入下一阶段。

## 第二阶段：获取设计数据

### 解析设计链接

从用户提供的链接中提取参数：

**短链接格式：**
```
https://mastergo.com/goto/LhGgBAK
```
→ 直接使用 shortLink 参数

**完整链接格式：**
```
https://mastergo.com/file/171080253797398?page_id=M&layer_id=1:263
```
→ 提取 fileId: `171080253797398`
→ 提取 layerId: `1:263`（注意 URL 编码 `%3A` = `:`）

### 调用 MCP 工具

```javascript
// 使用 mcp__getDsl 获取设计数据
const dsl = await mcp__getDsl({
  fileId: "171080253797398",
  layerId: "1:263"
});

// 如果有组件文档链接，获取组件文档
if (dsl.componentDocumentLinks?.length > 0) {
  for (const url of dsl.componentDocumentLinks) {
    const componentDoc = await mcp__getComponentLink({ url });
    // 使用组件文档指导代码生成
  }
}
```

## 第二点五阶段：分析交互元素并询问用户

**获取 DSL 数据后，必须分析页面中的交互元素，并向用户展示可实现的交互逻辑。**

### 交互元素识别规则

根据 DSL 数据识别以下交互元素：

| DSL 特征 | 交互元素类型 | 可实现的交互逻辑 |
|---------|------------|----------------|
| 按钮样式的 FRAME + 文字"搜索/查询" | 搜索按钮 | 点击触发搜索、加载状态 |
| 按钮样式的 FRAME + 文字"重置/清空" | 重置按钮 | 点击清空表单 |
| 按钮样式的 FRAME + 文字"创建/新增/添加" | 创建按钮 | 点击打开弹窗/跳转页面 |
| 输入框样式的 FRAME | 输入框 | 双向绑定、输入验证、防抖搜索 |
| 下拉箭头 + 选项文字 | 下拉选择 | 选项切换、联动筛选 |
| 开关/Toggle 样式 | 开关 | 状态切换、确认提示 |
| 卡片列表 | 列表 | 分页加载、无限滚动、空状态 |
| 文字"编辑/删除/查看" | 操作链接 | 点击事件、确认弹窗 |
| 表格结构 | 数据表格 | 排序、筛选、分页、选择 |
| 弹窗/模态框 | 弹窗 | 打开/关闭、表单提交 |

### 分析并展示给用户

分析完 DSL 后，向用户展示识别到的交互元素和可实现的逻辑：

```
我分析了设计稿，识别到以下可交互元素：

**表单交互：**
- [ ] 搜索按钮 → 点击触发搜索请求
- [ ] 重置按钮 → 点击清空筛选条件
- [ ] 输入框 → 双向数据绑定
- [ ] 下拉选择 → 选项切换筛选

**列表交互：**
- [ ] 卡片列表 → 数据渲染、空状态处理
- [ ] 开关切换 → 启用/禁用状态切换（可选：二次确认）
- [ ] 编辑链接 → 点击跳转/打开编辑弹窗

**其他：**
- [ ] 创建按钮 → 点击打开创建弹窗/跳转创建页

请问需要实现哪些交互逻辑？
1. 全部实现
2. 只实现基础交互（表单绑定、按钮点击）
3. 不需要交互，只要静态页面
4. 自定义选择（告诉我具体需要哪些）
```

### 根据用户选择决定代码生成方式

**用户选择"全部实现"或"基础交互"：**
- 生成完整的响应式数据
- 生成事件处理函数（含具体逻辑）
- 生成 API 调用占位
- 生成加载状态、空状态处理

**用户选择"不需要交互"：**
- 只生成静态 UI 结构
- 数据使用静态 mock
- 事件处理函数只保留空壳或不生成

**用户选择"自定义"：**
- 根据用户指定的交互项生成对应逻辑

### 交互逻辑代码模板

**搜索功能（完整实现）：**
```typescript
// 搜索状态
const loading = ref(false)

// 搜索方法
const handleSearch = async () => {
  loading.value = true
  try {
    const params = { ...filterForm }
    const res = await api.search(params)
    dataList.value = res.data
  } catch (error) {
    ElMessage.error('搜索失败')
  } finally {
    loading.value = false
  }
}

// 重置方法
const handleReset = () => {
  Object.assign(filterForm, {
    name: '',
    status: '',
    category: ''
  })
  handleSearch()
}
```

**开关切换（带确认）：**
```typescript
const handleSwitchChange = async (item: DataItem, value: boolean) => {
  try {
    await ElMessageBox.confirm(
      `确定要${value ? '启用' : '禁用'}「${item.name}」吗？`,
      '提示',
      { type: 'warning' }
    )
    await api.updateStatus(item.id, value)
    ElMessage.success('操作成功')
  } catch {
    // 用户取消，恢复原状态
    item.enabled = !value
  }
}
```

**列表加载（带空状态）：**
```typescript
const dataList = ref<DataItem[]>([])
const loading = ref(false)
const isEmpty = computed(() => dataList.value.length === 0)

const fetchList = async () => {
  loading.value = true
  try {
    const res = await api.getList()
    dataList.value = res.data
  } finally {
    loading.value = false
  }
}

onMounted(() => {
  fetchList()
})
```

**搜索功能（静态版本 - 不需要交互时使用）：**
```typescript
// 只保留空壳函数
const handleSearch = () => {
  console.log('搜索', filterForm)
}

const handleReset = () => {
  // 清空表单
}
```

## 第三阶段：生成代码

### 代码生成规则

根据用户选择的技术栈，应用对应的代码模板：

#### React + Ant Design + CSS Modules + TypeScript

```tsx
// ComponentName.tsx
import React, { FC } from 'react';
import { Button, Input, Select, Card, Switch } from 'antd';
import styles from './ComponentName.module.css';

interface ComponentNameProps {
  // props 定义
}

const ComponentName: FC<ComponentNameProps> = () => {
  return (
    <div className={styles.container}>
      {/* DSL 转换的 JSX */}
    </div>
  );
};

export default ComponentName;
```

```css
/* ComponentName.module.css */
.container {
  /* 从 DSL styles 提取 */
}
```

#### Vue 3 + Element Plus + SCSS

```vue
<template>
  <div class="container">
    <!-- DSL 转换的模板 -->
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { ElButton, ElInput, ElSelect, ElCard, ElSwitch } from 'element-plus';

// 组件逻辑
</script>

<style lang="scss" scoped>
.container {
  /* 从 DSL styles 提取 */
}
</style>
```

#### 原生 HTML + Tailwind CSS

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body>
  <div class="w-full p-4 bg-white">
    <!-- DSL 转换的 HTML -->
  </div>
</body>
</html>
```

### DSL 到组件的映射

| DSL 类型 | Ant Design | Element Plus | 原生 |
|---------|------------|--------------|------|
| `type: "FRAME"` + 按钮样式 | `<Button>` | `<el-button>` | `<button>` |
| `type: "FRAME"` + 输入框样式 | `<Input>` | `<el-input>` | `<input>` |
| `type: "FRAME"` + 下拉样式 | `<Select>` | `<el-select>` | `<select>` |
| `type: "FRAME"` + 开关样式 | `<Switch>` | `<el-switch>` | 自定义 toggle |
| `type: "FRAME"` + 卡片阴影 | `<Card>` | `<el-card>` | `<div class="card">` |
| `type: "TEXT"` | 文本节点 | 文本节点 | `<span>` / `<p>` |

### 样式 Token 提取

从 DSL 的 `styles` 对象提取设计 Token：

```javascript
// DSL styles 示例
{
  "paint_1:598": { "value": ["#0073FF"] },  // 主色
  "paint_1:39": { "value": ["#3D3D3D"] },   // 文字色
  "effect_1:1483": { "value": ["box-shadow: 2px 0px 8px 4px rgba(2, 45, 98, 0.06);"] }
}

// 转换为 CSS 变量
:root {
  --color-primary: #0073FF;
  --color-text: #3D3D3D;
  --shadow-card: 2px 0px 8px 4px rgba(2, 45, 98, 0.06);
}
```

## 第四阶段：确认与迭代

生成代码后，询问用户：

```
代码已生成，请检查是否符合预期。

如果需要调整，可以告诉我：
- 某个组件需要换成其他组件
- 样式需要调整
- 需要添加交互逻辑
- 其他修改需求
```

## 特殊场景处理

### 用户没有明确技术栈

如果用户说"随便"或"你决定"：
- 检查项目中是否有 `package.json`，推断技术栈
- 如果是新项目，推荐：React + Ant Design + CSS Modules

### 用户要求批量生成

如果用户提供多个设计链接：
- 逐个处理，每个生成独立组件
- 保持技术栈一致

### 用户要求生成完整项目

如果用户要求生成完整项目结构：
- 使用 `mcp__getMeta` 获取站点信息
- 生成路由配置
- 生成页面组件
- 生成公共组件

## 注意事项

1. **必须先问再生成** - 不要假设用户的技术栈
2. **保持代码简洁** - 不要过度封装
3. **注释清晰** - 标注哪些是从设计稿提取的
4. **Token 变量化** - 颜色、字体等提取为变量
5. **组件库优先** - 能用组件库的不要自己写
