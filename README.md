# 学校数字简介系统

纯前端、零依赖的学校简介展示系统，手机扫码即可访问，自动适配手机屏幕。

---

## 文件结构

```
school-intro-dist/
├── index.html       # 主页面（修改此文件即可更新内容）
├── qrcode.html     # 二维码展示页（手机扫码用）
├── server.cjs       # 本地 HTTP 服务器（Node.js）
├── assets/          # 图片资源目录（当前未使用）
├── README.md        # 使用说明
└── WORK_LOG.md     # 开发记录（续接工作必读）
```

---

## 快速开始（新电脑首用）

### 第一步：安装 Node.js

下载地址：https://nodejs.org/zh-cn/ （选 LTS 版）

安装后在命令行验证：
```bash
node -v
```

### 第二步：启动服务器

```bash
cd school-intro-dist
node server.cjs
```

成功后会显示：
```
Server running at http://0.0.0.0:8000
Local:   http://localhost:8000
```

### 第三步：手机扫码访问

查看电脑局域网IP：
```bash
ipconfig | findstr IPv4
```
手机连接同一Wi-Fi后，访问 `http://[查到的IP]:8000`

打开 `http://[查到的IP]:8000/qrcode.html` 显示二维码，直接用手机扫码。

---

## 如何修改内容

用文本编辑器打开 `index.html`，找到 `schoolData` 对象（约第92行），替换以下内容：

```js
const schoolData = {
  schoolName:    "阳光中学",        // 校名
  heroTitle:    "欢迎来到阳光中学",  // 主标题
  heroSubtitle: "立德树人 · ...",   // 副标题/理念
  stats: [ ... ],                  // 4个统计数字
  teachers: [ ... ],               // 教师列表
  gallery: [ ... ],                // 校园风采
  features: [ ... ],               // 办学特色
  contact: { ... }                  // 地址、电话、邮箱、网址
}
```

修改后保存，刷新浏览器即可。

---

## 如何替换为真实图片（可选）

当前校园风采用 emoji 占位，换成实景照片：

1. 将图片放入 `assets/` 目录（如 `school-gate.jpg`）
2. 修改 `gallery` 数据，将 `icon` 改为 `img`：
   ```js
   { id: 1, img: "assets/school-gate.jpg", title: "学校正门", desc: "..." }
   ```
3. 修改 `Gallery()` 组件（找到约第229行），将 `{g.icon}` 改为：
   ```jsx
   <img src={g.img} style={{width:'100%',height:'140px',objectFit:'cover'}} />
   ```

---

## 部署到公网

| 方式 | 难度 | 说明 |
|------|------|------|
| GitHub Pages | 简单 | 将文件上传到 GitHub 仓库，启用 Pages 即可，有免费域名 |
| Vercel / Netlify | 简单 | 直接拖拽上传，自动生成 HTTPS 访问地址 |
| 阿里云 OSS | 中等 | 上传文件，设置静态网站托管，绑定域名 |

---

## 技术说明

| 项目 | 说明 |
|------|------|
| 前端框架 | React 18（CDN引入，无需npm install） |
| 编译 | Babel Standalone（浏览器内编译JSX） |
| 样式 | 纯CSS内嵌 |
| 图标 | Font Awesome 6（CDN） |
| 二维码 | qrserver.com在线API |
| 运行时依赖 | Node.js v16+（仅用于运行server.cjs）|

---

## 环境要求

- **浏览器**：Chrome / Edge / Firefox / Safari 近3年版本
- **本地开发**：Node.js v16+
- **手机访问**：与电脑同一局域网

---

## ⚠️ 已知问题（必读）

**问题1：viewport 提示**

如浏览器控制台提示 viewport 相关问题，将 `index.html` 第4行改为：
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
```
（删除 `maximum-scale` 和 `user-scalable=no`，更宽松且功能完全相同）

**问题2：contact.qrcode 字段未使用**

`index.html` 第139行 `contact` 对象中有一行未使用的 `qrcode` 字段，可直接删除，不影响功能。

---

*生成时间：2026-05-16*
