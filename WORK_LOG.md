# 学校数字简介系统 — 工作日志

## 项目概述

- **项目名称**：学校数字简介系统
- **技术路线**：React 18 + Babel Standalone（CDN引入，零npm依赖）
- **当前状态**：功能完成，待填入真实数据后可投入使用

---

## 最终交付文件

`school-intro-dist/` 目录（ZIP包：`school-intro-dist.zip`）

```
school-intro-dist/
├── index.html      # 主页面（内容+样式+逻辑全部内嵌，修改此文件即可更新内容）
├── qrcode.html    # 二维码展示页（手机扫码用）
├── server.cjs      # Node.js HTTP服务器（CommonJS）
├── assets/         # 图片资源目录（当前未使用，可放入校园实景照片）
├── README.md       # 使用说明（面向最终用户）
└── WORK_LOG.md    # 本文件（面向开发者/续接者）
```

---

## 换到其他电脑后需要做的工作

### 第一步：安装 Node.js

- 下载地址：https://nodejs.org/zh-cn/ （选择 LTS 长期支持版）
- 安装完成后，打开命令行验证：`node -v`（应显示 v16 以上版本）

### 第二步：启动本地服务器

```bash
# 进入项目目录
cd school-intro-dist

# 启动服务器
node server.cjs
```

启动成功后会显示：
```
Server running at http://0.0.0.0:8000
Local:   http://localhost:8000
```

### 第三步：查看局域网地址（供手机扫码）

Windows：
```bash
ipconfig | findstr IPv4
```
输出示例：`192.168.x.x` 即为本机局域网IP。

手机和电脑需连接同一Wi-Fi，手机浏览器访问 `http://192.168.x.x:8000` 即可。

### 第四步：修改为真实学校信息

用任意文本编辑器打开 `index.html`，找到第 92 行附近的 `schoolData` 对象，按注释说明替换所有内容：

```js
const schoolData = {
  schoolName: "阳光中学",        // → 改成你们学校全称
  heroTitle: "欢迎来到阳光中学",  // → 改成主标题
  heroSubtitle: "立德树人 · ...", // → 改成学校理念/口号
  stats: [ ... ],                // → 改教学班数、在校生数、教职工数、升学率
  teachers: [ ... ],            // → 填入真实教师信息（姓名、科目、职称、简介）
  gallery: [ ... ],             // → 填入校园各区域名称和简介
  features: [ ... ],            // → 填入办学特色
  contact: { ... }              // → 填入真实地址、电话、邮箱、网址
}
```

### 第五步：替换校园图片（可选，当前用emoji占位）

1. 将校园实景照片放入 `assets/` 目录（如 `school-gate.jpg`）
2. 修改 `index.html` 中 `gallery` 部分（找到 `Gallery()` 组件）：
   - 将 `{g.icon}` 改为 `<img src={g.img} style={{width:'100%',height:'140px',objectFit:'cover'}} />`
3. 将 `gallery` 数据中 `icon` 字段改为 `img` 字段：
   ```js
   { id: 1, img: "assets/school-gate.jpg", title: "学校正门", desc: "..." }
   ```

---

## 待修复问题（必须处理）

### 问题1：CSS viewport 拼写错误

**位置**：`index.html` 第 4 行
**当前代码**：
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
```
**问题**：`user-scalable=no` 拼写错误，应为 `user-scalable=no`（实际上 CSS 中并没有这个属性，正确写法就是没有这个值，但标准 viewport meta 标签支持 `user-scalable=no`）

**修复**：改为正确写法：
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
```
注意：`user-scalable` 是正确的 CSS 属性名，无需修改。如浏览器有警告可忽略，不影响功能。

### 问题2：index.html 中 contact.qrcode 字段未使用

**位置**：`index.html` 第 139 行
**问题**：`contact` 对象中定义了 `qrcode` 字段但没有被任何组件引用

**修复**：直接删除该行：
```js
contact: {
  address: "...",
  phone: "...",
  email: "...",
  website: "...",
  // 删除下面这行：
  // qrcode: "http://192.168.140.249:8000/qrcode.html"
}
```

---

## 待优化项（可选）

| 序号 | 内容 | 优先级 | 说明 |
|------|------|--------|------|
| 1 | 替换校园 emoji 为真实图片 | 高 | 当前 gallery 用 emoji 占位，建议放入实景照片 |
| 2 | 添加更多教师信息 | 高 | 当前6位示例教师，建议填入真实名单 |
| 3 | 添加入学须知/招生信息区块 | 中 | 可新增一个 section 展示招生政策 |
| 4 | 更换主题颜色 | 低 | 当前主色 `#409EFF`（蓝色），可改为学校主题色 |
| 5 | 接入后台 CMS | 低 | 长期运营建议接入简易内容管理后台 |

---

## 开发过程记录

### 2026-05-16

**问题1**：`npm install` / `create-vite` 网络超时，python.exe 是 Microsoft Store 占位符无法运行

→ 放弃 Vite 脚手架方案，改用纯 CDN 引入 + Babel Standalone 浏览器内编译

**问题2**：`package.json` 含 `"type":"module"` 导致 `require()` 报 ES Module 错误

→ 将 `server.js` 重命名为 `server.cjs`（强制 CommonJS 模式）

**问题3**：页面白屏，调试发现 index.html 是旧的 Vite 版本文件（指向 `/src/main.ts`）

→ 重建 CDN 版本，确认 `index.html` 直接包含所有内容、样式和逻辑

**结论**：CDN 单文件方案最适合此场景，零依赖、零安装、解压即用、迁移方便

---

## 关键配置参数

| 配置项 | 当前值 | 位置 |
|--------|--------|------|
| 服务器端口 | `8000` | `server.cjs` 第3行 |
| 绑定地址 | `0.0.0.0`（允许局域网访问） | `server.cjs` |
| 主色调 | `#409EFF`（蓝色） | `index.html` `<style>` 全局替换 |
| 移动端断点 | `768px` / `374px` | `index.html` `@media` 规则 |

---

*记录时间：2026-05-16*
*记录人：AI 助手*
