# 9 - 给技术团队的交接说明

> 这是 UI 设计的"心法"。代码只是"形"，理解原则更重要。

---

## 🎯 一句话原则

**UI 部分照搬，业务逻辑你们写。**

```
你们要做：
✅ 把 v0 生成的 React 代码集成到项目里
✅ 接入 NewAPI 实现真实的 AI 调用
✅ 接入支付通道实现真实的充值
✅ 实现用户认证、状态管理
✅ 实现路由跳转

你们不要做：
❌ 重新设计 UI
❌ 改配色
❌ 加额外的功能（V1 之外的）
❌ 优化得太"花哨"
```

---

## 📋 V1 的核心页面（按用户路径排序）

### 1. 落地页 (2-landing-page.tsx)

**功能要点：**
```
- 顶部：Logo、登录、注册按钮
- 中部：大标题 + 输入框 + CTA 按钮
- 用户在输入框输入问题
- 点击"发送"或"立即免费体验"
- → 跳转到聊天页（带上用户的问题）
```

**关键逻辑：**
```typescript
// 用户输入问题后跳转到聊天页
const handleStart = (question: string) => {
  // 把问题存到 sessionStorage 或 URL 参数
  sessionStorage.setItem('first_question', question)
  router.push('/chat')
}
```

---

### 2. 聊天页（未注册）(3-chat-page-unregistered.tsx)

**功能要点：**
```
- 顶部显示"剩余 N 次免费体验"
- 用户进入后，自动用 GPT-5 回答第一个问题（从首页带过来的）
- 用户可以继续聊
- 每条 AI 回答下有"换个模型重答"按钮
- 用满 3 次后，弹出注册弹窗
```

**关键逻辑：**
```typescript
// 状态管理
const [messages, setMessages] = useState<Message[]>([])
const [freeCount, setFreeCount] = useState(3)
const [showRegisterModal, setShowRegisterModal] = useState(false)

// 用户发送消息
const handleSend = async (text: string) => {
  if (freeCount === 0) {
    setShowRegisterModal(true)
    return
  }
  
  // 调用 NewAPI
  const response = await api.chat({
    model: 'gpt-5',
    message: text
  })
  
  setMessages([...messages, 
    { role: 'user', content: text },
    { role: 'ai', content: response.text, model: 'gpt-5' }
  ])
  
  setFreeCount(freeCount - 1)
  
  if (freeCount - 1 === 0) {
    // 最后一次回答后，2秒弹出注册
    setTimeout(() => setShowRegisterModal(true), 2000)
  }
}
```

---

### 3. 聊天页（已注册）(4-chat-page-registered.tsx)

**功能要点：**
```
- 顶部显示"还能用 N 次"（用次数，不用金额）
- 顶部小字显示"当前 GPT-5"
- 右上角"充值"按钮 + 用户头像
- 完整的聊天功能
- 不再有"剩余次数"限制
```

**关键逻辑：**
```typescript
// 余额转换为"剩余次数"
const balance = user.balance // ¥45.23
const estimatedCount = Math.floor(balance / 0.02) // 假设每次约 ¥0.02
// 显示为 "还能用 2200+ 次"

// 模型切换
const handleSwitchModel = async (messageId: string, newModel: string) => {
  // 用新模型重新回答该消息
  const userMessage = findUserMessageBefore(messageId)
  const response = await api.chat({
    model: newModel,
    message: userMessage.content
  })
  // 替换该 AI 消息
  updateMessage(messageId, response.text, newModel)
}
```

---

### 4. 注册弹窗 (5-register-modal.tsx)

**功能要点：**
```
- 在用户聊到第 3 次后触发
- 标题："再聊一次需要注册"
- 副标题："登录后保存对话，再送 ¥3 体验额度"
- 两个按钮：Google 注册 / 邮箱注册
- 不强制，可以关闭
```

**关键逻辑：**
```typescript
const handleRegister = async (method: 'google' | 'email') => {
  if (method === 'google') {
    // OAuth 跳转
    window.location.href = '/auth/google'
  } else {
    // 跳到邮箱注册页（或者弹另一个表单）
    router.push('/register')
  }
}

// 注册成功后
// 1. 把未注册时的对话保存到该用户
// 2. 给账户加 ¥3
// 3. 关闭弹窗，继续聊天
```

---

### 5. 充值页 (6-recharge-page.tsx)

