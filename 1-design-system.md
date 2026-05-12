
# 1 - 设计系统（Design System）

> 所有页面都遵循这一份规范。技术实现时不要修改这些值。

---

## 🎨 颜色

### 基础色

| 用途 | HEX | Tailwind 类名 |
|-----|-----|--------------|
| 页面背景 | `#FAFAFA` | `bg-[#FAFAFA]` |
| 卡片背景 | `#FFFFFF` | `bg-white` |
| 主文字 | `#1A1A1A` | `text-[#1A1A1A]` |
| 次要文字 | `#666666` | `text-[#666666]` |
| 三级文字（弱） | `#999999` | `text-[#999999]` |
| 边框/分割线 | `#E5E5E5` | `border-[#E5E5E5]` |

### 按钮色

| 用途 | HEX | 说明 |
|-----|-----|------|
| 主按钮背景 | `#1A1A1A` | 黑色按钮 |
| 主按钮悬浮 | `#000000` | 纯黑 |
| 主按钮文字 | `#FFFFFF` | 白字 |
| 次按钮背景 | `#FFFFFF` | 白底 |
| 次按钮边框 | `#E5E5E5` | 浅灰边 |
| 次按钮文字 | `#1A1A1A` | 黑字 |

### 重要约束

```
❌ 不要使用任何彩色（紫色、绿色、蓝色等）
❌ 不要使用渐变
❌ 不要使用纯黑 #000 做文字
✅ 文字用 #1A1A1A
✅ 强调用纯黑 #000（仅按钮悬浮）
✅ 只用黑白灰，保持高级感
```

---

## 📝 字体

### 字体族

```css
font-family: -apple-system, BlinkMacSystemFont, 
  "PingFang SC", "Hiragino Sans GB", 
  "Microsoft YaHei", sans-serif;
```

### 字号

| 级别 | 桌面端 | 移动端 | Tailwind | 用途 |
|------|--------|--------|----------|------|
| Hero | 72px | 48px | `text-7xl` / `text-5xl` | 首页主标题 |
| H1 | 48px | 32px | `text-5xl` / `text-3xl` | 大标题 |
| H2 | 32px | 24px | `text-3xl` / `text-2xl` | 区块标题 |
| H3 | 24px | 20px | `text-2xl` / `text-xl` | 小标题 |
| 正文大 | 18px | 16px | `text-lg` / `text-base` | 重要正文 |
| 正文 | 16px | 16px | `text-base` | 普通正文 |
| 小字 | 14px | 14px | `text-sm` | 辅助说明 |
| 极小 | 12px | 12px | `text-xs` | 元数据、标签 |

### 字重

```
font-normal (400)    → 正文
font-medium (500)    → 强调
font-semibold (600)  → 小标题
font-bold (700)      → 大标题
```

### 大标题特殊处理

```css
/* 主标题需要紧凑感 */
letter-spacing: -0.02em;
line-height: 1.1;
```

---

## 🔘 按钮

### 主按钮（CTA）

```tsx
<button className="
  bg-[#1A1A1A] hover:bg-black 
  text-white 
  rounded-full 
  px-6 py-3 
  font-medium 
  text-base
  transition-colors duration-200
">
  立即免费体验
</button>
```

### 次按钮

```tsx
<button className="
  bg-white hover:bg-[#FAFAFA] 
  border border-[#E5E5E5]
  text-[#1A1A1A]
  rounded-full 
  px-6 py-3 
  font-medium 
  text-base
  transition-colors duration-200
">
  登录
</button>
```

### 小按钮（用于消息下方动作）

```tsx
<button className="
  text-xs 
  text-[#666666] hover:text-[#1A1A1A]
  transition-colors duration-200
">
  🔄 换个模型重答
</button>
```

---

## 📦 卡片

### 标准卡片

```tsx
<div className="
  bg-white 
  border border-[#E5E5E5] 
  rounded-2xl 
  p-8
  hover:border-[#D5D5D5]
  transition-all duration-200
">
  内容
</div>
```

