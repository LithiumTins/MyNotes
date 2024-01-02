# 基本命令

## 查看帮助
```bash
git help <command>
git <command> --help
man git-<command>
```
<br>

## config
Git的配置文件存在于以下三个目录：
- `/etc/gitconfig`：系统级别
- `~/.gitconfig`：用户级别，使用`--global`选项时
- `<repository>/.git/config`：仓库级别

### 配置方式
```bash
git config --global user.name <name>        # 设置用户名
git config --global user.email <email>      # 设置邮箱
git config --global core.editor <editor>    # 设置编辑器
```

### 查看配置
```bash
git config --list
```
<br>

## 初始化仓库
```bash
git init
```
<br>

## 克隆仓库
```bash
git clone <url>         # 克隆仓库
git clone <url> <dir>   # 克隆仓库到指定目录
git clone <url> -o <name>   # 克隆仓库并指定远程仓库名
```

## glob模式的正则表达式
可以在 `git` 命令指定文件时使用，语法如下：
- `*` ：0或多个任意字符
- `[abc]` ：匹配a、b、c中的任意一个字符
- `?` ：匹配任意一个字符
- `a/**/z` ：两个 `*` 匹配任意目录，如 `a/z`、`a/b/z`、`a/b/c/z` 等

<br>

## .gitignore
可以在仓库的根目录下创建 `.gitignore` 文件，用于指定不需要跟踪的文件，语法如下：
- 空行会被忽略
- `#` 开头的行是注释
- 没有 `/` 在前面的文件名会递归匹配子目录下的文件，如果只想要根目录下的文件，需要写成 `/file` 的形式
- 以 `/` 结尾表示目录
- `!` 表示白名单

<br>

## 跟踪文件
```bash
# 跟踪文件
git add <file>
```
<br>

## 查看状态
```bash
# 完整提示信息
git status

# 简短提示信息，或者--short
git status -s
```
<br>

## 查看区别
```bash
# 查看工作区和暂存区的区别
git diff

# 查看暂存区和仓库的区别
git diff --cached
```
<br>

## 提交
```bash
# 提交暂存区的文件（打开编辑器输入提交信息，注释显示status信息）
git commit

# 注释还会显示 diff 信息
git commit -v

# 不打开编辑器
git commit -m <message>

# 自动把跟踪的文件暂存起来
git commit -a
```
<br>

## 移除文件
```bash
# 从仓库和工作区移除文件
git rm <file>

# 从仓库移除文件，保留工作区中的文件
git rm --cached <file>

# 如果文件已经添加到暂存区，需要强制删除
git rm -f <file>
```
<br>

## 移动文件
```bash
# 移动文件
git mv <old> <new>
```
<br>

## 查看提交历史
```bash
# 查看提交历史
git log


# ------------ 筛选提交历史 ------------
# 最新两条记录
git log -2

# 最近一周的记录
git log --since=1.week
git log --after=1.week

# 一周之前的记录
git log --until=1.week
git log --before=1.week

# 2019年1月1日之后的记录
git log --since=2019-01-01

# 2年1天3小时之前的记录
git log --until="2 years 1 day 3 hours ago"

# 提交者为lin的记录
git log --committer=lin

# 作者为lin的记录
git log --author=lin

# 包含关键字lin的记录
git log --grep=lin

# 作者为lin或者包含关键字lin的记录
git log --author=lin --grep=lin

# 作者为lin同时包含关键字lin的记录
git log --all-match --author=lin --grep=lin

# 添加或移除了函数func的记录
git log -Sfunc


# ------------ 定制显示方式 ------------
# 显示差异
git log -p

# 对于每次提交，显示每文件的统计信息，以及整个提交的统计信息
git log --stat

# 只显示提交的统计信息
git log --shortstat

# 显示更改文件的文件名
git log --name-only

# 显示git status -s的结果
git log --name-status

# 只显示哈希值的前几位
git log --abbrev-commit

# 使用相对时间显示
git log --relative-date

# 绘制提交树
git log --graph

# 格式化输出
git log --pretty=<format>

# 不显示各个分支所在的位置
git log --no-decorate

# 控制分支位置显示方式
git log --decorate={short|full|auto|no}

# 显示所有分支的提交
git log --all
```

