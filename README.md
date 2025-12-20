# Icon Manager（图标管理器）

一个基于 [Astro](https://astro.build) 的静态图标管理站点：把图标文件放到 `public/icon/`，即可得到一个可搜索的图标浏览页，以及一个可跨域访问的图标元数据接口 `/icons.json`。项目默认按静态站点构建（SSG），非常适合部署到阿里云 ESA 等边缘/静态托管平台。

## ✨ 功能

- **静态构建**：构建产物为纯静态资源，可直接丢到任意静态托管/CDN。
- **图标浏览**：网格展示、原生懒加载、单个图标可直接下载。
- **快速搜索**：按英文名、中文名、域名/备注、原始文件名实时过滤。
- **复制直链**：一键复制带域名的图标 URL（页面内自动补全 `window.location.origin`）。
- **开放 API**：自动生成 `/icons.json`（包含 `name/cnName/domain/url` 等字段），并启用 CORS。

## ✅ 环境要求

- Node.js（建议 `>= 18`）
- npm

## 🚀 快速开始

### 本地开发

```bash
npm install
npm run dev
```

- 访问页面：`http://localhost:4321/`
- 访问接口：`http://localhost:4321/icons.json`

### 构建与预览

```bash
npm run build
npm run preview
```

- 构建产物：`dist/`

## 📦 放置图标（最重要）

- 将图标文件放到：`public/icon/`
- 支持的扩展名：`.png`、`.jpg`、`.jpeg`、`.svg`、`.webp`、`.gif`
- 新增/删除/替换图标后：
  - 开发模式：刷新页面即可看到变化
  - 静态部署：需要重新执行 `npm run build` 并重新部署 `dist/`

## 🧩 文件命名规范

系统按文件名解析元数据，分隔符为双连字符 `--`：

```text
英文名--中文名--域名或备注.扩展名
```

解析规则：

- `英文名`：必填；页面与 API 中的 `name` 来自它
- `中文名`：选填；对应 `cnName`
- `域名或备注`：选填；对应 `domain`
- 仅解析前三段，多余段会被忽略
- `英文名`中的下划线 `_` 会在展示与 API 中替换成空格（例如 `Apple_Music` 会显示为 `Apple Music`）

示例：

- `Bilibili--哔哩哔哩--bilibili.com.svg`
- `Aliyun--阿里云--aliyun.com.png`
- `GitHub--github.com.svg`（省略中文名时，第二段会被当作中文名；推荐始终写满三段）

命名建议（避免 URL 编码问题）：

- 尽量使用 ASCII（英文/数字/`-`/`_`/`.`）
- 避免 `+`、空格、`#`、`?`、`%` 等特殊字符
- 中文名建议仅出现在第二段（`中文名`），不要放在文件名其它位置

## 🔌 API：`/icons.json`

返回所有图标元数据数组，且默认允许跨域访问（`Access-Control-Allow-Origin: *`）。

字段说明：

- `name`：英文名（`_` 会被替换为空格）
- `cnName`：中文名（可能为空字符串）
- `domain`：域名或备注（可能为空字符串）
- `filename`：原始文件名（含扩展名）
- `url`：图标相对路径（`/icon/<filename>`）
- `downloadUrl`：下载相对路径（与 `url` 相同）

响应示例：

```json
[
  {
    "name": "Bilibili",
    "cnName": "哔哩哔哩",
    "domain": "bilibili.com",
    "filename": "Bilibili--哔哩哔哩--bilibili.com.svg",
    "url": "/icon/Bilibili--哔哩哔哩--bilibili.com.svg",
    "downloadUrl": "/icon/Bilibili--哔哩哔哩--bilibili.com.svg"
  }
]
```

外部项目调用示例：

```js
const res = await fetch('https://your-domain.com/icons.json');
const icons = await res.json();
console.log(icons[0].url); // "/icon/xxx.svg"
```

## 🧱 项目结构

```text
.
├── public/
│   └── icon/              # 图标静态资源目录（直接对外提供 /icon/*）
├── src/
│   └── pages/
│       ├── index.astro    # 浏览/搜索 UI
│       └── icons.json.ts  # 扫描 public/icon 并生成 JSON
├── astro.config.mjs       # Astro 配置（output: static）
├── esa.jsonc              # 阿里云 ESA 构建/发布配置
└── package.json
```

## ☁️ 部署

### 任意静态托管

1. 本地执行 `npm run build`
2. 上传 `dist/` 到你的静态托管（Nginx、OSS、GitHub Pages、Cloudflare Pages 等）

### 阿里云 ESA

项目已提供 `esa.jsonc`（包含 `installCommand/buildCommand` 与静态资源目录 `./dist`）。按 ESA 的部署方式将 `dist/` 发布到边缘即可。

## 🛠 常见问题

- 页面能打开但没有图标：确认 `public/icon/` 目录存在且包含支持的图片格式文件。
- 新增图标后线上不生效：静态站点需要重新 `npm run build` 并重新部署 `dist/`。
- 链接包含中文/特殊字符打不开：建议按“命名建议”避免特殊字符；浏览页会对链接做 `encodeURIComponent`，但 JSON 接口返回的是原始路径字符串。

## 📄 License

MIT
