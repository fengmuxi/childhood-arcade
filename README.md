# 童年游戏厅 · Childhood Arcade

```
 ██████ ██   ██ ██ ██      ██████  ██   ██  ██████   ██████  ██████
██      ██   ██ ██ ██      ██   ██ ██   ██ ██    ██ ██    ██ ██   ██
██      ███████ ██ ██      ██   ██ ███████ ██    ██ ██    ██ ██   ██
██      ██   ██ ██ ██      ██   ██ ██   ██ ██    ██ ██    ██ ██   ██
 ██████ ██   ██ ██ ███████ ██████  ██   ██  ██████   ██████  ██████
                ▸  ARCADE · 1985 - 2026 · INSERT COIN  ◂
```

> 自托管的 **8090 怀旧游戏厅** · 14 个主流平台 · 云存档 · 浏览器内对战
> 作者 **YiZe**

[![license: MIT](https://img.shields.io/badge/license-MIT-6366F1.svg)](./LICENSE)
![stack](https://img.shields.io/badge/stack-Vue%203%20·%20Hono%20·%20SQLite-6366F1)
![vibe](https://img.shields.io/badge/vibe-modern%20slate%20·%20indigo-6366F1)

在自家服务器 / NAS 上开一间童年游戏厅——**街机 / FC / SFC / GB / GBA / MD / PS1** 一条龙，浏览器直接开玩，多端云存档，WebRTC 一键开房和朋友对战。底层 libretro，不装客户端。

---

## 🎯 一图理解

| 别家 | 童年游戏厅 |
|---|---|
| 一个模拟器 = 一个网站 | **14 平台统一收藏柜** |
| Cookie 登录、单机 | **账号 + 多端云存档 + 收藏** |
| 联机要装客户端 | **浏览器开房**，一个房间码，游客也能围观 |
| 房主掉线房间就没了 | **房间常驻**，主机刷新不丢 |
| 核心靠海外 CDN | **全部本地化**（`data/cores/` 预置 ~100 MB）|
| 按键 ABXY 一刀切 | **按平台原生按键**（街机 ABCD / MD ABCXYZ / PS1 ◯✕△□）|

---

## ✨ 功能

**游戏库** · 14 平台 / FBNeo + MAME2003+ / 任天堂全家桶 / 世嘉全家桶 / PS1 · ROM 拖拽上传（100 MB，可配）· 公开 / 私密 · 多版本（原版 + 汉化 + hack）· 收藏 · 软删回收站 + 永久删除

**模拟器** · Nostalgist 0.21 · retroarch-emscripten-build v1.22.2 · Rewind + Run-Ahead 默认开 · 屏幕唤醒锁 · 按平台原生按键命名 + 配色（街机 ABCD / MD 六键 / SNES 四色菱形 / PS1 符号）· 键盘 / 手柄 / 虚拟摇杆自定义映射

**移动端 H5** · 8 向虚拟摇杆 · **MOBA 多点触控**（手指滑到哪按哪，王者荣耀手感）· **连发开关**持久化 · `env(safe-area-inset-*)` 全四边安全区 · 禁长按菜单 / 文本选中 · 横屏紧凑布局

**联机对战** · 公开房间大厅 + 房间码 + bcrypt 密码房 · **游客免登陆围观**（收视频流 + 聊天，自动分配「访客-xxxx」）· 主机 `canvas.captureStream` + `AudioContext` → WebRTC · 访客输入反向回传主机派发为 P2 F-key · 房主掉线 / 刷新房间不丢 · admin killswitch

**账号 + 云存档** · 注册 / 登录 / 改密 · DB session（可撤销，记 IP/UA）· `/api/saves` 多端同步，游客 fallback 到 localStorage · 首次 seed 管理员 `admin/admin123`（**务必改**）

**主题** · Modern Slate（温暖 stone + Indigo-500）· 完整语义色板 CSS token · 浅 / 深色一键切（固化到 localStorage）

**管理后台** · 用户 / ROM 跨用户管理 · 站点设置 · BIOS 列表 · netplay 开关

---

## 🚀 部署

### 本地部署

适合二次开发、源码运行或直接在服务器上用 Node.js 托管。

**环境要求**

- Node.js 24+
- npm

```bash
# 1. 安装依赖
npm install

# 2. 开发模式
npm run dev
```

开发模式默认启动：

- API：`http://localhost:3000`
- Web：`http://localhost:5173`（已代理 `/api`）

生产运行：

```bash
# 1. 安装依赖
npm ci

# 2. 构建前端
npm run build

# 3. 可选：裁剪为生产依赖
npm prune --omit=dev

# 4. 启动服务
npm start
```

生产模式默认访问：`http://localhost:3000`

仓库已在 `data/cores/` + `data/bios/` 预置全部核心 + 街机 / FDS BIOS，无需额外下载。PSX BIOS 需自备放入 `data/bios/psx/`。

### Docker 部署

镜像地址：[yize8888/childhood-arcade](https://hub.docker.com/r/yize8888/childhood-arcade)

#### docker run

```bash
docker run -d \
  --name childhood-arcade \
  --restart unless-stopped \
  -p 3000:3000 \
  -v ./data:/data \
  -e MAX_UPLOAD_BYTES=104857600 \
  yize8888/childhood-arcade:latest
```

启动后访问：`http://服务器IP:3000`

#### Docker Compose

```yaml
services:
  childhood-arcade:
    image: yize8888/childhood-arcade:latest
    container_name: childhood-arcade
    restart: unless-stopped
    ports:
      - "3000:3000"
    environment:
      MAX_UPLOAD_BYTES: "104857600"
      # 首次启动默认管理员：admin / admin123
      # 建议首次登录后立即修改密码，或取消下面两行注释自定义初始账号
      # ADMIN_USERNAME: admin
      # ADMIN_PASSWORD: your-strong-password
    volumes:
      - ./data:/data
```

启动：

```bash
docker compose up -d
```

更新镜像：

```bash
docker compose pull
docker compose up -d
```

如果想从源码本地构建镜像：

```bash
docker compose up -d --build
```

### 环境变量（全部可选）

| 变量 | 默认 | 说明 |
|---|---|---|
| `PORT` | `3000` | HTTP 端口 |
| `DB_PATH` | `data/app.db` | SQLite 文件 |
| `UPLOADS_DIR` | `data/uploads` | ROM 上传 |
| `BIOS_DIR` | `data/bios` | BIOS |
| `SAVES_DIR` | `data/saves` | 云存档 |
| `CORES_DIR` | `data/cores` | 本地核心 |
| `ADMIN_USERNAME` / `ADMIN_PASSWORD` | `admin` / `admin123` | 首次 seed |
| `MAX_UPLOAD_BYTES` | `104857600` | 单 ROM 上限 |

### 反代要点

单端口 3000 同时跑 HTTP + WebSocket (`/api/rooms/:code/ws`)，反代需透传 `Upgrade` + `Connection`，连接超时 ≥ 60 s。WebRTC 默认用 Google + Cloudflare 公共 STUN，CGNAT 需自建 TURN。

---

## 🏗 架构

```
┌────────────────── Browser (Vue 3) ───────────────────┐
│  Gallery → Player (Nostalgist WASM)                  │
│     ↓ canvas.captureStream + AudioContext            │
│  WebRTC PeerConnection ←─ DataChannel (guest input)  │
└───────────────┬──────────────────────────────────────┘
                │ WebSocket /api/rooms/:code/ws
┌───────────────▼──────────────────────────────────────┐
│  Hono 4   +  /auth /roms /rooms /saves /bios /cores  │
│  room-hub (in-memory peer map) + signaling relay     │
└───────────────┬──────────────────────────────────────┘
                │ Drizzle ORM
┌───────────────▼──────────────────────────────────────┐
│  SQLite (better-sqlite3, WAL)                        │
│  users · sessions · roms · rooms · saves · favorites │
└──────────────────────────────────────────────────────┘
```

- 视频 / 输入 **端到端**，服务器不参与
- ROM 在 `data/uploads`，镜像升级不丢
- 核心本地托管，完全离线可用
- 房间仅在房主显式关闭或 admin 软删时失效

**技术栈** · Vue 3 · Vite 6 · Hono 4 · @hono/node-ws · Nostalgist 0.21 · better-sqlite3 · Drizzle ORM · bcryptjs · 原生 WebSocket + WebRTC · 纯手写 CSS（无 UI 框架）

---

## 📝 许可

MIT — 见 [LICENSE](./LICENSE)

**不附带任何 ROM 或游戏版权内容**。请自备合法 ROM；仓库内置的 arcade BIOS 仅供研究与个人备份使用。

```
            ▸ PRESS START TO CONTINUE ◂
                 1985 ─ 2026
```
