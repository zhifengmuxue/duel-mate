# Duel Mate 游戏王线下对战工具

> 🎮 一款使用 [uni-app](https://uniapp.dcloud.io/) 构建的跨平台应用，用于实现双人对战、比分记录、规则管理等功能。

## 📦 项目概述

**Duel Mate** 是一个支持 Android、iOS、H5、小程序多端运行的轻量对战工具，适合朋友间游戏对战、桌游对局、运动比分记录等场景。

## 🚀 技术栈

- [uni-app](https://uniapp.dcloud.io/)
- Vue 3 Composition API

## 📂 目录结构

```bash
duel-mate/
├── public/             # 公共静态资源
│   └── index.html      # H5 入口模板
├── src/                # 源代码
│   ├── pages/          # 页面文件
│   │   └── index/      # 首页模块
│   │       └── index.vue # 首页
│   ├── static/         # 静态资源
│   │   └── logo.png    # 应用图标
│   ├── App.vue         # 应用入口组件
│   ├── main.ts         # 主逻辑入口（TypeScript）
│   ├── manifest.json   # 应用配置
│   ├── pages.json      # 页面路由配置
│   └── uni.scss        # uni-app 全局样式变量
├── .gitignore          # Git 忽略文件
├── babel.config.js     # Babel 配置
├── package.json        # 项目依赖和脚本
├── postcss.config.js   # PostCSS 配置
├── shims-uni.d.ts      # uni-app TypeScript 声明文件
├── shims-vue.d.ts      # Vue TypeScript 声明文件
└── tsconfig.json       # TypeScript 配置
```

## 🧑‍💻 运行与开发

### 安装依赖

```bash
npm install
```

### 启动开发服务器（H5）

```bash
npm run dev:%PLATFORM%
# 示例：npm run dev:h5 或 npm run dev:app
```

### 构建打包

```bash
npm run build:%PLATFORM%
# 示例：npm run build:app
```

### 平台构建说明

| 平台       | 命令示例             | 注意事项                    |
|------------|----------------------|-----------------------------|
| H5         | `npm run dev:h5`     | 浏览器中调试                |
| 微信小程序 | `npm run dev:mp-weixin` | 微信开发者工具             |
| Android/iOS| `npm run dev:app`    | 需使用 HBuilderX 或 CLI 打包 |


## 🤝 贡献指南

欢迎贡献功能、修复问题或提出建议。

1. Fork 本仓库
2. 创建分支：`git checkout -b feat/your-feature`
3. 提交修改：`git commit -m '添加某功能'`
4. 推送分支：`git push origin feat/your-feature`
5. 提交 Pull Request

## 📄 License

MIT License