---

## 💬 消息气泡

### 用户消息（右侧）

```tsx
<div className="flex justify-end">
  <div className="
    bg-[#1A1A1A] 
    text-white 
    rounded-2xl rounded-tr-md
    p-4
    max-w-[75%]
    text-[15px]
  ">
    用户的消息内容
  </div>
</div>
```

### AI 消息（左侧）

```tsx
<div className="flex justify-start flex-col items-start">
  {/* 模型标识 */}
  <p className="text-xs text-[#999999] mb-2 ml-1">
    由 GPT-5 回答
  </p>
  
  {/* 消息气泡 */}
  <div className="
    bg-white 
    border border-[#E5E5E5]
    rounded-2xl rounded-tl-md
    p-4
    max-w-[75%]
    text-[15px]
    text-[#1A1A1A]
  ">
    AI 的回答内容
  </div>
  
  {/* 操作按钮 */}
  <div className="flex gap-4 mt-3 ml-1">
    <button className="text-xs text-[#666666] hover:text-[#1A1A1A]">
      🔄 换个模型重答
    </button>
    <button className="text-xs text-[#666666] hover:text-[#1A1A1A]">
      📋 复制
    </button>
    <button className="text-xs">👍</button>
    <button className="text-xs">👎</button>
  </div>
</div>
```

---

## 📐 圆角规范

| 用途 | Tailwind | 像素 |
|------|----------|------|
| 按钮 | `rounded-full` | 999px |
| 输入框 | `rounded-2xl` | 16px |
| 卡片 | `rounded-2xl` | 16px |
| 消息气泡 | `rounded-2xl` 加 `rounded-tr-md`/`rounded-tl-md` | 16px + 角 |
| 头像 | `rounded-full` | 完全圆形 |

---

## 📏 间距规范

### 区块间距

| 用途 | Tailwind | 说明 |
|------|----------|------|
| 区块之间（大） | `py-32` | 首页各区块 |
| 区块之间（中） | `py-20` | 移动端 / 内部区块 |
| 元素之间 | `space-y-6` 或 `space-y-8` | 消息列表 |
| 紧凑间距 | `space-y-4` | 表单 |

### 内边距

| 用途 | Tailwind |
|------|----------|
| 卡片内 | `p-8` (桌面) / `p-6` (移动) |
| 按钮内 | `px-6 py-3` (大) / `px-5 py-2` (小) |
| 页面边 | `px-6` (桌面) / `px-4` (移动) |

---

## 🖥️ 响应式断点

```javascript
// Tailwind 默认断点
sm: 640px   // 大手机 / 小平板
md: 768px   // 平板
lg: 1024px  // 桌面
xl: 1280px  // 大屏
```

### 内容最大宽度

```
首页内容：max-w-[720px] mx-auto
聊天页内容：max-w-[800px] mx-auto
顶栏内容：max-w-[1200px] mx-auto
```

---

## ⚡ 动画

### 标准过渡

```css
transition-colors duration-200
transition-all duration-200
```

### 不要使用的

```
❌ 复杂的入场动画
❌ 视差滚动
❌ 弹簧动画
❌ 旋转、缩放过度
```

### 可以使用的

```
✅ 颜色过渡（按钮悬浮）
✅ 边框过渡（卡片悬浮）
✅ 透明度过渡（消息显示）
```

---

## 📱 移动端注意事项

```
✅ 输入框 font-size 至少 16px（防 iOS 自动缩放）
✅ 按钮高度至少 44px（触摸友好）
✅ 触摸区域足够大（不要紧贴）
✅ 横向滚动避免（除非必要）
✅ 底部固定的输入框要考虑键盘弹出
```

---

```

---

## ✅ 总结

```
风格 = x.ai 极简骨架 + 暖白背景
配色 = 黑白灰
强调 = 用排版和留白，不用颜色
体验 = 安静、专业、不打扰

记住一句话：
"最好的设计是看不见的设计"
```
