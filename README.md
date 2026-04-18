# StarRailAssistant 攻略站前端

基于 Vue 3 + Vite + Element Plus 的攻略展示与上传前端项目。

## 项目简介

本项目用于展示攻略列表、查看攻略详情、下载攻略 JSON、上传攻略 JSON。
当前已将“攻略详情弹窗”和“上传攻略弹窗”拆分为独立组件，上传流程抽离到 composable，便于维护与复用。

## 主要功能

- 攻略列表展示
- 攻略详情弹窗查看
- 攻略 JSON 下载
- 拖拽/点击上传 JSON 文件
- JSON 格式校验与错误提示
- 深色模式切换

## 技术栈

- Vue 3
- Vite
- Element Plus
- Axios
- @vueuse/core

## 运行环境

- Node.js: `^20.19.0 || >=22.12.0`

## 快速开始

```sh
npm install
npm run dev
```

默认会启动 Vite 开发服务。

## 常用命令

```sh
npm run dev
npm run build
npm run preview
```

## 环境变量

请求客户端使用 `VITE_APP_API_BASE_URL` 作为 `axios` 的 `baseURL`。

可在项目根目录创建 `.env.development`：

```env
VITE_APP_API_BASE_URL=/api
```

说明：

- 当 `VITE_APP_API_BASE_URL=/api` 时，可配合 `vite.config.js` 中的代理转发。
- 当前开发代理会把 `/api` 转发到SRA服务器。

## 目录结构

```text
.
├─ public/
├─ src/
│  ├─ api/
│  │  └─ strategy.js               # 攻略相关接口
│  ├─ components/
│  │  ├─ Header.vue
│  │  ├─ Aside.vue
│  │  ├─ StrategyDetailDialog.vue  # 攻略详情弹窗
│  │  └─ UploadStrategyDialog.vue  # 上传攻略弹窗
│  ├─ composables/
│  │  └─ useStrategyUpload.js      # 上传流程逻辑
│  ├─ utils/
│  │  └─ request.js                # axios 实例与拦截器
│  ├─ App.vue
│  └─ main.js
├─ vite.config.js
└─ package.json
```

## 接口约定

接口定义位于 `src/api/strategy.js`：

- `GET /strategy/list`：获取攻略列表
- `GET /strategy/detail?id={id}`：获取攻略详情
- `POST /strategy/create`：上传新攻略

上传的 JSON 需要是可被后端 `create` 接口接收的对象结构。

## 开发说明

- 页面入口在 `src/App.vue`
- 请求封装在 `src/utils/request.js`，统一处理错误消息

## 常见问题

### 1) 页面报 `Cannot read properties of undefined (reading 'length')`

通常是列表接口返回值不是数组。当前页面已对列表做数组兜底处理；如果仍出现，请检查后端返回结构。

### 2) 上传 JSON 失败

- 确认文件后缀或 `MIME` 为 JSON
- 确认 JSON 语法合法
- 确认后端 `create` 接口字段要求

### 3) 请求地址不正确

请检查：

- `.env.*` 中 `VITE_APP_API_BASE_URL`
- `vite.config.js` 的 `server.proxy` 配置

## 构建发布

```sh
npm run build
npm run preview
```

`vite.config.js` 当前配置了 `base: '/sra-strategy-site/'`，部署到子路径时请保持一致。