**功能要点：**
```
- 显示当前余额
- 4 个金额档：¥30 / ¥100 / ¥300 / ¥500
- 两种支付方式：支付宝 / 微信
- 点击"立即支付"跳转到聚合支付
```

**关键逻辑：**
```typescript
const handlePay = async (amount: number, method: 'alipay' | 'wechat') => {
  // 创建订单
  const order = await api.createOrder({
    amount,
    method
  })
  
  // 跳转到聚合支付（YunGouOS / PayBeaver）
  window.location.href = order.payUrl
  
  // 用户支付完成后，聚合支付会回调 webhook
  // 后端收到 webhook 后给用户加余额
}
```

---

## 🔌 接入 NewAPI

```
NewAPI 应该已经部署好了，提供以下接口：

POST /v1/chat/completions
{
  "model": "gpt-5",
  "messages": [...]
}

Response:
{
  "choices": [{ "message": { "content": "..." } }],
  "usage": { "total_tokens": 123 }
}

我们的 Backend 要做：
1. 转发请求到 NewAPI
2. 根据 tokens 计算费用
3. 扣除用户余额
4. 记录使用日志
```

---

## 📦 状态管理建议

```typescript
// 用 Zustand 或 Context
interface UserState {
  user: User | null
  balance: number
  freeCount: number  // 未注册时的剩余次数
}

interface ChatState {
  messages: Message[]
  currentModel: string
  isLoading: boolean
}
```

---

## 🚦 优先级排序

```
P0 必做（V1 上线）：
✅ 落地页
✅ 聊天页（核心）
✅ 注册（最简单的邮箱注册）
✅ 充值页

P1 重要（V1 上线后立刻补）：
- 登录页
- 个人中心（简单版）
- 错误处理
- 加载状态

P2 之后做（V1.5）：
- 流式输出（逐字显示）
- 对话历史保存
- 邮箱验证
- 找回密码

V2 才做：
- 智能路由（自动选模型）
- 模型对比
- 提示词模板
```

---

## ⚠️ 常见问题

### Q1：v0 生成的代码用了 shadcn/ui，要不要保留？

```
A：保留就好。
shadcn/ui 是基于 Radix UI，质量很高。
不要为了"自己写"而重写。
```

### Q2：v0 用了 Next.js，我们项目是普通 React 怎么办？

```
A：可以转换。
- v0 的 Next.js 代码可以剥离成纯 React
- 把 Next.js 特有的（如 'use client'）去掉
- 把 next/link 换成 react-router-dom 的 Link
- 把 next/image 换成普通 img
```

### Q3：要不要做暗色模式？

```
A：V1 不做。
- 用户来用 AI，不需要暗色
- 加暗色 = 工作量翻倍
- V2 再考虑
```

### Q4：要不要做无障碍（a11y）？

```
A：基础 a11y 做好，但不深度优化。
- semantic HTML（用 <button> 不用 <div onClick>）
- 关键元素加 aria-label
- 颜色对比度 OK（我们的设计已经够）
- 不需要 screen reader 完美支持
```

---

## 🚀 上线 Checklist

技术团队上线前确认：

```
☐ 所有页面响应式 OK（手机、平板、桌面）
☐ 接入 NewAPI 成功，能真实对话
☐ 接入聚合支付成功，能真实充值
☐ 用户注册/登录流程通畅
☐ 余额扣费准确
☐ 错误处理友好（不会暴露技术错误给用户）
☐ 加载状态明确（不会卡住没反馈）
☐ HTTPS 配置好
☐ 域名解析 OK
☐ 数据库备份策略
☐ 监控告警（错误率、响应时间）
```

---

## 💬 联系方式

```
Anita (PM)
- 飞书：[填写]
- 邮箱：[填写]

UI 有任何修改需求：
1. 在飞书评论里说
2. 或者去 v0 链接（见 8-v0-links.md）直接看实时效果

不要：
❌ 私下改 UI 后才告诉 PM
❌ "感觉这样更好"就动手改

要：
✅ 改之前先讨论
✅ 提出问题，提出建议
✅ 一起做决策
```

---

## 🙏 最后的话

```
这是 V1，30 天上线。
不追求完美，追求"能用 + 能感受"。
上线后看用户反馈，再迭代。

UI 部分照我的来，
功能部分你们专业，我相信你们。

有问题随时找我。

—— Anita
```