### pretty选项
- `oneline` ：每条记录显示在一行
- `short` ：显示简短的提交信息
- `full` ：显示完整提交信息
- `fuller` ：显示更加完整的提交信息
- `format` ：使用方式是 `--pretty=format:<style>` ，在 `<style>` 中可以使用格式化占位符
    - `%H` ：提交的完整哈希值
    - `%h` ：提交的简短哈希值
    - `%T` ：树的完整哈希值
    - `%t` ：树的简短哈希值
    - `%P` ：父对象的完整哈希值
    - `%p` ：父对象的简短哈希值
    - `%an` ：作者的名字
    - `%ae` ：作者的邮箱
    - `%ad` ：作者修改的日期
    - `%ar` ：作者修改的相对日期
    - `%cn` ：提交者的名字
    - `%ce` ：提交者的邮箱
    - `%cd` ：提交日期
    - `%cr` ：提交的相对日期
    - `%s` ：提交信息

<br>

## 撤销操作
```bash
# 重新提交
git commit --amend

# 撤销暂存区的文件，不会影响工作区
git reset HEAD <file>

# 撤销对文件的修改
git checkout -- <file>
```

## 远程仓库
```bash
# 查看远程仓库
git remote

# 显示url（push/pull）
git remote -v

# 添加远程仓库
git remote add <name> <url>

# 获取远程仓库到本地（不会合并）
git fetch <remote>

# 拉取远程仓库并合并本地
git pull <remote> <branch>

# 推送本地仓库到远程
git push <remote> <branch>

# 查看远程仓库信息
git remote show <remote>

# 重命名远程仓库
git remote rename <old> <new>

# 删除远程仓库
git remote remove <remote>
```

## 标签
```bash
# 列出标签
git tag

# 列出符合模式的标签
git tag -l <pattern>

# 创建轻量标签
git tag <tag>

# 创建附注标签（包含打标签的人和时间等存储在标签中的额外各种信息）
git tag -a <tag> -m <message>

# 对于指定的提交打标签
git tag -a <tag> <hash>

# 查看标签信息
git show <tag>

# 推送标签到远程
git push <remote> <tag>

# 推送所有标签到远程
git push <remote> --tags

# 删除本地标签
git tag -d <tag>

# 检出标签（在此处创建一个新的分支）
git checkout -b <branch> <tag>
```
<br>

## 别名
```bash
# 设置别名
git config --global alias.<alias> <command>

# 别名执行外部命令
git config --global alias.<alias> '!<command>'
```
<br>

## 分支创建
```bash
# 创建分支（不切换分支）
git branch <branch>
```
<br>

## 分支切换
```bash
# 切换分支
git checkout <branch>

# 创建并切换分支
git checkout -b <branch>
```
<br>

## 合并分支
```bash
# 合并分支
git merge <branch>
```
<br>

## 删除分支
```bash
# 删除已经合并的分支
git branch -d <branch>

# 强制删除分支
git branch -D <branch>
```
<br>

## 分支管理
```bash
# 查看分支
git branch

# 查看分支与指向的提交
git branch -v

# 查看已经合并到当前分支的分支
git branch --merged

# 查看没有合并到当前分支的分支
git branch --no-merged
```

## 远程分支
```bash
# 同步远程分支
git fetch <remote>

# 推送本地分支到远程
git push <remote> <branch>

# 删除远程分支
git push <remote> <local_branch>:<remote_branch>

# 缓存推送密码
git config --global credential.helper cache

# 合并远程分支
git merge <remote>/<branch>

# 创建本地副本分支
git checkout -b <branch> <remote>/<branch>
git checkout --track <remote>/<branch>

# 更改本地分支跟踪的远程分支
git branch -u <remote>/<branch>
git branch --set-upstream-to=<remote>/<branch>

# 合并上游分支
git merge @{u}
git merge @{upstream}

# 查看所有跟踪分支
git branch -vv

# 删除远程分支
git push <remote> --delete <branch>
```

