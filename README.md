# 🍅 番茄钟应用 (Pomodoro Timer App)

一个基于 Vue3 + Vite 构建的现代化番茄钟应用，帮助您提高工作效率和时间管理能力。

## 🌟 功能特性

### 🎯 核心功能
- **三种工作模式**：
  - 工作模式（25分钟）
  - 短休息（5分钟）
  - 长休息（15分钟）
- **智能计时器**：
  - 实时倒计时显示（mm:ss格式）
  - 开始/暂停/重置控制
  - 自动模式切换
  - 每完成4个番茄钟后自动进入长休息
- **任务管理**：
  - 添加新任务
  - 标记任务完成状态
  - 删除任务
  - 显示剩余任务数量
- **用户体验增强**：
  - 计时结束音频提示
  - 已完成番茄钟计数
  - 响应式设计，适配所有设备
  - 现代化 UI 界面，整体显示在屏幕中央

### 🛠 技术栈
- **前端框架**：Vue 3 (Composition API)
- **构建工具**：Vite
- **样式**：CSS3 + Scoped Styles
- **音频**：Web Audio API
- **响应式**：Mobile First 设计

## 🚀 快速开始

### 🐳 Docker 部署（推荐）

```bash
# 构建 Docker 镜像
docker build -t pomodoro-app .

# 运行容器
docker run -d -p 3000:3000 --name pomodoro-container pomodoro-app

# 访问应用
# 浏览器打开 http://localhost:3000
```

### 🌐 Nginx 部署

1. 构建项目：
   ```bash
   npm run build
   ```

2. 复制 `dist` 目录到 Nginx 网站目录

3. 配置 Nginx：
   ```nginx
   server {
       listen 80;
       server_name localhost;

       root /var/www/pomodoro;
       index index.html;

       location / {
           try_files $uri $uri/ /index.html;
       }

       location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
           expires 1y;
           add_header Cache-Control "public, immutable";
       }
   }
   ```

4. 重启 Nginx：
   ```bash
   systemctl restart nginx
   ```

### 🏗 本地开发

```bash
# 安装依赖
npm install

# 启动开发服务器
npm run dev

# 构建生产版本
npm run build

# 预览生产构建
npm run preview
```

## 📁 项目结构

```
pomodoro-app/
├── src/
│   ├── App.vue              # 主应用组件
│   ├── main.js              # 应用入口文件
│   └── components/
│       ├── PomodoroTimer.vue # 核心计时器组件
│       └── TaskManager.vue   # 任务管理组件
├── public/                  # 静态资源目录
├── dist/                    # 生产构建目录
├── package.json             # 项目依赖和脚本
├── vite.config.js           # Vite 配置文件
├── Dockerfile              # Docker 配置文件
└── README.md               # 项目说明文档
```

## 🎨 界面预览

应用采用现代化设计，具有直观的用户界面：
- 清晰的计时显示
- 明确的模式切换按钮
- 简洁的任务管理面板
- 响应式布局适配各种设备

## 🔧 配置说明

### 端口配置
- 开发服务器默认运行在 `http://localhost:3000`
- Docker 部署映射到主机 3000 端口
- Nginx 部署默认使用 80 端口

### 自定义配置
- 计时模式时长可在 `PomodoroTimer.vue` 组件中修改
- UI 样式可通过组件中的 `<style scoped>` 部分调整

## 🤝 贡献

欢迎提交 Issue 和 Pull Request 来改进这个项目！

## 📄 许可证

MIT License - 详见 [LICENSE](LICENSE) 文件

## 🙏 鸣谢

- [Vue 3](https://vuejs.org/)
- [Vite](https://vitejs.dev/)
- [Docker](https://www.docker.com/)

---
> Made with ❤️ using Vue 3 + Vite