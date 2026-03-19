# Supabase Todo Demo 搭建指南

## 第一步：创建 Supabase 项目

1. 打开 https://supabase.com
2. 用 GitHub 账号登录
3. 点击 **"New Project"**
4. 填写：
   - Organization: 选择你的 GitHub 账号
   - Name: `todo-demo`
   - Database Password: 设置一个密码
   - Region: 选择 `Northeast Asia (Tokyo)` 
5. 点击 **"Create new project"**

等待 1-2 分钟创建完成...

---

## 第二步：创建数据库表

1. 项目创建完成后，点击左侧 **"SQL Editor"**
2. 复制以下 SQL 并运行：

```sql
-- 创建 todos 表
CREATE TABLE todos (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  title TEXT NOT NULL,
  is_completed BOOLEAN DEFAULT false,
  user_id UUID REFERENCES auth.users(id) DEFAULT auth.uid(),
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- 开启 RLS (行级安全)
ALTER TABLE todos ENABLE ROW LEVEL SECURITY;

-- 创建策略：用户只能看自己的 todo
CREATE POLICY "Users can see own todos" ON todos
  FOR SELECT USING (auth.uid() = user_id);

CREATE POLICY "Users can insert own todos" ON todos
  FOR INSERT WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can update own todos" ON todos
  FOR UPDATE USING (auth.uid() = user_id);

CREATE POLICY "Users can delete own todos" ON todos
  FOR DELETE USING (auth.uid() = user_id);
```

3. 点击 **"Run"** 执行

---

## 第三步：获取 API 密钥

1. 点击左侧 **"Project Settings"** (齿轮图标)
2. 点击 **"API"**
3. 找到：
   - **Project URL**: 类似于 `https://xxxxx.supabase.co`
   - **anon public** 密钥: 类似于 `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...`

---

## 第四步：配置代码

打开 `index.html`，找到这两行：

```javascript
const SUPABASE_URL = 'https://your-project.supabase.co';
const SUPABASE_KEY = 'your-anon-key';
```

替换为你刚才获取的值。

---

## 第五步：运行！

双击 `index.html` 在浏览器打开，或者部署到 GitHub Pages / Vercel。

---

## 功能演示

| 功能 | 说明 |
|------|------|
| 📝 注册/登录 | 邮箱 + 密码，自动验证 |
| ➕ 添加任务 | 输入内容回车或点击添加 |
| ✅ 完成/未完成 | 点击复选框切换 |
| 🗑️ 删除 | 点击删除按钮 |
| 💾 数据持久化 | 刷新页面数据还在 |
| 🔒 隐私安全 | 只能看到自己的任务 |

---

## 部署到公网

和之前的游戏一样，可以部署到：
- **Vercel**: 拖入文件夹即可
- **GitHub Pages**: 推送后自动部署

需要帮忙部署吗？