## 变基
```bash
# 变基到别的分支上
git rebase <branch>

# 变基到父分支的父分支上（仅取从父分支分叉出来以后的提交）
git rebase --onto <grandparent> <parent> <branch>

# 拉取并变基
git pull --rebase <remote> <branch>

# 配置拉取时自动变基
git config --global pull.rebase true
```

<br><br>

# 服务器上的Git
架设在服务器上的远程Git仓库，通常是一个**裸仓库**，没有工作区。

## 协议
常用的有以下四种协议。


### 本地协议
简单地把仓库放在本地的另一个目录下，常与NFS一起使用，可以使用以下两种方式指定路径：
```bash
git clone /opt/git/project.git
git clone file:///opt/git/project.git
```
添加一个本地仓库到远程仓库列表中：
```bash
git remote add <name> /opt/git/project.git
```

#### 优点
操作简单

#### 缺点
需要配置共享文件系统，并且速度慢，不能保护仓库

---

### HTTP协议
以前的HTTP协议只能读取文件，被称为**哑HTTP协议**，新版本的被称为**智能HTTP协议**，可以读写文件。

#### 哑HTTP协议
把仓库当做一个普通的文件，提供文件服务。把它放在HTTP根目录下，然后设置一个叫做 `post-update` 的钩子就可以了。
```bash
cd /var/www/htdocs
git clone --bare /opt/git/project.git
cd project.git
mv hooks/post-update.sample hooks/post-update
chmod a+x hooks/post-update
```

#### 优点
可以使用URL与用户名密码进行访问，比较方便。HTTP协议也不容易被防火墙拦截。

#### 缺点
有些服务器上HTTP/S的架设比较麻烦，并且频繁验证身份没有SSH方便。

---

### SSH协议
SSH协议受支持度很高，就算需要架设也比较容易。
```bash
git clone ssh://user@server/project.git
git clone user@server:project.git
```

#### 优点
架设简单，安全性高。

#### 缺点
需要用户有SSH帐号，不利于匿名访问。

---

### Git协议
类似与SSH协议，但是访问无需任何授权，在版本库中创建一个名为 `git-daemon-export-ok` 的文件，就可以使用Git协议访问了。没有授权机制，要么所有人都可以访问，要么就不可以。

#### 优点
速度非常快，因为无需加密和授权。

#### 缺点
没有访问控制，基本上需要配合其他协议来使用，一般提供只读服务。

## 在服务器上搭建Git
假设服务架设在一个支持SSH的服务器上，则访问控制可以通过服务器的文件系统权限来控制。
```bash
# 把现有的仓库导出为裸仓库（相当于只拷贝.git目录）
git clone --bare <repo> <repo>.git

# 把裸仓库放在服务器上
scp -r <repo>.git user@server:/opt/git

# 到服务器上初始化仓库（自动修改组权限为可写）
ssh user@server
cd /opt/git/<repo>.git
git init --bare --shared
```
在这样的情况下，让团队的每个人都拥有读写权限，有三种方法：
- 为每个人创建一个SSH账号
- 创建一个 `git` 账号，让每个人都发送一个公钥到服务器上
- 通过LDAP服务等进行集中授权

## 生成SSH公钥
```bash
ssh-keygen
```

## 配置服务器的SSH访问
```bash
# 创建一个git用户
sudo adduser git 

# 切换到git用户
su git

# 在家目录下创建一个公钥存放目录
cd ~
mkdir .ssh && chmod 700 .ssh

# 使用authorized_keys方法认证
touch .ssh/authorized_keys && chmod 600 .ssh/authorized_keys

# 把公钥添加到authorized_keys文件中
cat id_rsa.pub >> .ssh/authorized_keys

# 创建一个空的仓库
cd /opt/git
mkdir <repo>.git
cd <repo>.git
git init --bare

# 推送仓库的初始化版本
cd <repo>
git init
git add .
git commit -m "initial commit"
git remote add origin git@server:/opt/git/<repo>.git
git push origin master

# 克隆仓库
git clone git@server:/opt/git/<repo>.git

# 限制git用户只能通过git-shell登录，先确保git-shell在系统中并且在/etc/shells中
cat /etc/shells
which git-shell
sudo vim /etc/shells

# 然后配置每个用户的登录shell
sudo chsh <user>

# 获取git shell帮助
git help shell
```

