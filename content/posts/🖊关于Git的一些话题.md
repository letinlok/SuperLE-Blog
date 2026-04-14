# ❓ 无法上传、下载、Clone

#### 1.**确认仓库状态**

在宿主机克隆的仓库目录中，打开终端执行：

```Bash
git status

```

![](https://secure2.wostatic.cn/static/9tArSTfTxD8akLLtf91hho/1.png?auth_key=1776080471-xy2eRAGqEy2wFjgN7kzDFU-0-4a2468ec90941f00b209228852c6b66b)

#### 2.配置Git的用户信息

在宿主机上运行以下命令，替换为你自己的信息：

```Bash
# 设置用户名
git config --global user.name "你的名字"
# 设置邮箱
git config --global user.email "你的邮箱@example.com"
```

```Bash
# 去掉 --global 参数，只对当前仓库生效
git config user.name "你的名字"
git config user.email "你的邮箱@example.com"
```

```Bash
# 查看所有配置
git config --list

# 或查看特定配置
git config user.name
git config user.email
```

#### 为什么需要这个配置？

- Git需要记录每次提交的作者信息
- 这些信息会永久保存在提交历史中
- 多人协作时方便知道谁修改了代码
- 如果使用GitHub/GitLab等服务，邮箱会与账号关联

- 检查远程仓库地址命令：
```bash
git remote -v
```
# 🖊 Other

```Bash
1. 提交修改到本地仓库
# 添加所有修改
git add .
# 或添加指定文件
git add 文件名

# 提交到本地仓库
git commit -m "描述修改内容"

2. 推送到远程仓库（Ubuntu中的仓库）
git push origin 分支名
# 通常首次推送到主分支
git push origin main
# 或
git push origin master

```
# 📕Ubuntu中Clone代码权限问题

```Bash
# 1. 解除 Git 安全权限限制（解决 dubious ownership 报错）
git config --global --add safe.directory /home/git/repositories/lecode.git 
# 2. 重新克隆 gitcode 分支，查看你的代码 
git clone -b gitcode /home/git/repositories/lecode.git my-lecode
```
# 👀查看Git分支
## 一、本地电脑（Windows / 你的开发机）

### 1. 查看**本地所有分支**
```Bash
git branch
```

例：
- 带 `*` 的是**当前所在分支**
- 你会看到 `* gitcode`，证明你在这个分支
### 2. 查看**远程服务器上的所有分支**
```Bash
git branch -r
```

会显示：`origin/gitcode`
### 3. 查看**全部分支（本地 + 远程）**
```Bash
git branch -a
```
## 二、Ubuntu 服务器（裸仓库，专用命令）
#### 因为服务器仓库属于 `git` 用户，**必须加权限前缀**，直接执行：
``` bash
sudo -u git git branch -a
```
✅ 运行后就能看到服务器上的所有分支（你会看到 `gitcode` 分支）