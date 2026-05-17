# 学校数字简介系统 - API接口文档

> 版本: v1.0 | 更新日期: 2026-05-16

---

## 一、接口规范

### 1.1 基本信息

| 项目 | 说明 |
|------|------|
| 基础路径 | `/api/v1` |
| 数据格式 | JSON |
| 编码 | UTF-8 |
| 时间格式 | ISO 8601 (e.g., `2026-05-16T10:30:00Z`) |

### 1.2 认证方式

| 接口类型 | 认证方式 | 说明 |
|---------|---------|------|
| 公开接口 | 无需认证 | 获取学校信息、师资列表等 |
| 管理接口 | JWT Bearer Token | 需要登录获取Token |
| 外部接口 | AppKey + AppSecret | 用于第三方系统对接 |

### 1.3 请求头

```http
GET /api/v1/xxx HTTP/1.1
Host: api.school.edu.cn
Content-Type: application/json
Authorization: Bearer <token>           # 管理接口需要
X-App-Key: <app_key>                    # 外部接口需要
X-Timestamp: <timestamp>                # 外部接口需要
X-Signature: <signature>                # 外部接口需要
```

### 1.4 响应格式

**成功响应：**
```json
{
  "code": 200,
  "message": "success",
  "data": { ... },
  "timestamp": "2026-05-16T10:30:00Z"
}
```

**错误响应：**
```json
{
  "code": 400,
  "message": "参数错误",
  "errors": [
    { "field": "name", "message": "名称不能为空" }
  ],
  "timestamp": "2026-05-16T10:30:00Z"
}
```

### 1.5 分页格式

```json
{
  "code": 200,
  "data": {
    "list": [...],
    "pagination": {
      "page": 1,
      "pageSize": 10,
      "total": 100,
      "totalPages": 10
    }
  }
}
```

---

## 二、公开接口

### 2.1 学校信息

#### 获取学校基本信息
```
GET /public/school
```

**响应示例：**
```json
{
  "code": 200,
  "data": {
    "name": "XX中学",
    "shortName": "XX中学",
    "logo": "https://cdn.school.edu.cn/uploads/logo.png",
    "banner": "https://cdn.school.edu.cn/uploads/banner.jpg",
    "establishedYear": 2000,
    "address": "XX省XX市XX区XX路100号",
    "phone": "0571-88888888",
    "email": "info@school.edu.cn",
    "website": "https://www.school.edu.cn"
  }
}
```

#### 获取学校简介
```
GET /public/school/intro
```

#### 获取学校配置
```
GET /public/school/config?keys=school_intro,school_history,school_spirit
```

---

### 2.2 教师接口

#### 获取教师列表
```
GET /public/teachers
```

**Query参数：**
| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| page | number | 否 | 页码，默认1 |
| pageSize | number | 否 | 每页数量，默认20 |
| subject | string | 否 | 按学科筛选 |
| title | string | 否 | 按职称筛选 |
| keyword | string | 否 | 搜索关键词（姓名/简介） |

**响应示例：**
```json
{
  "code": 200,
  "data": {
    "list": [
      {
        "id": 1,
        "name": "张老师",
        "gender": "male",
        "title": "高级教师",
        "subject": "数学",
        "avatar": "https://cdn.school.edu.cn/teachers/avatar/1.jpg",
        "teachingStyle": "幽默风趣，注重思维培养",
        "sortOrder": 1
      }
    ],
    "pagination": {
      "page": 1,
      "pageSize": 20,
      "total": 150,
      "totalPages": 8
    }
  }
}
```

#### 获取教师详情
```
GET /public/teachers/:id
```

---

### 2.3 全景接口

#### 获取全景列表
```
GET /public/panoramas
```

**Query参数：**
| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| category | string | 否 | 分类：campus/building/classroom/lab/sports |

**响应示例：**
```json
{
  "code": 200,
  "data": [
    {
      "id": 1,
      "title": "校门口全景",
      "category": "campus",
      "thumbnail": "https://cdn.school.edu.cn/panoramas/thumb/1.jpg",
      "mapX": 100,
      "mapY": 200
    }
  ]
}
```

#### 获取全景详情
```
GET /public/panoramas/:id
```

**响应示例：**
```json
{
  "code": 200,
  "data": {
    "id": 1,
    "title": "校门口全景",
    "description": "学校正门入口处全景图",
    "category": "campus",
    "sceneUrl": "https://cdn.school.edu.cn/panoramas/scene/1.jpg",
    "thumbnail": "https://cdn.school.edu.cn/panoramas/thumb/1.jpg",
    "audioUrl": "https://cdn.school.edu.cn/panoramas/audio/1.mp3",
    "hotspots": [
      {
        "id": "spot_1",
        "type": "link",
        "pitch": -2,
        "yaw": 45,
        "targetId": 2,
        "label": "前往教学楼"
      },
      {
        "id": "spot_2",
        "type": "info",
        "pitch": -5,
        "yaw": -30,
        "label": "校名石",
        "description": "刻有学校名称的巨石"
      }
    ]
  }
}
```

---

### 2.4 文章接口

#### 获取文章列表
```
GET /public/articles
```

**Query参数：**
| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| page | number | 否 | 页码 |
| pageSize | number | 否 | 每页数量 |
| category | string | 否 | 栏目slug |
| keyword | string | 否 | 搜索关键词 |

