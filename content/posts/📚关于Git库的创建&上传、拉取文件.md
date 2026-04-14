## 1. 创建裸仓库（Bare Repository）在Ubuntu中

```Bash
# 切换到 git 用户
sudo su - git

# 创建仓库目录并初始化裸仓库（以 project1 为例）
mkdir -p /home/git/repositories
cd /home/git/repositories
git init --bare project1.git        #project1可以修改为自己的仓库名称       

# 修正权限（确保 git 用户拥有）
chown -R git:git /home/git/repositories

# 🎯这三条命令的实际工作流（这里是说明，不用执行这些，其实是和上面命令一样的）
# 1. 创建仓库目录
mkdir -p /home/git/repositories
# 现在目录树：/home/git/repositories/

# 2. 进入该目录
cd /home/git/repositories

# 3. 创建裸仓库
git init --bare project1.git
# 输出：Initialized empty Git repository in /home/git/repositories/project1.git/
# 现在目录树：/home/git/repositories/project1.git/
# 其内部有：branches  config  description  HEAD  hooks  info  objects  refs

# 如果用了豆包的
# 创建仓库目录 
sudo -u git mkdir -p /home/git/repositories 
# 初始化裸仓库（服务器专用） 
sudo -u git git init --bare /home/git/repositories/project.git

```

## 📁 命令解析

#### 1. `mkdir -p /home/git/repositories`

|部分|解释|
|---|---|
|`mkdir`|创建目录（make directory）|
|`-p`|**递归创建**参数。如果父目录 `/home/git`不存在，会自动创建它。`p`代表 "parents"。|
|`/home/git/repositories`|这是为 Git 仓库准备的**专用存储目录**。|

**实际效果**：

- 如果 `/home/git`不存在 → 先创建 `/home/git`，再创建其下的 `repositories`目录。
- 如果 `/home/git`已存在 → 只创建 `repositories`目录。
- 最终得到路径：`/home/git/repositories/`

**最佳实践**：将仓库集中存放在一个目录下便于管理，如 `repositories/`、`gitserver/`等。

#### 2. `cd /home/git/repositories`

|部分|解释|
|---|---|
|`cd`|切换工作目录（change directory）|
|`/home/git/repositories`|目标路径，刚刚创建的目录|

**作用**：进入仓库目录，方便后续操作。这等同于“打开文件夹”的终端操作。

#### 3. `git init --bare project1.git`

这是**最关键的命令**，创建一个“裸仓库”。

|部分|解释|
|---|---|
|`git init`|初始化一个新的 Git 仓库|
|`--bare`|**裸仓库标志**。这是 Git 服务器的核心概念，与普通仓库有本质区别。|
|`project1.git`|仓库目录名。约定俗成以 `.git`结尾，表示是裸仓库。|

### 🔧 权限修正（重要！）

创建后，**必须确保 git 用户拥有该目录的所有权**：

```Bash
# 将整个 repositories 目录及其内容的所有者设为 git:git
sudo chown -R git:git /home/git/repositories/

# 验证权限
ls -la /home/git/
# 应显示：drwxr-xr-x  git git repositories/
ls -la /home/git/repositories/
# 应显示：drwxr-xr-x  git git project1.git/
```

## 2. 客户端测试连接

⚓**在宿主机（或任何有私钥的机器）上测试**：

```Bash
# 克隆仓库（注意路径对应）
git clone git@<虚拟机IP>:/home/git/repositories/project1.git

# 进入目录进行常规操作，进入仓库，创建文件，上传
cd project1
echo "# My Project" > README.md
git add .
git commit -m "Initial commit"
git push origin main
```

# 3. Clone、上传、下载Git库文件命令：

```Bash
# 克隆仓库（注意路径对应）
git clone git@<虚拟机IP>:/home/git/repositories/project1.git

# 上传
git add .
git commit -m "Initial commit"
git push origin main

# 下载
git pull origin master

# 上传、下载的命令均可以简化为：git push 、 git pull
```
