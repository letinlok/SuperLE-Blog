---
title: "✍关于Git服务器的搭建"
date: 2026-04-13
draft: false
summary: "轻量级 SSH Git 服务器（VMware虚拟机+Ubuntu20.04）部署"
tags: ["Git", "Ubuntu"]
---

### 🚀轻量级 SSH Git 服务器（VMware虚拟机+Ubuntu20.04）

#### 1. 虚拟机环境准备

```Bash
# 更新系统并安装基础软件
sudo apt update
sudo apt install -y git openssh-server

# 确认 Git 版本
git --version
```

#### 2. 创建专用 Git 用户

```Bash
# 创建不可登录 shell 的 git 用户（更安全）  这条命令用于创建一个专用的 Git 系统用户
sudo adduser --system --shell /bin/bash --gecos 'Git Version Control' --group --disabled-password --home /home/git git
# OR

# 创建不可登录的 git 系统用户（仅用于Git/SSH操作，更安全） ---豆包
sudo adduser --system --shell /usr/sbin/nologin --gecos 'Git Version Control' --group --home /home/git git

# 💡这条命令的深层用意
# 安全隔离：专为 Git 服务创建单独用户，避免使用 root或你的个人用户操作仓库。
# 无密码登录：结合 SSH 公钥认证，实现既安全又便捷的免密访问。
# 权限统一：所有仓库文件的所有者都是 git:git，避免权限混乱。
# 无密码：系统用户默认无密码，结合SSH公钥认证实现免密访问。
```

### 🔍 创建后验证

```Bash
# 查看用户信息
grep git /etc/passwd
# 输出类似：git:x:998:998:Git Version Control:/home/git:/bin/bash
# 注意 UID 是 998（<1000 的系统用户）

# 查看用户组
grep git /etc/group
# 输出类似：git:x:998:

# 尝试切换用户（会失败，因为禁用密码）
su - git
# 提示：This account is currently not available.
```

#### 3. 配置 SSH 密钥登录（免密）

**在宿主机（你的物理机）生成密钥对**（如果已有 `id_rsa.pub`可跳过）：

```Bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

##### 3.1Windows 生成 SSH 密钥

1. **打开 PowerShell**
    
    按 `Win + X`，选择 **Windows PowerShell** 或 **终端**。
    
2. **生成密钥对**
    
    执行以下命令（将 `your_email@example.com`替换为你的邮箱或标识）：
    

```Bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

**参数解释**：

- `-t rsa`：指定密钥类型为 RSA。
    
- `-b 4096`：指定密钥长度为 4096 位（更安全）。
    
- `-C`：添加注释，通常用于标识密钥所有者。
    
    3. **处理交互提示（关键步骤）**

执行命令后，会依次出现三个提示，**请按以下方案操作**：

| 提示信息                                                  | 推荐操作     | 说明                                          |
| ----------------------------------------------------- | -------- | ------------------------------------------- |
| `Enter file in which to save the key` **(输入保存密钥的文件)** | **按回车键** | 使用默认路径：`C:\Users\你的用户名.ssh\id_rsa` 可以指定存放路径 |
| `Enter passphrase` **(输入密码短语)**                       | **按回车键** | **留空**（不设置密码）。这样后续使用最方便，适合虚拟机内网环境。          |
| `Enter same passphrase again` **(再次输入密码短语)**          | **按回车键** | 确认留空。                                       |

#### 4. **处理交互提示（关键步骤）**

看到类似下方的输出，即表示生成成功：

```Bash
Your identification has been saved in C:\Users\你的用户名\.ssh\id_rsa.
Your public key has been saved in C:\Users\你的用户名\.ssh\id_rsa.pub.
The key fingerprint is:
SHA256:xxxxxxxxxxxxxxxx your_email@example.com
```

此时在 `C:\Users\你的用户名.ssh`目录下会生成两个文件：

- `id_rsa`：**私钥**（Private Key），**绝不可泄露**，相当于你的身份证。
- `id_rsa.pub`：**公钥**（Public Key），需要上传到 Ubuntu 虚拟机。
- 进入密钥所在的路径，使用notepad++或记事本打开并复制`id_rsa.pub`内容。

### 🚀 后续操作指，上传密钥到Git服务器

复制好公钥后，回到 Ubuntu 虚拟机（Git服务器），执行以下命令完成配置：

```Bash
# 第一步：确保/home/git目录的所有者是git用户（否则后续操作会有权限问题） ---豆包
sudo chown -R git:git /home/git/

# 切换到 git 用户的家目录并创建 .ssh 文件夹
sudo -u git mkdir -p /home/git/.ssh

# 将公钥写入授权文件（请将 PASTE_YOUR_PUBLIC_KEY_HERE 替换为你实际复制的公钥内容）
echo "PASTE_YOUR_PUBLIC_KEY_HERE" | sudo -u git tee -a /home/git/.ssh/authorized_keys

# 设置严格权限（SSH 强制要求）
sudo -u git chmod 700 /home/git/.ssh
sudo -u git chmod 600 /home/git/.ssh/authorized_keys

# 验证权限（可选）---豆包 
ls -la /home/git/.ssh/ 
# 正确输出： 
# drwx------ git git .ssh/ 
# -rw------- git git authorized_keys
```

完成后，在 Windows 宿主机通过 `ssh git@<虚拟机IP>`实现免密登录。

```Bash
# 测试免密登录（宿主机执行） 
ssh git@<虚拟机IP>
 
# ✅ 登录成功提示（无密码输入直接进入）： 
# Welcome to Ubuntu 20.04 LTS (GNU/Linux 5.4.0-xx-generic x86_64)
# ...（但会立即退出，因为git用户shell是nologin，仅允许git操作） 
#❌ 登录失败排查： 
# 1. 检查虚拟机IP是否正确； 
# 2. 检查authorized_keys文件是否包含你的公钥； 
# 3. 检查权限：.ssh目录必须700，authorized_keys必须600； 
# 4. 重启SSH服务：sudo systemctl restart sshd
```