#### 获取文章详情
```
GET /public/articles/:slug
```

---

## 三、管理接口（需认证）

### 3.1 认证接口

#### 管理员登录
```
POST /auth/login
```

**请求参数：**
```json
{
  "username": "admin",
  "password": "password123"
}
```

**响应示例：**
```json
{
  "code": 200,
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expiresIn": 604800,
    "user": {
      "id": 1,
      "username": "admin",
      "role": "admin",
      "realName": "系统管理员"
    }
  }
}
```

#### 刷新Token
```
POST /auth/refresh
```

**请求头：** `Authorization: Bearer <refreshToken>`

---

### 3.2 用户管理

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | /admin/users | 获取用户列表 |
| POST | /admin/users | 创建用户 |
| GET | /admin/users/:id | 获取用户详情 |
| PUT | /admin/users/:id | 更新用户 |
| DELETE | /admin/users/:id | 删除用户 |
| PUT | /admin/users/:id/password | 修改密码 |

---

### 3.3 教师管理

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | /admin/teachers | 获取教师列表 |
| POST | /admin/teachers | 创建教师 |
| GET | /admin/teachers/:id | 获取教师详情 |
| PUT | /admin/teachers/:id | 更新教师 |
| DELETE | /admin/teachers/:id | 删除教师 |
| POST | /admin/teachers/import | 批量导入 |
| GET | /admin/teachers/export | 导出 |

---

### 3.4 全景管理

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | /admin/panoramas | 获取全景列表 |
| POST | /admin/panoramas | 创建全景 |
| GET | /admin/panoramas/:id | 获取全景详情 |
| PUT | /admin/panoramas/:id | 更新全景 |
| DELETE | /admin/panoramas/:id | 删除全景 |
| PUT | /admin/panoramas/:id/hotspots | 配置热点 |

---

### 3.5 文章管理

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | /admin/articles | 获取文章列表 |
| POST | /admin/articles | 创建文章 |
| GET | /admin/articles/:id | 获取文章详情 |
| PUT | /admin/articles/:id | 更新文章 |
| DELETE | /admin/articles/:id | 删除文章 |
| PUT | /admin/articles/:id/publish | 发布文章 |
| PUT | /admin/articles/:id/unpublish | 下架文章 |

---

### 3.6 栏目管理

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | /admin/categories | 获取栏目树 |
| POST | /admin/categories | 创建栏目 |
| PUT | /admin/categories/:id | 更新栏目 |
| DELETE | /admin/categories/:id | 删除栏目 |

---

### 3.7 媒体管理

```
POST /admin/media/upload
Content-Type: multipart/form-data

file: <binary>  # 文件
folder: string  # 存放文件夹（可选）
```

**响应示例：**
```json
{
  "code": 200,
  "data": {
    "id": 1,
    "filename": "banner.jpg",
    "url": "https://cdn.school.edu.cn/uploads/banner.jpg",
    "thumbnailUrl": "https://cdn.school.edu.cn/uploads/thumb/banner.jpg",
    "mimeType": "image/jpeg",
    "fileSize": 1024000
  }
}
```

---

### 3.8 系统配置

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | /admin/config | 获取所有配置 |
| PUT | /admin/config | 批量更新配置 |
| GET | /admin/config/:key | 获取单个配置 |
| PUT | /admin/config/:key | 更新单个配置 |

---

## 四、外部对接接口

### 4.1 接口说明

| 项目 | 说明 |
|------|------|
| 认证方式 | AppKey + AppSecret + 签名验证 |
| 签名算法 | HMAC-SHA256(AppSecret + Timestamp + RequestBody) |
| Timestamp | 当前Unix时间戳（秒），有效期5分钟 |
| Rate Limit | 默认100次/分钟 |

### 4.2 获取Token

```
POST /api/external/token
X-App-Key: your_app_key
X-Timestamp: 1715841000
X-Signature: xxxxxxxx

{
  "appSecret": "your_app_secret"
}
```

### 4.3 获取教师数据

```
GET /api/external/teachers
X-App-Key: your_app_key
X-Timestamp: 1715841000
X-Signature: xxxxxxxx

# Query参数
updatedAfter: 2026-01-01T00:00:00Z  # 可选，获取指定时间后的数据
```

### 4.4 同步教师数据

```
POST /api/external/teachers/sync
X-App-Key: your_app_key
X-Timestamp: 1715841000
X-Signature: xxxxxxxx

{
  "teachers": [
    {
      "externalId": "EXT001",
      "name": "张老师",
      "gender": "male",
      "title": "高级教师",
      "subject": "数学",
      "status": "active"
    }
  ]
}
```

### 4.5 推送文章

```
POST /api/external/articles
X-App-Key: your_app_key
X-App-Secret: your_app_secret
X-Timestamp: 1715841000
X-Signature: xxxxxxxx

{
  "title": "校园新闻标题",
  "content": "新闻内容...",
  "category": "news",
  "coverImage": "https://..."
}
```

---

## 五、状态码说明

| 状态码 | 说明 |
|--------|------|
| 200 | 成功 |
| 201 | 创建成功 |
| 400 | 参数错误 |
| 401 | 未授权 |
| 403 | 权限不足 |
| 404 | 资源不存在 |
| 500 | 服务器错误 |

---

*文档版本：v1.0*