## 配置服务器的Git访问
```bash
# 启动git守护进程
git daemon --reuseaddr --base-path=/opt/git/ /opt/git/

# 配置成守护进程自动启动
sudo vim /etc/event.d/local-git-daemon

# 添加以下内容
start on startup
stop on shutdown
exec /usr/bin/git daemon \
    --user=git --group=git \
    --reuseaddr \
    --base-path=/opt/git/ \
    /opt/git/
respawn

# 直接启动
initctl start local-git-daemon

# 允许仓库的无授权访问
cd /opt/git/<repo>.git
touch git-daemon-export-ok
```

## 智能HTTP协议
在服务器上通过一个名为 `git-http-backend` 的CGI脚本来实现，它读取 `git fetch` 或者 `git push` 命令发送的请求头来检查客户端是否支持智能HTTP协议，如果不支持则按照哑HTTP协议处理。<br>
以下使用 `Apache` 作为HTTP服务器：
```bash
# 安装Apache
sudo apt-get install apache2 apache2-utils

# 启用mod_cgi、mod_alias、mod_env模块
a2enmod cgi alias env

# 配置Git根目录
SetEnv GIT_PROJECT_ROOT /opt/git

# 放开所有仓库的访问权限
SetEnv GIT_HTTP_EXPORT_ALL

# 令Git请求被git-http-backend脚本处理
ScriptAlias /git/ /usr/lib/git-core/git-http-backend/
```
然后需要配置 `Apache` 接受通过 `/git` 路径的请求，把以下的内容加入 `Apache` 的配置文件：
```
<Directory "/usr/lib/git-core/*">
    Options ExecCGI Indexes
    Order allow,deny
    Allow from all
    Require all granted
</Directory>
```
对于写操作，可能需要进行授权，可以加入以下内容：
```
<LocationMatch "^/git/.*/git-receive-pack$">
    AuthType Basic
    AuthName "Git Access"
    AuthUserFile /opt/git/.htpasswd
    Require valid-user
</LocationMatch>
```
然后创建一个密码文件 `.htaccess` ，把用户添加到文件中：
```bash
htdigest -c /opt/git/.htpasswd "Git Access" <user>
```

## GitWeb
Git提供了一个简易的网页，可以查看项目的提交历史等信息，这通过一个名为 `gitweb` 的CGI脚本来实现
```bash
# 在linux使用默认的lighttpd服务器
git instaweb

# 使用webrick服务器
git instaweb --httpd=webrick

# 关闭服务器
git instaweb --stop
```
在一些服务器上运行 `GitWeb` 可能需要安装软件包：
```bash
# 获得Git源代码，它包含了GitWeb
git clone git://git.kernel.org/pub/scm/git/git.git

# 编译GitWeb
cd git
make GITWEB_PROJECTROOT="/opt/git" prefix=/usr gitweb

# 把CGI脚本复制过去
sudo cp -Rf gitweb /var/www/
```
然后需要配置 `Apache` 让它使用这个脚本：
```
<VirtualHost *:80>
    ServerName gitserver
    DocumentRoot /var/www/gitweb
    <Directory /var/www/gitweb>
        Options ExecCGI +FollowSymLinks +SymLinksIfOwnerMatch
        AllowOverride All
        order allow,deny
        Allow from all
        AddHandler cgi-script cgi
        DirectoryIndex gitweb.cgi
    </Directory>
</VirtualHost>
```

## GitLab
上面提到的 `GitWeb` 是一个非常简单的网页。如果需要一个更强的服务器，可以使用 `GitLab` 。

