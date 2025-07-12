---
title: Git SSH 配置详细教程
date: 2025-07-03 11:04:55
tags: Git
categories:
  - 技术资料
---
配置 Git SSH 密钥的步骤如下，适用于 GitHub、GitLab 等平台：

### 一、生成 SSH 密钥
1. **打开终端**（Windows：Git Bash / CMD；Mac/Linux：Terminal）
2. **生成密钥**（替换邮箱）：
   ```bash
   ssh-keygen -t ed25519 -C "your_email@example.com"
   ```
   - 提示保存位置：按 `Enter` 使用默认路径 `~/.ssh/id_ed25519`
   - 设置密码（可选）：为密钥增加额外保护

   > 💡 如果系统不支持 Ed25519，改用 RSA：  
   > `ssh-keygen -t rsa -b 4096 -C "your_email@example.com"`

### 二、将公钥添加到 Git 平台
1. **复制公钥内容**：
   ```bash
   # macOS/Linux
   cat ~/.ssh/id_ed25519.pub | pbcopy

   # Windows (Git Bash)
   cat ~/.ssh/id_ed25519.pub | clip

   # 或手动打开文件复制
   ```
2. **添加到平台**：
   - **GitHub**：Settings → SSH and GPG keys → New SSH key
   - **GitLab**：Preferences → SSH Keys
   - **其他平台**：在用户设置中找到 SSH 密钥管理

### 三、配置 SSH 代理（推荐）
```bash
# 启动 ssh-agent
eval "$(ssh-agent -s)"

# 将私钥添加到代理
ssh-add ~/.ssh/id_ed25519  # 替换为你的私钥路径
```

### 四、测试连接
```bash
ssh -T git@github.com        # 测试 GitHub
ssh -T git@gitlab.com        # 测试 GitLab
```
成功响应示例：  
`Hi username! You've successfully authenticated...`

### 五、配置 Git 使用 SSH
克隆仓库时使用 SSH URL：
```bash
git clone git@github.com:username/repo.git
```
> 如果之前使用 HTTPS，可修改远程 URL：  
> ```bash
> git remote set-url origin git@github.com:username/repo.git
> ```

### 常见问题解决
1. **权限错误**：
   ```bash
   chmod 700 ~/.ssh
   chmod 600 ~/.ssh/id_ed25519*
   ```
2. **连接被拒绝**：
   - 检查网络是否允许 SSH（端口 22）
   - 平台是否支持 Ed25519（老旧系统建议用 RSA）
3. **多账号管理**：  
   在 `~/.ssh/config` 中配置多账号：
   ```config
   # 账号1 (默认)
   Host github.com
     HostName github.com
     User git
     IdentityFile ~/.ssh/id_ed25519
   
   # 账号2
   Host github-work
     HostName github.com
     User git
     IdentityFile ~/.ssh/work_key
   ```
   使用：`git clone git@github-work:company/project.git`

### 验证配置
```bash
ssh -vT git@github.com  # 查看详细连接过程
```

完成以上步骤后，即可免密码安全使用 Git 操作。遇到问题可检查 `~/.ssh/known_hosts` 文件或更新平台公钥。