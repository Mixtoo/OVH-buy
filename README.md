# OVH 服务器抢购系统

## � 目录
- [功能特性](#-功能特性)
- [系统要求](#-系统要求)
- [部署指南](#-部署指南)
  - [Docker 部署](#docker-部署)
  - [1Panel 反向代理配置](#1panel-反向代理配置)
- [功能模块说明](#-功能模块说明)
  - [服务器监控](#服务器监控)
  - [自动抢购](#自动抢购)
  - [Telegram 通知](#telegram-通知)
  - [Webhook 配置](#webhook-配置)
- [使用指南](#-使用指南)
- [常见问题](#-常见问题)
- [致谢](#-致谢)
- [社区](#-社区)

## ✨ 功能特性

- **实时监控**：监控 OVH 服务器库存状态
- **自动抢购**：自动下单可用服务器
- **多机房支持**：支持多个 OVH 机房监控
- **Telegram 通知**：实时推送库存和下单状态
- **Webhook 支持**：通过 Telegram 直接下单
- **批量操作**：支持批量添加监控任务
- **安全验证**：API 密钥保护

## 🖥️ 系统要求

- Docker 20.10.0+
- Docker Compose 2.0.0+
- 1Panel 1.0.0+（可选，用于反向代理）
- 公网 IP 地址（用于 Webhook 接收）

## 🚀 部署指南

### Docker 部署

1. **克隆仓库**
   ```bash
   git clone [仓库地址]
   cd 1112
   ```

2. **配置环境变量**
   编辑 `backend/.env` 文件：
   ```env
   API_SECRET_KEY=your_secure_key_here
   ```

3. **启动服务**
   ```bash
   docker-compose up -d --build
   ```

4. **访问系统**
   - 管理界面：http://YOUR_IP:20000
   - 初始配置：http://YOUR_IP:20000/settings

### 1Panel 反向代理配置

1. **安装 1Panel**
   ```bash
   curl -sSL https://resource.fit2cloud.com/1panel/package/quick_start.sh -o quick_start.sh && sudo bash quick_start.sh
   ```

2. **添加网站**
   - 登录 1Panel 控制台
   - 进入「网站」-「网站」-「创建网站」
   - 填写域名（如：ovh.yourdomain.com）
   - 选择「反向代理」
   
3. **配置反向代理**
   ```
   目标 URL: http://YOUR_SERVER_IP:20000
   发送域名: $host
   ```
   
4. **SSL 配置**
   - 在网站设置中启用 HTTPS
   - 选择「Let's Encrypt」自动申请证书
   - 开启「强制 HTTPS」

5. **Webhook 配置**
   - 在 Telegram Bot 设置 Webhook
   - Webhook URL: `https://yourdomain.com/api/telegram/webhook`
   - 在系统设置中配置 Telegram Bot Token 和 Chat ID

## 🛠️ 功能模块说明

### 服务器监控

- 实时监控服务器库存状态
- 支持多机房同时监控
- 可设置监控间隔
- 库存变化实时通知

### 自动抢购

- 自动下单可用服务器
- 支持批量下单
- 2分钟防重复下单保护
- 下单成功自动通知

### Telegram 通知

- 库存变化通知
- 下单状态通知
- 系统报警通知
- 支持交互式按钮操作

### Webhook 配置

- 接收 Telegram 回调
- 支持内联键盘交互
- 实时响应库存状态
- 支持直接下单操作

## 🤖 Telegram 机器人使用指南

### 基本命令

- `/start` - 显示欢迎信息和帮助
- `/help` - 显示帮助信息
- `/status` - 查看当前监控状态
- `/list` - 查看监控中的服务器列表

### 下单命令格式

1. **基本格式**
   ```
   <plan_code> [datacenter] [quantity]
   ```

2. **使用示例**
   - 下单指定机房和数量：
     ```
     24sk202 rbx 1
     ```
   - 下单指定机房，默认数量为1：
     ```
     24sk202 rbx
     ```
   - 下单所有机房，指定数量：
     ```
     24sk202 1
     ```
   - 下单所有机房，默认数量为1：
     ```
     24sk202
     ```

3. **参数说明**
   - `plan_code`: 服务器型号（必填）
   - `datacenter`: 机房代码（可选），如 rbx, gra 等
   - `quantity`: 数量（可选，默认为1）

### 交互式按钮

当服务器有货时，机器人会发送消息并附带操作按钮：

```
🟢 服务器有货通知

型号: 24sk202
机房: GRA (法国)
状态: 有货

[ 立即下单 ]  [ 查看详情 ]
```

- 点击「立即下单」直接下单
- 点击「查看详情」查看服务器详细信息

### 订单确认

下单后会收到确认消息：
```
✅ 订单已提交

型号: 24sk202
机房: GRA
数量: 1
状态: 处理中...

[ 取消订单 ]
```

### 订单状态通知

- 下单成功: `🟢 订单 #1234 下单成功`
- 下单失败: `🔴 订单 #1234 失败: 库存不足`
- 订单完成: `✅ 订单 #1234 已完成`

## 📖 使用指南

1. **首次配置**
   - 访问 `http://your-domain/settings`
   - 配置 OVH API 凭证
   - 设置 Telegram 通知
   - 配置 Webhook URL

2. **添加监控任务**
   - 在监控页面添加服务器型号
   - 选择目标机房
   - 设置通知方式

3. **自动抢购**
   - 在抢购队列中添加任务
   - 设置抢购数量
   - 系统自动执行下单

## ❓ 常见问题

**Q: 如何查看系统日志？**
A: 在 1Panel 的容器管理中查看容器日志

**Q: Webhook 不工作怎么办？**
- 检查 1Panel 反向代理配置
- 确认端口 20000 已开放
- 检查 Telegram Bot Token 和 Chat ID 是否正确

**Q: 如何更新系统？**
```bash
git pull
docker-compose up -d --build
```

## 🙏 致谢

- [@szzc1227](https://t.me/szzc1227) - 协助测试
- [@mtrucc](https://github.com/mtrucc) - 技术支持

## 🌐 社区

- **Telegram 群组：** [https://t.me/OVHGroup](https://t.me/OVHGroup) - OVH 中文用户交流分享群组
- **问题反馈：** 在 GitHub 提交 Issue

## 📝 许可证

MIT License