### 安装
过程涉及到更多的东西，可以：
- 下载虚拟机镜像
- 在[bitnami](https://bitnami.com/stack/gitlab)上下载一键安装包
- 或者跟着[readme](https://gitlab.com/gitlab-org/gitlab-ce/tree/master)安装

### 管理
在浏览器中访问 `GitLab` 的IP地址，使用默认的用户名 `admin@local.host` 和密码 `5iveL!fe` 登录，然后修改密码。

#### 使用者
每个人都有自己的项目命名空间，他的项目一般在 `http://<server>/<user>/<project>` 下。屏蔽一个用户后，他的数据会被保留，但无法再登录；删除一个用户后，他的数据会被删除。

#### 组
组是一些项目的集合，以及这些项目的访问权限。组也是一个命名空间，组的项目一般在 `http://<server>/<group>/<project>` 下。组可以有多个成员，每个成员都有自己的权限。

#### 项目
项目就是一个版本库，可以属于一个用户，也可以属于一个组。

#### 钩子
钩子相当于是某种触发器，可以在某些事件发生时触发钩子， `GitLab` 提供了系统级和项目级的钩子。

<br><br>

# 分布式Git

## 分布式工作流
- **集中式工作流**：只有一个中央仓库，每个人都从中央仓库拉取和推送。每个人推送之前都需要先拉取，保证使用 fast-forward 方式推送。
- **集成管理者工作流**：有一个中央仓库和一个维护者。其他人如果需要贡献，先克隆出一个公共仓库，然后推送到公共仓库，维护者再从公共仓库拉取、测试、推送到中央仓库。
- **司令官和副官工作流**：有一个参考仓库和司令官，有许多副官管理项目的不同部分并创建副仓库。开发者完成工作以后，变基到参考仓库的master分支上；副官审核以后把这些工作整合进副仓库的master分支；司令官把副仓库的master分支变基到参考仓库的master分支上。

## 向一个项目贡献

### 提交准则
Git官方在源代码中提供了一些相关的提示，放在 `Documentation/SubmittingPatches` 文件中。比如有些行尾空格引起的差别，可以使用 `git diff --check` 来检查；以及尽量使得每个提交最小化地改动一个功能；简要精准地描述提交的内容，尽量不超过50个字符（25个汉字），然后空一行再写详细的描述。

### 私有小型团队
仓库是闭源的，每个人都有推送权限。每个人要进行开发，先从仓库克隆出一个本地版本，完成特性分支上的开发以后并入主分支，然后推送到仓库；如果已经有人向主分支推送了，那么先拉取并本地合并。

### 私有管理团队
仓库是闭源的，有一个管理者专门负责主分支的维护。其他人在特性分支上推送，然后通知管理者审查并合并到主分支里面。每个特性分支上可能有多个合作者。

### 派生的公开项目
一般的开源项目放在公共代码托管平台，没有直接推送的权限，所以常常先 `fork` 出一个派生仓库，完成开发后推送到这个仓库，然后发起一个 `pull request` ，请求原仓库的维护者合并。如果在 `GitHub` 上，可以直接在网页上发起PR；或者也可以使用命令行：
```bash
# 发送邮件给维护者，并附带一些信息
git request-pull origin/master myfork
```

### 使用邮件的公开项目
有些项目需要使用邮件来发送补丁：
```bash
# 生成带补丁的邮件（-M表示查找重命名）
git format-patch -M origin/master
```
可以人为地编辑补丁的内容，在 `---` 和第一个 `diff` 之间的内容不会影响补丁的应用。<br>
可以直接使用 `Git` 命令行借助 `IMAP` 来发送邮件，首先需要配置 `~/.gitconfig` ：
```
[imap]
  folder = "[Gmail]/Drafts"
  host = imaps://imap.gmail.com
  user = user@gmail.com
  pass = p4ssw0rd
  port = 993
  sslverify = false
```
完成配置以后可以使用以下命令发送邮件：
```bash
cat *.patch | git imap-send
```
也可以使用 `SMTP` 来发送邮件，首先需要配置 `~/.gitconfig` ：
```
[sendemail]
  smtpencryption = tls
  smtpserver = smtp.gmail.com
  smtpuser = user@gmail.com
  smtpserverport = 587
```
然后使用以下命令发送邮件：
```bash
git send-email *.patch
```

## 维护项目
作为项目的管理者。

### 仅在特性分支上开发
尽量将新东西都局限在特性分支上，这样可以保证主分支的稳定性。可以考虑为分支附带上命名空间，比如 `<developer>/<name>` 。可以如下创建特性分支：
```bash
# 从master分支创建特性分支
git branch sc/ruby_client master

# 使用checkout
git checkout -b sc/ruby_client master
```

### 应用来自邮件的补丁

#### apply命令
如果补丁是使用 `git diff` 和 `diff` 指令生成的补丁，可以使用 `git apply` 命令来应用补丁：
```bash
# 应用补丁
git apply <patch>

# 检查补丁是否已经应用
git apply --check <patch>
```
相比于使用 `patch -p1` 命令，它允许更少的模糊匹配，能够处理 `git diff` 描述的添加、删除和重命名操作。最关键的是，它是原子的，如果应用失败则会撤销所有的修改。

#### am命令
相比于 `diff` ，使用 `format-patch` 生成的补丁则包含了作者信息、提交信息等，更好理解，可以使用 `git am` 命令来应用补丁：
```bash
# 应用补丁
git am <patch>
```
相较于 `git apply` ，它会自动创建一个提交。<br>
当 `am` 应用失败的时候，它会标记冲突的文件，然后停下来询问用户如何处理。此时可以：
```bash
# 解决所有的冲突，手动git add冲突的文件，继续应用补丁
git am --resolved

# 跳过当前的补丁
git am --skip

# 放弃应用补丁，撤销所有的修改
git am --abort
```
如果创建补丁的那个提交在本地的版本库内，可以让 `am` 指令尝试进行三方合并：
```bash
git am -3 <patch>
```
在使用多个补丁的时候，可以进入交互模式，每个补丁都会询问是否需要应用：
```bash
git am -i <patch>
```

### 检出远程分支
可能别的开发者会发来一个邮件，包含他自己版本库的地址，这时候可以在本地获取并合并他的修改：
```bash
# 添加远程仓库
git remote add <remote> <url>

# 拉取远程分支
git fetch <remote>

# 创建本地跟踪分支
git checkout -b <branch> <remote>/<branch>
```
这比较适合长期合作的情况，由于获得了提交历史， `Git` 也可以自动地进行三方合并。如果是临时合作，可以使用 `pull` 命令：
```bash
# 一次性拉取并合并
git pull <url>
```

### 确定引入了哪些东西
先查看特性分支做了什么：
```bash
# 查看其不在master分支上的提交
git log <branch> --not master

# 查看其相比于master分支的差异

# 麻烦一些地，手工找到公共祖先
git merge-base <branch> master
git diff <output-of-last-commmand>

# 更便捷地
git diff master...<branch>
```

### 将贡献的工作整合进来
- 最简单的方法是只维护一个主分支，所有的特性分支都直接并入主分支。
- 更稳定的方法是维护一个开发分支，所有的特性分支都并入开发分支，在开发分支足够稳定以后，再 `fast-forward` 主分支并打上版本标签。
- 大型的项目有4个长期分支
    - master：主分支
    - next：完成开发且安全的特性会被并入这个分支，随后会被并入master分支
    - pu：包含需要更多工作的特性，稳定后并入master分支
    - maint：上一个稳定版本，并添加一些bug修复的补丁

整合的时候，可以选择直接把特性分支 `rebase` 进来，也可以遴选：
```bash
# 提取某个提交的补丁，作用于当前分支，形成一个新的提交
git cherry-pick <commit-hash>
```
进行反复的 `merge` 和 `rebase` ，可能会遇到大量的冲突，可以使用 `Git` 提供的重用已记录的冲突解决方案（reuse recorded resolution）来简化冲突的处理：
```bash
# 启用自动rerere
git config --global rerere.enabled true

# 手动rerere
git rerere
```

### 为发布打标签
发布一个版本的时候可能需要打上标签：
```bash
# 打标签并签名
git tag -s v1.5 -m 'my signed 1.5 tag'
```
签名的时候可能会需要分发PGP公钥，这个公钥可以直接放进版本库中：
```bash
# 先查看需要的公钥
gpg --list-keys

# 然后把公钥导出到版本库中生成一个hash-object，获得对应的hash值
gpg -a --export <key-hash> | git hash-object -w --stdin

# 可以创建一个标签来指向这个hash-object
git tag -a maintainer-pgp-pub <output-of-last-command>
```
当别人需要校验签名的时候，可以使用以下命令把PGP公钥导入本地的 `gpg` 中：
```bash
git show maintainer-pgp-pub | gpg --import
```

### 生成一个构建号
可能提交的时候会想要一个构建版本号，以下的指令可以提供参考：
```bash
git describe master
```
它给出以下形式的字符串：
```
<最近的标签名> - <该标签之后的提交数> - <该提交的部分哈希值>
```

### 准备一次发布
有些人不使用 `Git` 来获取源码，他们可能需要一个压缩包：
```bash
# 生成一个归档的gzip压缩包
git archive master --prefix='project/' | gzip > `git describe master`.tar.gz

# zip格式
git archive master --prefix='project/' --format=zip > `git describe master`.zip
```

### 制作提交简报
```bash
# 生成一个changelog文档对给定范围内的提交进行总结
git shortlog --no-merges master --not v1.0.1
```

<br><br>

# GitHub
仅记录一些重要的内容。

## 处理PR
```bash
# ls-remote可以看到所有的远程分支，包括PR的分支
git ls-remote origin

# 拉取PR的分支
git fetch origin refs/pull/<PR#>/head
```
如果需要一次性拉取所有的PR分支，需要修改 `.git/config` 文件：
```
[remote "origin"]
    fetch = +refs/heads/*:refs/remotes/origin/*
    fetch = +refs/pull/*/head:refs/remotes/origin/pr/*
```
下面新增的那行会把远程 `refs/pull` 下的所有分支都拉取到本地的 `refs/remotes/origin/pr` 下面。然后就可以在本地以 `pr/<PR#>` 的形式访问PR分支了。

## README文件
可以是 `README` 、 `README.md` 、 `README.asciidoc` ，一般包含以下内容：
- 项目的作用
- 如何配置和安装
- 关于如何使用和运行的例子
- 项目的许可证
- 如何向项目贡献力量

## CONTRIBUTING文件
允许是任意扩展名的，在别人发起PR的时候会自动显示出来，一般描述贡献的规则等等。

## 脚本GitHub
可以对 `GitHub` 的功能进行一些DIY，这需要借助 `GitHub` 提供的钩子系统和API接口。

### 服务
可以订阅 `GitHub` 自己提供的一些服务，比如当有人向仓库推送的时候就向指定的邮箱发送一封邮件等等。

### 钩子
利用钩子可以做一些没有预先提供服务的工作，实际上 `GitHub` 在钩子的触发条件被满足的时候向指定的URL发送一个HTTP请求。

### API
`GitHub` 提供了相当丰富的API，几乎可以做到在网页端能够做到的任何事情。比如：
```bash
# 获取一个用户的相关信息
curl https://api.github.com/users/<user>

# 获取Java项目的.gitignore文件模板
curl https://api.github.com/gitignore/templates/Java

# 向一个issue发表评论
curl \
-H "Content-Type: application/json" \
-H "Authorization: token <token>" \
--data '{"body":"A new comment, :+1:"}' \
https://api.github.com/repos/<user>/<repo>/issues/<issue#>/comments
```

### PR的状态
每一个PR可以附加多个相关的状态，这可以用于帮助审查代码是否符合某些标准，可以通过web服务来实现：
```ruby
require 'httparty'
require 'sinatra'
require 'json'
post '/payload' do
  push = JSON.parse(request.body.read) # parse the JSON
  repo_name = push['repository']['full_name']
  # look through each commit message
  push["commits"].each do |commit|
    # look for a Signed-off-by string
    if /Signed-off-by/.match commit['message']
      state = 'success'
      description = 'Successfully signed off!'
    else
      state = 'failure'
      description = 'No signoff found.'
    end
    # post status to GitHub
    sha = commit["id"]
    status_url =
"https://api.github.com/repos/#{repo_name}/statuses/#{sha}"
    status = {
      "state" => state,
      "description" => description,
      "target_url" => "http://example.com/how-to-signoff",
      "context" => "validate/signoff"
    }
    HTTParty.post(status_url,
      :body => status.to_json,
      :headers => {
        'Content-Type' => 'application/json',
        'User-Agent' => 'tonychacon/signoff',
        'Authorization' => "token #{ENV['TOKEN']}" }
    )
  end
end
```
以上代码会审查提交信息中是否有 `Signed-off-by` 字符串，如果有则会在PR页面上显示一个绿色的勾，否则显示一个红色的叉。

### Octokit
上面的例子中使用 `curl` 命令来调用API，实际上也有相关的开源库，比如 `Octokit` ，可以访问[官方网址](https://github.com/octokit)了解更多。

<br><br>

# Git工具
介绍一些比较少用的，但功能强大的小工具。

## 选择提交
许多 `Git` 命令需要指定一个或若干个提交，这有几种不同的方式。

### 单个提交
可以通过提供单个提交的完整哈希值来指定一个提交，但还有些更方便的方式。

#### 简短哈希值
可以只提供哈希值的前几位，要求不少于4位并且是无歧义的。这可以通过命令查看：
```bash
git log --abbrev-commit --pretty=oneline
```
默认会显示7位的哈希值，当出现歧义的情况下， `Git` 会自动增加一些位数。

#### 分支引用
每个分支实际上都指向一个确定的提交，可以用分支名来指定这个提交。 `Git` 提供了一个底层指令来确定一个分支指向的提交：
```bash
git rev-parse <branch>
```

#### 引用日志
`Git` 在后台维护了一个日志，记录了最近几个月来 `HEAD` 和各个分支指向过的提交：
```bash
# 查看引用日志
git reflog

# 查看HEAD指向的前第5个提交
git show HEAD@{5}

# 查看master分支昨天指向的提交
git show master@{yesterday}

# 查看master分支两个月以前的提交
git show master@{2.months.ago}

# 显示引用日志的各个提交的详细信息
git log -g <branch>
```

#### 祖先引用
可以根据 `HEAD` 或者某个分支向前追溯若干个提交，比如：
```bash
# 查看HEAD所在提交的父提交
git show HEAD^
git show HEAD~

# 查看某个提交的父提交
git show <commit-hash>^

# 如果HEAD指向一个merge得到的提交，默认情况下HEAD^指向合并时所在的分支的父提交，HEAD^2指向另一个分支的父提交
git show HEAD^2

# HEAD指向提交之前的第n个提交
git show HEAD~n

# 可以连续混合使用以上两个符号
git show HEAD~3^2
git show HEAD~5^^^
```

### 提交区间
常常需要局限操作涉及分支的范围，比如：
```bash
# 在develop分支上，不在master分支上的提交
git log master..develop
git log ^master develop
git log develop --not master

# 相比之下双点语法没有那么灵活，因为有时可能需要超过两个分支
git log b1 b2 ^b3
git log b1 b2 --not b3

# 查看两分支上不同的提交
git log master...develop

# 可以显示每个提交具体在哪个分支上
git log --left-right master...develop
```

## 交互式暂存
有时候一次修改涉及了多个功能，可能希望把关于每个功能的修改拆分成单个提交，这时候可以使用交互式暂存：
```bash
# 交互式暂存
git add -i
```
这会进入一个交互式的命令行，提供了若干的交互指令来完成暂存的工作。其中的 `patch` 指令可以对单个文件的不同部分进行拆分暂存，这同样可以通过以下命令完成：
```bash
git add -p
git add --patch
```
相反地，如果需要部分取消暂存，可以：
```bash
git reset --patch
```
还有部分检出文件和部分储藏文件的指令：
```bash
git checkout --patch
git stash save --patch
```

## 储藏与清理
如果需要切换到其他分支，并且在当前分支上的工作进行到一半，又不想创建一个提交，可以使用储藏。

### 储藏工作
```bash
# 储存当前修改到一个栈上，还原工作区
git stash
git stash save

# 查看储藏的工作
git stash list

# 还原最近储藏的工作，但是不暂存之前暂存的文件
git stash apply

# 还原上次储藏的工作，同上
git stash apply stash@{1}

# 还原上次储藏的工作，并恢复暂存情况
git stash apply --index

# 删除某个储藏记录
git stash drop stash@{1}

# 应用储藏并删除
git stash pop
```

### 创造性的储藏
在进行储藏的时候，可以不储藏已经暂存的文件：