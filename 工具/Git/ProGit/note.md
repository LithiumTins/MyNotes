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
# 储存当前修改到一个栈上，还原工作区（不储藏未跟踪的文件）
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
```bash
# 不储藏已经暂存的文件
git stash --keep-index

# 同时储藏未跟踪的文件
git stash --include-untracked
git stash -u

# 交互式储藏
git stash --patch
```

### 从储藏创建一个分支
```bash
# 从创建储藏的提交处，创建一个新的分支并应用储藏，把储藏从栈上删除
git stash branch <branch-name>
```

### 清理工作目录
默认情况下 `git clean` 会忽略 `.gitignore` 文件中指定的文件
```bash
# 清除工作目录中未跟踪的文件
git clean

# 确定删除
git clean -f

# 连同空的子目录
git clean -f -d

# 测试指令将会删除哪些文件
git clean -d -n

# 连同忽略的文件
git clean -f -x

# 交互式
git clean -i
```

## 签署工作

### GPG介绍
通过GPG可以完成一个签署和验证的工作
```bash
# 生成一个GPG密钥
gpg --gen-key

# 查看密钥
gpg --list-keys

# 配置Git使用GPG密钥
git config --global user.signingkey <key-id>
```

### 签署标签
```bash
git tag -s v1.5 -m 'my signed 1.5 tag'
```

### 验证标签
```bash
git tag -v v1.5
```

### 签署提交
```bash
# 签署将要创建的提交
git commit -a -m -S 'signed commit'

# 查看提交的签名
git log --show-signature

# 格式化输出签名情况（%G?）
git log --pretty=format:'%h %G? %aN <%aE>'

# 拒绝包含未签名提交的分支
git merge --verify-signatures <branch>

# 签署合并提交（-S）
git merge --verify-signatures -S <branch>
```

### 每个人必须签署
如果需要把签名引入工作流中，要求团队中的每个人都进行签名工作，否则会非常麻烦，所以需要考虑清除是否需要这些。

## 搜索
有时候需要快速查找一个函数是在哪里调用或者定义的，或者某个函数的变更历史。

### Git Grep
这个命令可以从提交历史或工作目录中查找一个字符串或正则表达式：
```bash
# 查找一个字符串
git grep <string>

# 输出行号
git grep -n <string>

# 查看包含字符串的文件，以及其中包含的数量
git grep --count <string>

# 查看匹配的位置属于哪个函数
git grep -p <string>

# 查找同时包含两个字符串的文件
git grep -e <string1> --and -e <string2>

# 控制格式
git grep --break --heading <string>
```

### Git日志搜索
有时候可能更关心某个目标是在哪个提交中引入的。
```bash
# 查看常量CONST_VALUE是什么时候引入或移除的（-S）
git log -SCONST_VALUE

# 利用-G可以进行正则表达式搜索
```

### 行日志搜索
可以展现一行或一个函数的历史变更。
```bash
# 查看函数foo的变更历史
git log -L :foo:<file>

# 也可以使用正则表达式
git log -L <regex>:<file>
```

## 重写历史
即使是已经进入提交树的提交，也可以通过重写历史来修改。

### 修改最后一次提交
这会改变提交的检验和。
```bash
git commit --amend
```

### 修改多个提交信息
由于Git并没有提供改变历史的工具，这需要通过变基来完成。变基会重写涉及到的所有提交，所以不要对已经推送的分支使用。
```bash
# 修改最近三次提交的信息（传入的是要修改的提交的父提交）
git rebase -i HEAD~3
```
展示出的提交顺序与git log相反（最先的提交在最上面）。如果需要修改某一个提交，可以把 `pick` 改成 `edit` ，然后保存退出，可以：
```bash
# 修改提交信息
git commit --amend

# 继续变基
git rebase --continue
```

### 重新排序提交
调整提交行的顺序，或者删除掉某些行， `Git` 会从上到下依次应用这些提交的补丁。

### 压缩提交
把一个提交的 `pick` 改成 `squash` ，会把它压缩到上一个提交，然后会进入编辑模式， `Git` 自动生成了压缩的相关描述，可以修改。

### 拆分提交
这同样通过 `git rebase -i` 来实现，把需要拆分的提交的 `pick` 改成 `edit` 。退出以后，提交应用至该提交时，应用后会停下来，可以：
```bash
# 撤销该提交和暂存情况
git reset HEAD^

# 多次暂存并提交

# 继续变基
git rebase --continue
```

### filter-branch
`filter-branch` 可以按照一定的方式对整个提交历史进行重写，由于这个指令非常危险，可以新建一个测试分支并在这个分支上测试，然后硬重置主分支。

#### 从每一个提交移除一个文件
```bash
# --tree-filter检出每一个提交，执行指定的命令，然后重新提交
git filter-branch --tree-filter 'rm -f passwords.txt' HEAD

# 作用到所有分支可以传递--all
```

#### 使一个子目录作为新的根目录
`Git` 会自动移除所有不影响子目录的提交。
```bash
git filter-branch --subdirectory-filter <directory> HEAD
```

#### 全局修改邮箱地址
```bash
git filter-branch --commit-filter '
        if [ "$GIT_AUTHOR_EMAIL" = "schacon@localhost" ];
        then
                GIT_AUTHOR_NAME="Scott Chacon";
                GIT_AUTHOR_EMAIL="schacon@example.com";
                git commit-tree "$@";
        else
                git commit-tree "$@";
        fi' HEAD
```

## 重置揭密
可以把 `Git` 的行为理解为它维护了三棵树，分别是：
- HEAD：上一次提交的快照形成的树
- Index：预计的下一次提交的快照形成的树
- 工作目录：被展开到文件系统中的树

### Git Reset
它完成三个基本的动作：

#### 移动HEAD
移动HEAD以及其指向的分支到目标位置。如果指定的是：
```bash
git reset --soft <commit>
```
那么它的工作就已经完成了，即不会改变索引和工作目录。

#### 更新索引
如果指定的是：
```bash
git reset --mixed <commit>
```
那么它还会同步地把索引也设置成该提交的快照。（实际上在不加任何选项的时候，这就是默认的行为）

#### 更新工作目录
如果指定的是：
```bash
git reset --hard <commit>
```
那么它还会把工作目录也一并同步，这是一个危险的操作，覆盖掉的文件无法找回。

### 通过路径来重置
可以指定一个作用路径来限制重置的范围：
```bash
git reset file.txt
```
它把HEAD中的 `file.txt` 的快照复制到索引中，这相当于取消暂存。也可以指定一个特定的提交：
```bash
git reset <commit> -- file.txt
```
这不会影响HEAD，但会把该提交中的 `file.txt` 复制到索引中。

### 压缩提交
假设现在新增了两个提交，但是前一个提交是不完整的，希望压缩掉这个提交，可以：
```bash
# 先把HEAD和分支还原到上上个提交
git reset --soft HEAD~2

# 此时暂存区没有改变，保存了接下来两个提交依次作用后的结果，可以直接提交
git commit
```

### 检出
`checkout` 或多或少跟 `reset` 有些相似之处，但也存在区别。

#### 不带路径
以下两条指令非常相似：
```bash
git checkout <branch>
git reset --hard <branch>
```
它们都会尝试覆盖三个树，区别在于：
- 如果有未提交的修改， `checkout` 会失败
- `checkout` 并不移动所在的分支

#### 带路径
允许指定一个文件路径进行检出：
```bash
git checkout <branch> <file>
```
它不会移动HEAD，只是把对应提交中的<file>取出来放到索引和工作目录中。

## 高级合并
有时候可能会遇上一些棘手的合并冲突，这时候可以使用一些高级的工具来解决。

### 合并冲突
在进行一次可能有冲突的合并之前，尽量保持工作目录是干净的，这样可以避免工作的丢失。

#### 中断一次合并
如果遇到了冲突并且不想处理，可以停止合并：
```bash
git merge --abort
```

#### 忽略空白
如果合并冲突是由于一些空白引起的，可以忽略空白：
```bash
# 忽略已存在的空白的修改
git merge -Xignore-all-space <branch>

# 忽略所有空白的修改
git merge -Xignore-space-change <branch>
```

#### 手动修改再合并
当发生冲突的时候，对于一个特定的文件，有三个版本：
```bash
# 共同祖先的版本
git show :1:<filename> > <filename>.common

# HEAD指向的版本
git show :2:<filename> > <filename>.ours

# 要合并的版本
git show :3:<filename> > <filename>.theirs

# 可以通过以下命令来查看它们的哈希值
git ls-files -u
```
手动修改文件以后可以合并它们：
```bash
git merge-file -p <filename>.ours <filename>.common <filename>.theirs > <outputfile>
```
完成这些工作以后可以查看发生的改变：
```bash
# 跟自己分支的不同
git diff --ours <filename>

# 跟对方分支的不同（忽略空白）
git diff --theirs -b <filename>
```
结束后可以清理掉中间文件：
```bash
git clean -f
```

#### 检出冲突
当遇见冲突的时候，可以获取更多的信息：
```bash
# 除了ours和theirs以外，也在冲突的文件中显示base
git checkout --conflict=diff3 hello.rb

# 也可以配置为默认行为
git config --global merge.conflictstyle diff3
```

#### 合并日志
查看两个分支涉及到冲突部分的提交历史：
```bash
git log --merge --oneline --left-right <branch1>...<branch2>
```

#### 组合式差异格式
在合并的时候，没有冲突的文件会被暂存，此时运行 `git diff` 将会得到冲突文件和暂存区的差异，对于每一个差异行，可能会有两个加号，左边的表示相比于ours新增的行，右边的表示相比于theirs新增的行。<br>
对于一个合并提交运行 `git show` 也可以看到类似的信息，或者是运行 `git log -p --cc` 。

### 撤销合并
如果在本地进行了一次合并，但想要取消它，可以有两种处理方式。

#### 修复引用
简单地把分支移回到上一个提交即可：
```bash
git reset --hard HEAD^
```

#### 还原提交
通过创建一个新的提交去除合并的影响：
```bash
git revert -m 1 <commit>
```
然而此时如果再次合并之前的分支， `Git` 不会引入任何更改，因为之前的合并提交还在。如果确定需要再次合并，可以用同样的方法撤销前面的撤销。

### 其他类型的合并
有一些可选择的、特殊的合并策略。

#### 偏好一方
```bash
# 当遇到冲突的时候，选择当前分支的版本
git merge -Xours <branch>

# 当遇到冲突的时候，选择对方分支的版本
git merge -Xtheirs <branch>

# 也可以使用另一个指令
git merge-file --ours

# 完全忽略另一个分支
git merge -s ours <branch>
```

#### 子树合并
可以有两个项目，其中一个作为另一个的子项目，映射到其中某个子目录中。如果需要建立这样的结构，需要：
```bash
# 把子项目添加为一个远程仓库
git remote add subproject <url>

# 拉取子项目到本地
git fetch subproject

# 设置一个分支跟踪子项目的master分支
git checkout -b subproject-master subproject/master
```
接下来可以把子项目作为一个子目录：
```bash
# 先切换到主项目的master分支
git checkout master

# 把子项目的master分支读到subproject/目录下
git read-tree --prefix=subproject/ -u subproject-master
```
如果需要更新子项目，可以：
```bash
# 切换到子项目
git checkout subproject-master

# 拉取更新
git pull

# 切换到主项目
git checkout master

# 合并子项目
git merge --squash -s recursive -Xsubtree=subproject subproject-master
```
相比于子项目，这是一种更加复杂的方式，如果需要查看主项目中的子项目和子项目本身的差异，需要：
```bash
git diff-tree -p subproject-master
```

## Rerere
开启 `rerere` 功能：
```bash
git config --global rerere.enabled true
```
或者在特定仓库中创建 `.git/rr-cache` 目录。

## 使用Git调试
`Git` 提供了两个工具来帮助调试。

### 文件标注
如果有一段代码有问题，想知道是谁引入的，可以使用 `git blame` ：
```bash
# 用-L限制范围
git blame -L 12,22 simplegit.rb

# 用-C选项可以跟踪文件的复制和移动
git blame -C -L 12,22 simplegit.rb
```

### 二分查找
当代码出现了问题，但是已经难以追溯是哪个提交中引入的，可以使用二分查找：
```bash
# 启动二分查找
git bisect start

# 当前提交是有问题的
git bisect bad

# 1.0版本是没有问题的
git bisect good v1.0

# 当前提交是没有问题的
git bisect good

# 把HEAD重置到最初的提交
git bisect reset

# 可以实现一个自动测试脚本，正常时返回0，否则返回非0
git bisect start HEAD v1.0
git bisect run test-error.sh
```

## 子模块

### 开始使用子模块
```bash
# 把另一个项目添加为子模块，它会被自动clone下来
git submodule add https://github.com/chaconinc/DbConnector

# 可以定制存放的目录
git submodule add https://github.com/chaconinc/DbConnector <dir>
```
然后仓库中会有一个 `.gitmodules` 文件，保存了子项目的映射情况。其中的url映射是可以修改的：
```bash
git config submodule.DbConnector.url <url>
```

### 克隆含有子模块的项目
主项目克隆下来后会有子模块所在的子目录，但此时子目录是空的，需要：
```bash
# 初始化本地配置文件
git submodule init

# 拉取子项目数据并检出合适的提交
git submodule update
```
或者克隆主项目的时候加上：
```bash
git clone --recursive <url>
```

### 在包含子模块的项目上工作
假设现在有一个项目包含子模块，需要同时在主项目和子项目上和其他人协作。

#### 拉取上游修改
可以在子项目中手动拉取上游修改：
```bash
cd DbConnector
git fetch
git merge origin/master
```
回到上游后可以查看到子项目的变更：
```bash
# 手动查看
git diff --submodule

# 把查看子项目作为默认行为
git config --global diff.submodule log
```
这时提交的话就会更新主项目中子项目的版本。也可以在主项目中直接更新子项目：
```bash
git submodule update --remote DbConnector
```
以上命令默认更新子模块的 `master` 分支，可以通过以下命令修改：
```bash
git config -f .gitmodules submodule.DbConnector.branch <branch>
```
如果子模块发生了修改，经过配置以后可以通过 `git status` 查看：
```bash
git config status.submodulesummary 1
```

#### 在子模块上工作
```bash
# 进入子模块目录
cd DbConnector

# 检出一个分支
git checkout stable

# 回到主项目
cd ..

# 更新子项目，由于已经检出分支，拉取到的分支会自动合并
git submodule update --remote --merge

# 此时对子项目做一些修改
cd DbConnector
touch file
git commit -am 'added a new file'
cd ..

# 假设有人对子项目做了更改
git submodule update --remote --rebase
```

#### 发布子模块改动
如果子模块发生了更新，并且只推送了主模块，那么其他人无法获取子模块的正确内容，在推送的时候可以加以检查：
```bash
# 保证子模块已经被推送
git push --recurse-submodules=check

# 当子模块没有推送的时候，自动推送到远程仓库
git push --recurse-submodules=on-demand
```

#### 合并子模块改动
如果子模块出现了分叉， `Git` 一般不会主动合并，需要手动合并：
```bash
# 查看对方的提交哈希值
git diff

# 进入子模块目录
cd DbConnector

# 查看当前所在的公共提交
git rev-parse HEAD

# 为对方提交创建一个分支并合并
git branch try-merge <other-commit>
git merge try-merge

# 完成合并后回到主项目提交
cd ..
git add DbConnector
git commit -m 'merge submodule'
```
有时可能存在子模块的某个提交同时包含了需要合并的两个提交， `Git` 找到这个提交后会告知，这时候可以：
```bash
git merge <merged-commit>
```

### 子模块技巧

#### 子模块遍历
```bash
# 在所有子模块中储存进度
git submodule foreach 'git stash'

# 把所有子模块切换到新特性分支
git submodule foreach 'git checkout -b new-feature'

# 查看整个项目的差异
git diff; git submodule foreach 'git diff'
```

#### 有用的别名
```bash
git config alias.sdiff '!'"git diff && git submodule foreach 'git diff'"
git config alias.spush 'push --recurse-submodules=on-demand'
git config alias.supdate 'submodule update --remote --merge'
```

### 子模块的问题
如果一个分支上引入了一个子模块，此时切换到另一个没有这个模块的分支，这个目录不会被删除。如果删除了这个目录，回到原来的分支时，又需要重新初始化子模块。<br>
如果有一个子目录，在一个分支中把它转换成了子模块，切换回仍为子目录的分支时，会得到错误，需要强制切换：
```bash
git checkout -f <branch>
```
切换回来后目录是空的，需要找回文件：
```bash
git checkout .
```

## 打包
可以把 `Git` 的数据打包成一个二进制文件，然后通过其他方式传输：
```bash
# 创建一个包含master分支所有数据的文件（包含HEAD指示克隆后需要检出的分支）
git bundle create repo.bundle HEAD master

# 可以从这个文件克隆出一个仓库
git clone repo.bundle repo

# 指定检出的分支
git clone repo.bundle -b master repo
```
为了缩小文件的体积，需要手动指定打包的范围：
```bash
# 先用git log查看哪些提交是新发生的更改，如
git log --oneline master ^origin/master

# 然后打包这些提交
git bundle create commits.bundle master ^9a466c5

# 可以查看包里的是否合法，是否和仓库具有相同的祖先
git bundle verify commits.bundle

# 也可以查看包里有哪些分支
git bundle list-heads commits.bundle

# 把分支从包中提取出来
git fetch commits.bundle master:newbranch
```

## 替换
可以指定 `Git` 当遇到某些提交时，使用另一个提交来替换它。比如进行新老仓库的连接，如果新仓库的提交<commit1>和老仓库的提交<commit2>是首个等价的提交，那么可以：
```bash
git replace <commit1> <commit2>
```
这样两条提交历史就连接起来了。

## 凭证存储
如果使用HTTP协议，那么每次连接都需要输入用户名密码，不过 `Git` 允许一些缓存机制：
- 默认：所有都不缓存
- cache：将凭证放在内存中15分钟
- store：将凭证明文存储在home目录下
- osxkeychain：Mac特有，使用钥匙串存储凭证，加密存储在磁盘上
- winstore：Windows特有，使用凭证管理器存储凭证

可以通过以下命令来配置：
```bash
# 配置凭证存储模式
git config --global credential.helper <mode>

# 自定义store模式的存储位置
git config --global credential.helper store --file <file>

# 配置store模式保存时间（秒）
git config --global credential.helper cache --timeout 3600
```
也可以配置到 `.gitconfig` 文件中，可以同时有多个helper：
```
[credential]
    helper = store --file /mnt/thumbdrive/.git-credentials
    helper = cache --timeout 30000
```

### 底层实现
底层的指令是 `git credential` 。

### 自定义凭证缓存
因为凭证缓存程序和 `Git` 是分离的，所以可以自行提供缓存凭证程序，甚至自己实现，需要保证：
- `get` 的行为正常
- 凭证文件格式和 `git-credential-store` 使用的格式相同
- 一般凭证文件路径是固定的，但应该允许用户传入自定义路径

提供一个 `ruby` 脚本作为例子：
```ruby
#!/usr/bin/env ruby
require 'optparse'
path = File.expand_path '~/.git-credentials'
OptionParser.new do |opts|
    opts.banner = 'USAGE: git-credential-read-only [options] <action>'
    opts.on('-f', '--file PATH', 'Specify path for backing store') do
|argpath|
        path = File.expand_path argpath
    end
end.parse!
exit(0) unless ARGV[0].downcase == 'get'
exit(0) unless File.exists? path
known = {}
while line = STDIN.gets
    break if line.strip == ''
    k,v = line.strip.split '=', 2
    known[k] = v
end
File.readlines(path).each do |fileline|
    prot,user,pass,host =
fileline.scan(/^(.*?):\/\/(.*?):(.*?)@(.*)$/).first
    if prot == known['protocol'] and host == known['host'] then
        puts "protocol=#{prot}"
        puts "host=#{host}"
        puts "username=#{user}"
        puts "password=#{pass}"
        exit(0)
    end
end
```
把这个工具保存为 `git-credential-read-only` ，然后放进 `PATH` 中，就可以使用了：
```bash
git credential-read-only --file=/mnt/shared/creds get

git config --global credential.helper read-only --file /mnt/shared/creds
```

<br><br>

# 自定义Git

## 配置Git

### 客户端基本配置
```bash
# Git会根据环境变量$VISUAL和$EDITOR来调用文本编辑器，或者使用vi
# 可以手动配置
git config --global core.editor emacs

# 设置默认提交信息，从~/.gitmessage.txt中读取
git config --global commit.template ~/.gitmessage.txt

# 设置log和diff等命令显示的分页器
git config --global core.pager 'more'
# 关闭该选项
git config --global core.pager ''

# 设置签名使用的GPG密钥
git config --global user.signingkey <key-id>

# 设置全局的忽略文件
git config --global core.excludesfile ~/.gitignore

# 设置自动纠错
git config --global help.autocorrect 1
```

### Git中的着色
```bash
# 是否启用着色
git config --global color.ui [always|auto|false]

# 着色哪些部分
git config --global color.branch [true|false|always]
git config --global color.diff [true|false|always]
git config --global color.interactive [true|false|always]
git config --global color.status [true|false|always]

# 可以在子选项覆盖掉父选项的行为
git config --global color.diff.meta "blue black bold"
```

### 外部的合并与比较工具
有一个图形化的软件P4Merge，可以配置为 `Git` 的合并工具。先创建一个脚本包装 `merge` 命令，向 `/usr/local/bin/extMerge` 中写入：
```bash
#!/bin/sh
path/to/p4merge $*
```
然后创建一个脚本包装 `diff` 命令，向 `/usr/local/bin/extDiff` 中写入：
```bash
#!/bin/sh
[ $# -eq 7 ] && path/to/p4merge "$2" "$5“
```
把它们都赋予可执行权限：
```bash
sudo chmod +x /usr/local/bin/extMerge
sudo chmod +x /usr/local/bin/extDiff
```
然后配置 `Git` 使用它们：
```bash
# 使用extMerge作为合并工具
git config --global merge.tool extMerge

# 设置extMerge的调用方式
git config --global mergetool.extMerge.cmd 'extMerge "$BASE" "$LOCAL" "$REMOTE" "$MERGED"'

# 返回值不代表合并的结果
git config --global mergetool.extMerge.trustExitCode false

# 使用extDiff作为比较工具
git config --global diff.external extDiff
```
或者向 `~/.gitconfig` 中写入：
```
[merge]
  tool = extMerge
[mergetool "extMerge"]
  cmd = extMerge "$BASE" "$LOCAL" "$REMOTE" "$MERGED"
  trustExitCode = false
[diff]
  external = extDiff
```
实际上 `Git` 预置了很多合并工具，可以通过以下命令查看：
```bash
git mergetool --tool-help
```

### 格式化与多余的空白字符
```bash
# 提交时把\r\n转换成\n，检出时把\n转换成\r\n
git config --global core.autocrlf true

# 提交时把\r\n转换成\n，检出时不转换
git config --global core.autocrlf input

# 关闭自动转换
git config --global core.autocrlf false

# 打开查找行尾空格（默认）
git config --global core.whitespace blank-at-eol

# 打开查找文件末尾的空白行（默认）
git config --global core.whitespace blank-at-eof

# 打开查找行头tab之前的空格（默认）
git config --global core.whitespace space-before-tab

# 打开查找空格开头的行
git config --global core.whitespace indent-with-non-tab

# 打开查找tab开头的行
git config --global core.whitespace tab-in-indent

# 忽略行尾的回车
git config --global core.whitespace cr-at-eol

# 关闭查找行尾空格
git config --global core.whitespace -blank-at-eol
```
在运行 `git diff` 的时候， `Git` 就可以检测到这些问题并发出警告。在应用补丁的时候，可以附加选项：
```bash
# 对以上配置发出警告
git apply --whitespace=warn <patch>

# 自动修正以上配置
git apply --whitespace=fix <patch>
```
另外， `git rebase` 也可以附加这样的选项。

### 服务器端配置
```bash
# 每次推送时强制检查仓库完整性
git config --global receive.fsckObjects true

# 拒绝强制推送
git config --global receive.denyNonFastForwards true

# 拒绝删除分支
git config --global receive.denyDeletes true
```

## Git属性
允许针对特定的路径来设置配置项，在 `.gitattributes` 文件中，如果不希望提交属性文件，可以在 `.git/info/attributes` 中设置。

### 二进制文件
可以告诉 `Git` 那些是二进制文件、如何比较一些二进制文件。

#### 识别二进制文件
比如 `Mac` 上的 `Xcode` 会有一个 `.pbxproj` 后缀的文件，实际上是一个轻量级的数据库，通常无法通过 `Git` 进行处理，所以希望把它当成二进制文件，在 `.gitattributes` 中设置：
```
*.pbxproj binary
```

#### 比较二进制文件
像 `Microsoft Word` 的文档这样的二进制文件，默认情况下会被当成二进制文件而不会被 `git diff` 比较，但是可以通过配置来比较：
```
*.doc diff=word
```
word比较器是需要自己提供的。首先要安装[docx2txt](http://docx2txt.sourceforge.net/)，按照 `INSTALL` 文件的说明把它放到可执行路径下。然后写一个脚本包装它，在可执行路径下创建一个 `docx2txt` 文件：
```bash
#!/bin/bash
docx2txt.pl $1 -
```
赋予它可执行权限：
```bash
sudo chmod +x /usr/local/bin/docx2txt
```
然后配置 `Git` 使用它：
```bash
git config diff.word.textconv docx2txt
```
甚至也可以通过 `exiftool` 来比较 `.png` 图片：
```bash
echo '*.png diff=exif' >> .gitattributes
git config diff.exif.textconv exiftool
```

### 关键字展开
没看懂

### 导出版本库
编辑 `.gitattributes` 文件，可以在导出版本库时实现一些特殊功能。<br>
- 在导出项目归档时忽略一些文件
  ```
  *.o export-ignore
  ```
- 在导出项目归档时自动向文件注入一些元数据
  ```
  LAST_COMMIT export-subst
  ```
  在 `LAST_COMMIT` 中的 `Git` 注解会自动被替换。

### 合并策略
可以指明在合并冲突时只使用自己的版本。向 `.gitattributes` 中写入：
```
*.txt merge=ours
```
然后配置 `Git` ：
```bash
git config merge.ours.driver true
```

## Git钩子
可以配置的、在特定的事件发生时触发的脚本，叫做钩子。有两类钩子，分别是客户端钩子和服务器端钩子。

### 安装一个钩子
钩子都存储在 `.git/hooks` 目录下，当初始化一个仓库的时候， `Git` 会像其中加入一些以 `.sample` 结尾的实例。脚本文件的名字是重要的。

### 客户端钩子
需要注意的是，克隆一个仓库的时候，钩子并不会被复制。如果需要用钩子实现一些功能，可以用服务器端钩子来实现。或者采用其他的方式分发钩子。

#### 提交工作流钩子
- `pre-commit` ：在提交之前运行，如果脚本返回非0值，提交会被中止。如果执行 `git commit --no-verify` ，那么钩子不会被执行。
- `prepare-commit-msg` ：在默认提交信息已经生成、即将打开编辑器的时候运行。
- `commit-msg` ：在提交信息编辑器关闭以后运行，如果脚本返回非0值，提交会被中止。
- `post-commit` ：在提交完成以后运行。

#### 电子邮件工作流钩子
- `applypatch-msg` ：在应用补丁之前运行，如果脚本返回非0值，应用补丁会被中止。接受参数，包含请求合并信息的临时文件的名字。
- `pre-appplypatch` ：在应用补丁之后、提交产生之前运行，如果脚本返回非0值，应用补丁会被中止。
- `post-applypatch` ：在提交产生之后运行。

#### 其他客户端钩子
- `pre-rebase` ：在变基之前运行，如果脚本返回非0值，变基会被中止。
- `post-rewrite` ：被会修改提交的命令调用。
- `post-checkout` ：在检出之后运行。
- `post-merge` ：在合并之后运行。
- `pre-push` ：在推送之前运行，如果脚本返回非0值，推送会被中止。
- `pre-auto-gc` ：在 `git gc --auto` 之前运行。

### 服务器端钩子
- `pre-receive` ：在接收之前运行，如果脚本返回非0值，推送会被中止。
- `update` ：相比于 `pre-receive` 作用于整个推送，它对于本次推送包含的每个分支都会运行一次。如果返回非0值，只拒绝对应的分支。
- `post-receive` ：在接收完成以后运行。

<br><br>

# Git与其他系统
本章主要考虑的是 `Git` 和其他版本控制系统之间的交互或迁移。

## 作为客户端的Git
可能在团队的其他人使用别的版本控制系统的时候，自己却在使用 `Git` ，这需要通过某种中间的适配器来实现，这被称为“桥接”。

### Git与Subversion
通过命令 `git svn` 来进行桥接，但是有一些注意事项，比如：
- 尽量通过变基来把历史整理成直线
- 不要重写历史然后再次提交
- 不要同时在一个 `Git` 仓库中工作

#### 设置
先完成一些基础的配置工作：
```bash
# 创建一个本地Subversion仓库
mkdir /tmp/test-svn
svnadmin create /tmp/test-svn

# 允许所有用户改变版本属性
echo "#!/bin/sh \n exit 0;" > /tmp/test-svn/hooks/pre-revprop-change
chmod +x /tmp/test-svn/hooks/pre-revprop-change

# 添加远程仓库
svnsync init file:///tmp/test-svn http://progit-example.googlecode.com/svn/

# 同步
svnsync sync file:///tmp/test-svn
```

#### 开始
```bash
# 从svn仓库中克隆出一个Git仓库
git svn clone file:///tmp/test-svn -T trunk -b branches -t tags

# 因为上面三个参数非常常见，可以简写为
git svn clone file:///tmp/test-svn -s
```
这里克隆下来的标签被放在 `refs/remotes/origin/tags` 底下，说明和 `Git` 自己的标签相比，它们更多像是被当做分支来对待。

#### 提交回Subversion
```bash
# 完成一些工作以后，提交
git commit -am 'Adding git-svn instructions to the README'

# 把提交推送回Subversion
git svn dcommit
```
推送完成以后， `Git` 会重写推送上去的提交来包含一个唯一的标识符，这会导致这些提交的哈希值被改变。如果需要同时推送到 `Git` 和 `Subversion` 服务器，建议先推送到 `Subversion` ，然后再推送到 `Git` 。

#### 拉取新改动
如果推送的时候已经有其他人推送了一些改动，那么将会遇到冲突，需要先拉取：
```bash
# 拉取改动并自动变基
git svn rebase

# 只拉取改动
git svn fetch
```

#### Git分支问题
Subversion只有一个线性的提交历史，如果创建一个特性分支，工作完成后合并入主分支，再提交上去的话，提交历史只有主分支上的一个整合了所有修改的提交。

### Subversion分支
Subversion的分支和Git的分支不尽相同，如果可以就不要使用它。

#### 创建一个新的SVN分支
```bash
git svn branch <branch>
```
相当于SVN中的命令：
```bash
svn copy trunk branches/<branch>
```
这并不会自动检出该分支。

#### Subversion命令
```bash
# SVN风格的历史（不会获取服务器的最新信息，同时只显示dcommit到服务器的提交）
git svn log

# 注解
git svn blame <file>

# 忽略Subversion所忽略的文件
git svn create-ignore
git svn show-ignore
```

### Git与Mercurial
如果更喜欢在本地使用 `Git` 却需要在团队中使用 `Mercurial` ，可以使用 `git-remote-hg` 。

#### git-remote-hg
```bash
# 下载
curl -o ~/bin/git-remote-hg https://raw.githubusercontent.com/felipec/git-remote-hg/master/git-remote-hg

# 赋予可执行权限
chmod +x ~/bin/git-remote-hg

# 安装Python依赖
pip install mercurial
```
还需要一个 `Mercurial` 客户端，可以在[官网](http://mercurial.selenic.com/)安装。接下来可以准备一个官方提供的“hello world”仓库：
```bash
# 克隆
git clone http://selenic.com/repo/hello /tmp/hello
```

#### 开始
接下来看一个典型的工作流：
```bash
# 克隆仓库
git clone hg::/tmp/hello /tmp/hello-git

# 进入仓库
cd /tmp/hello-git

# 查看历史
git log --oneline --graph --decorate
```
在这里发现只显示了两个提交，并且一大堆引用都指向了最后一个提交，进一步查看：
```bash
tree .git/refs
```
如果需要忽略一些文件，但是 `.gitignore` 文件又不能被提交，可以使用 `.hgignore` 文件：
```bash
# exclude文件跟.gitignore文件差不多，但是它不会被提交
cp .hgignore .git/info/exclude
```

#### 工作流程
现在完成了一些工作并形成了2个本地的提交：
```bash
# 先看看是否有其他人推送
git fetch

# 查看提交历史
git log --oneline --graph --decorate --all

# Mercurial能够处理合并，所以直接merge就行
git merge origin/master

# 然后推送
git push
```

#### 分支与书签
`Git` 中的分支只有一种，在 `Mercurial` 中被称为 `bookmark` ，即书签。实际上 `Mercurial` 还有另一种分支，称为 `branch` ，它要更重量级一些，在提交产生的时候 `branch` 的名字会被记录在提交中。
```bash
# 创建Mercurial书签
git checkout -b featureA
git push origin featureA
```
在 `Mercurial` 中就可以通过 `hg bookmarks` 来看到这个书签。如果想创建一个 `branch` ，可以：
```bash
# 在branches命名空间中创建一个分支
git checkout -b branches/permanent
```
然后在 `Mercurial` 中可以通过 `hg branches` 来查看。<br>
**值得注意的是，Mercurial并不支持变更历史，尽量避免这样的行为。**

### Git与Perforce
Perforce本身是非常古老的一个版本控制系统，如果可以，尽量把整个项目迁移至Git。如果一定要结合两者一起工作，也有一些办法。

#### Git Fusion
这是Perforce官方制作的桥接工具，可以在[官网](http://www.perforce.com/git-fusion)获取。简单起见，最好下载一个虚拟机来运行Perforce守护进程和Git Fusion，在[官网](http://www.perforce.com/downloads/Perforce/20-User)获得虚拟机镜像。启动虚拟机以后，它会要求你设置三个用户（root、perforce、git）的密码，然后提供一个实例名字。接下来就会看到一个启动屏幕，它给出了一个IP地址。接下来：
```bash
# 创建一个Perforce用户（打开一个vi来自定义用户）
p4 -p localhost:1666 -u super user -f john

# 修改密码
p4 -p localhost:1666 -u john passwd

# 退出会话
exit
```
Git Fusion内置了一个证书，然而域名不匹配虚拟主机的IP地址，如果需要永久安装的话就需要手动安装其他证书。这里简单地让Git不验证证书：
```bash
export GIT_SSL_NO_VERIFY=true
```
此时就可以开始测试：
```bash
# 克隆样例仓库（输入john的用户名和密码）
git clone https://10.0.1.254/Talkhouse
```
可以调整一些配置，借助Git Fusion实际上很简单，这是通过 `p4gf_config` 文件来控制的。**后面看不太懂了**。

#### Git-p4
没看

### Git与TFS
没看

## 迁移到Git
可能会需要把项目从别的版本控制系统迁移到Git。

### Subversion
可以简单地 `git svn clone` 一个仓库并推送到Git服务器上，这样就迁移了代码。如果想要历史，可能需要从SVN服务器一点一点拉取。会有一些问题，比如SVN需要每个人有一个用户，并会把用户信息写到提交中。如果希望把它转换成Git形式的用户信息，需要创建一个 `users.txt` 文件，类似：
```
schacon = Scott Chacon <schacon@geemail.com>
selse = Someo Nelse <selse@geemail.com>
```
这需要作者名字列表，可以通过以下命令获取：
```bash
svn log --xml | grep author | sort -u | perl -pe 's/.*>(.*?)<.*/$1 = /'
```
这把日志输出为XML格式，然后保留作者信息行、去重、去除XML标记。把这样生成的文件提供给 `git svn` ，并告诉Git不要导入元数据：
```bash
git svn clone http://my-project.googlecode.com/svn/ --authors-file=users.txt --no-metadata -s my_project
```
然后要完成一些清理工作：
```bash
# 清除git svn创建的奇怪引用
cp -Rf .git/refs/remotes/origin/tags/* .git/refs/tags/
rm -Rf .git/refs/remotes/origin/tags

# 把refs/remotes里剩下的分支变成本地分支
cp -Rf .git/refs/remotes/* .git/refs/heads/
rm -Rf .git/refs/remotes

# 然后添加远程仓库
git remote add origin <url>

# 上传所有的数据
git push origin --all
```

### Mercurial
利用一个叫做 `hg-fast-export` 的工具，可以很容易地把Mercurial仓库转换成Git仓库。首先获得它：
```bash
git clone http://repo.or.cz/r/fast-export.git /tmp/fast-export
```
然后可以开始转换：
```bash
# 克隆Mercurial仓库
hg clone <url> /tmp/hg-repo

# 建立作者映射
cd /tmp/hg-repo
hg log | grep user: | sort | uniq | sed 's/user: *//' > ../authors

# 作者映射经过Hg-fast-export修改

# 新建一个Git仓库
git init /tmp/converted
cd /tmp/converted

# 进行导出，-r指示Mercurial仓库的位置，-A指示作者映射的位置
/tmp/fast-export/hg-fast-export.sh -r /tmp/hg-repo -A /tmp/authors

# 推送到远程仓库
git remote add origin <url>
git push origin --all
```

### Perforce
还是之前那两种方式。

#### Git Fusion
直接把仓库推送到Git远程仓库就好了。

#### Git-p4
首先设置客户端环境变量：
```bash
export P4PORT=public.perforce.com:1666
```
然后克隆仓库：
```bash
# 项目只有一个分支，如果还有其他分支，传入选项--detect-branches
git-p4 clone //guest/perforce_software/jam@all p4import
```
此时导入的仓库在每个提交信息中都保存了一些git-p4的相关信息。如果想要去除这些信息：
```bash
git filter-branch --msg-filter 'sed -e "/^\[git-p4:/d"'
```
此时可以推送到远程了。

#### TFS
首先映射用户名：
```bash
# 获取所有的历史
tf history $/myproject -recursive > AUTHORS_TMP

# 取出作者信息
cat AUTHORS_TMP | cut -b 11-20 | tail -n+3 | uniq | sort > AUTHORS
```
需要手动把格式整理成这样：
```
DOMAIN\username = User Name <email@address.com>
```
然后进行克隆：
```bash
git tfs clone --with-branches --authors=AUTHORS https://username.visualstudio.com/DefaultCollection $/project/Trunk
project_git
```
同样需要清理提交信息：
```bash
git filter-branch -f --msg-filter 'sed "s/^git-tfs-id:.*$//g"' --
--all
```
此时就可以推送了。

### 一个自定义的导入器
如果使用的是其他的一些系统，可能找不到一个合适的工具，需要自定义，可以借助 `git fast-import` 来实现。接下来尝试实现一个简单地导入器。<br>
考虑在 `current` 目录下工作，并且已经做了多次备份，运行 `ls /opt/import_from` 可以看到目录结构如下：
```
back_2014_01_02
back_2014_01_04
back_2014_01_14
back_2014_02_03
current
```
接下来可以实现脚本了，把输出重定向到 `git fast-import` ，需要注意的是在Windows下不要引入 `\r\n` ，这个命令只接受 `\n` ：
```ruby
#!/usr/bin/env ruby

$stdout.binmode
$author = "John Doe <john@example.com>"

$marks = []
def convert_dir_to_mark(dir)
    if !$marks.include?(dir)
        $marks << dir
    end
    ($marks.index(dir)+1).to_s
end

def convert_dir_to_date(dir)
    if dir == 'current'
        return Time.now().to_i
    else
        dir = dir.gsub('back_', '')
        (year, month, day) = dir.split('_')
        return Time.local(year, month, day).to_i
    end
end

def export_data(string)
    print "data #{string.size}\n#{string}"
end

def inline_data(file, code='M', mode='644')
    content = File.read(file)
    puts "#{code} #{mode} inline #{file}"
    export_data(content)
end

def print_export(dir, last_mark)
    date = convert_dir_to_date(dir)
    mark = convert_dir_to_mark(dir)

    puts 'commit refs/heads/master'
    puts "mark :#{mark}"
    puts "committer #{$author} #{date} -0700"
    export_data("imported from #{dir}")
    puts "from :#{last_mark}" if last_mark

    puts 'deleteall'
    Dir.glob("**/*").each do |file|
        next if !File.file?(file)
        inline_data(file)
    end
    mark
end

# Loop through the directories
last_mark = nil
Dir.chdir(ARGV[0]) do
    Dir.glob("*").each do |dir|
        next if File.file?(dir)

        # move into the target directory
        Dir.chdir(dir) do
            last_mark = print_export(dir, last_mark)
        end
    end
end
```
没有细看，形成的输出大致形式如下：
```
commit refs/heads/master
mark :1
committer John Doe <john@example.com> 1388649600 -0700
data 29
imported from back_2014_01_02deleteall
M 644 inline README.md
data 28
# Hello

This is my readme.
```
然后可以运行：
```bash
# 初始化一个Git仓库
git init

# 运行脚本
ruby import.rb /opt/import_from | git fast-import
```

<br><br>

# Git内部原理

## 底层命令和高层命令
对用户友好的命令称为高层命令，剩下的那些称作底层命令，更加贴近Git的内部实现。当执行 `git init` 的时候，Git会创建一个 `.git` 目录，所有的数据都存储在这里面。它的结构大致如下：
- `HEAD` ：指示目前被检出的分支
- `config*` ：包含项目特有的配置选项
- `description` ：供GitWeb使用的项目描述
- `hooks/` ：客户端或服务器端的钩子脚本
- `info/` ：包含一个全局性排除文件，可以把不希望放在 `.gitignore` 文件中的忽略模式放在这里
- `objects/` ：存储所有数据内容
- `refs/` ：存储指向数据的提交对象的指针

## Git对象
Git是一个**内容寻址文件系统**，这说明Git像是一个简单的键值对数据库，向它插入一个数据，它返回一个键值以方便后续对这个数据的检索。举个例子，当刚刚初始化一个Git仓库的时候，它的 `objects/` 目录是空的，这时可以调用命令插入一个数据：
```bash
# -w表示保存数据，否则只计算哈希值；--stdin表示从标准输入读取数据，否则从提供的文件读取
echo 'test content' | git hash-object -w --stdin
```
Git会根据头部信息和内容共同计算一个哈希值并返回：
```
d670460b4b4aece5915caf5c68d12f560a9fe3e4
```
此时可以找到文件 `.git/objects/d6/70460b4b4aece5915caf5c68d12f560a9fe3e4` ，可以读取它的内容：
```bash
# -p让该命令自动判断内容的类型
git cat-file -p d670460b4b4aece5915caf5c68d12f560a9fe3e4
```
对于存储的文件来说也类似：
```bash
# 创建一个文件
echo 'version 1' > test.txt

# 存储文件
git hash-object -w test.txt

# 然后覆盖文件的内容
echo 'version 2' > test.txt

# 再存储一个版本
git hash-object -w test.txt
```
此时就可以看到文件的两个版本都存储到了 `.git/objects` 目录下，可以恢复文件到第一个版本：
```bash
git cat-file -p <hash1> > test.txt
```
显然，Git并不存储文件的名字，而是存储文件的内容，这些数据称为数据对象，Git可以判断出数据对象的类型：
```bash
git cat-file -t <hash>
```

### 树对象
树对象相当于文件系统中的目录项，包含一条或多条书对象记录，每条记录含有一个指向数据对象或子树对象的SHA-1指针，以及其他信息。当查看主分支的最新提交的树对象时：
```bash
git cat-file -p master^{tree}
```
会看到这样的输出：
```
100644 blob a906cb2a4a904a152e80877d4088654daad0c859 README
100644 blob 8f94139338f9404f26296befa88755fc2598c289 Rakefile
040000 tree 99f1a6d12cb4b6f19c8655fca46c3ecf317074e0 lib
```
其中的lib是一个子目录，也是一个树对象。显然，书对象在文件存储中基本上充当目录的身份。通常我们通过把文件添加到暂存区然后提交来创建书对象，实际上也可以手动完成一系列的过程。比如：
```bash
# 把test.txt添加到暂存区
# 100644表示普通文件，100755表示可执行文件， 120000表示符号链接
# --add是必须的，因为它没有进入过暂存区
# --cacheinfo也是必须的，因为要添加的文件在Git数据库中而不是工作目录中。
git update-index --add --cacheinfo 100644 <hash> test.txt

# 有了暂存区，可以创建一个树对象
git write-tree

# 接着进行第二个树对象的创建
echo 'new file' > test.txt
git update-index test.txt
git update-index --add new.txt
git write-tree
```
也可以把某个树对象加入到暂存区作为子目录：
```bash
git read-tree --prefix=<dir> <tree>
```

### 提交对象
以上方法创建的树对象也相当于一个个快照，但是无从记住它们的哈希值，这时候要用到提交。可以把一个树对象形成一个提交对象：
```bash
# 通过重定向输入提交信息，得到<commit1>的哈希值
echo 'first commit' | git commit-tree <tree>

# 再创建两个提交，-p指定父提交
echo 'second commit' | git commit-tree <tree> -p <commit1>
echo 'third commit' | git commit-tree <tree> -p <commit2>
```
此时已经可以通过 `git log` 查看提交历史。

### 对象存储
Git存储对象的时候会插入头部信息，实际上是 `<type> <size>\0` 的格式，把它和文件内容拼接在一切以后就可以计算哈希值。计算完毕以后，Git使用 `zlib` 压缩数据再写入磁盘。这里的`<type>`有三个取值：
- `blob` ：数据对象
- `tree` ：树对象
- `commit` ：提交对象

## Git引用
虽然建立了提交链表以后可以通过最新的提交来查看历史，但是仍需知道最新提交的哈希值。通过引用，我们可以用一个文件来保存这个哈希值，并给这个文件一个可读的名字，这就叫引用。它们被存储在 `.git/refs` 目录下，创建一个引用：
```bash
echo "<hash>" > .git/refs/heads/master
```
此时就可以使用 `master` 这个名字了。如果需要更新某个引用，不建议直接修改文件，最好通过指令：
```bash
git update-ref refs/heads/master
```
或者通过 `update-ref` 来建立一个引用：
```bash
git update-ref refs/heads/test <hash>
```

### HEAD引用
HEAD并不像其他的引用一样包含一个哈希值，而是指向其他的引用，它的内容类似：
```
ref: refs/heads/master
```
当执行 `git commit` 的时候，它取得HEAD实际指向提交的哈希值作为其父提交。可以手动查看和编辑HEAD文件，但有个命令更安全：
```bash
# 查看HEAD引用
git symbolic-ref HEAD

# 修改HEAD引用
git symbolic-ref HEAD refs/heads/test

# 对比上条指令，以下指令会报错
git symbolic-ref HEAD test
```

### 标签引用
实际上存在第四种对象，标签对象。它非常类似提交对象，但通常它指向一个提交对象而非树对象。相当于是某个提交的一个别名。有两种标签，分别是附注标签和轻量标签。轻量标签只包含指向提交的哈希值：
```bash
# 创建一个轻量标签
git update-ref refs/tags/v1.0 <hash>
```
附注标签包含更多的信息，比如标签的作者、标签的日期、标签的信息、标签的签名等等。实际上Git会创建一个引用来指向标签对象：
```bash
# 创建一个附注标签
git tag -a v1.1 <hash> -m 'test tag'
```

### 远程引用
对于远程仓库，Git有一些本地同步分支，它们放在 `.git/refs/remotes` 目录下。远程引用是只读的，即使使用 `git checkout` 到某个远程引用，也不会把HEAD指向它，不能通过 `git commit` 来修改它。

## 包文件
对于一个足够大的代码文件，即使只修改了一点点，Git也会产生一个新的数据对象来储存新的版本。这样占用存储空间会很大，它们被称为“松散”对象格式。然而Git时不时会把多个这种对象打包成一个“包文件”，来节约空间。当松散对象太多、执行 `git gc` 、推送到远程仓库时，Git就会进行打包。比如：
```bash
git gc
```
执行结束以后，发现许多对象都不见了，出现了一个 `.pack` 文件，它是容纳了打包前所有对象数据的包文件；还有一个 `.idx` 文件，它是一个索引文件，用于快速查询某个对象在包文件中的位置。在打包的过程中，Git查找接近的文件，把最新的版本的完整数据保留，把之前的版本按差异的方式存储。

## 引用规格
当像这样添加一个远程仓库：
```bash
git remote add origin https://github.com/schacon/simplegit-progit
```
Git会向 `.git/config` 文件添加一个小节：
```
[remote "origin"]
url = https://github.com/schacon/simplegit-progit
fetch = +refs/heads/*:refs/remotes/origin/*
```
这里的 `fetch` 行被称为引用规格，形式是 `+<src>:<dst>` ，其中 `<src>` 是远程仓库中的引用， `<dst>` 是在本地仓库中的位置， `+` 表示即使不能快进也要强制更新引用。如果只希望拉取一次，也可以：
```bash
# 直接指定引用规格
git fetch origin master:refs/remotes/origin/mymaster

# 指定多个引用规格
git fetch origin master:refs/remotes/origin/mymaster topic:refs/remotes/origin/topic
```
或者可以修改配置文件，使得一次拉取操作多个引用规格：
```
[remote "origin"]
url = https://github.com/schacon/simplegit-progit
fetch = +refs/heads/master:refs/remotes/origin/master
fetch = +refs/heads/experiment:refs/remotes/origin/experiment
```

### 引用规格推送
如果希望把 `master` 分支推送到 `qa/master` 分支，可以：
```bash
git push origin master:refs/heads/qa/master
```
如果希望这是默认行为，可以修改配置文件，加一条 `push` ：
```
[remote "origin"]
url = https://github.com/schacon/simplegit-progit
fetch = +refs/heads/*:refs/remotes/origin/*
push = refs/heads/master:refs/heads/qa/master
```

### 删除引用
通过以下命令可以删除一个远程引用：
```bash
git push origin :topic
```

## 传输协议
Git通过两种主要的方式在版本库之间传递数据，分别是哑协议和智能协议。

### 哑协议
这种协议下，服务端甚至不需要有Git，只需要通过HTTP的GET来发送数据。接下来看看具体的过程，克隆仓库：
```bash
git clone http://server/simplegit-progit.git
```
它首先拉取 `info/refs` 文件，这个文件是通过 `git update-server-info` 生成的，它包含了远程引用和SHA-1值的列表。有了这个文件，就可以遍历所有的数据了。然而有些提交并不以松散形式存在，这样GET请求会得到404响应。此时Git会检查 `objects/info/http-alternates` 文件，它指示了一些替代版本库，如果不为空，Git会到那里去试试。如果依然不行，Git就会获取 `objects/info/packs` 文件来查看包文件列表，它会先获取对应包文件的索引来检查需要的文件在哪个包里，然后把包文件下载下来。

### 智能协议
哑协议的效率比较低，并且不能从客户端向服务端推送数据。智能协议的Git能理解客户需要什么数据，并生成合适的包文件。有两组进程在工作，分别负责上传数据和下载数据。

#### 上传数据
Git使用 `send-pack` 和 `receive-pack` 进程，前者运行在客户端，后者运行在服务端。在推送的时候，Git会尝试SSH连接服务器，然后通过SSH在服务端执行命令 `git-receive-pack 'simplegit-progit.git'` 这样的命令，它先指出服务端能力的列表，然后为每一个引用发送一行响应。 `send-pack` 根据响应判断哪些提交是本地有而服务端没有的，然后为每个需要更新的引用发送一行数据（该行长度、旧SHA-1值、新SHA-1值和将要更新的引用等）。接下来客户端发送一个包文件，包含了需要传送的所有对象，服务端发回相应告知是成功还是失败。

#### 下载数据
Git使用 `fetch-pack` 和 `upload-pack` 进程，前者由客户端启动，连接至服务器端的后者。同样通过SSH连接服务器然后执行 `git-upload-pack 'simplegit-progit.git'` ，此时服务器返回的内容与上述类似，但是服务器能力有所不同，另外它还返回了HEAD的指向。 `fetch-pack` 会响应一个“want”和它需要的对象的SHA-1值，也发送一个“have”和它拥有对象的SHA-1值，最后发送一个“done”来表示结束。此后 `upload-pack` 发送需要的包文件。

## 维护与数据恢复

### 维护
Git会不定时自动运行 `git gc --auto` 命令，然而一般而言这个命令没有太大的效果。大约需要7000个以上松散对象或者超过50个包文件才会让Git启动真正的 `gc` 命令。这两个阈值可以通过 `gc.auto` 和 `gc.autopacklimit` 来调整。当 `gc` 发生的时候，它还会打包所有引用到文件 `.git/packed-refs` 中，Git在查找引用的时候，先查找 `.git/refs` 目录，找不到的时候会到这个文件中查找。

### 数据恢复
如果由于硬重置导致丢失了几个提交，可以通过 `git reflog` 来查看引用的历史，然后通过SHA-1值还原回去。如果连引用日志也丢失了，可以通过 `git fsck --full` 来找到所有的空悬提交，检查是否有需要的提交。

### 移除对象
如果某个版本引入了一个大文件并且在后续的版本中已经将这个文件删除，但是由于历史原因，这个文件仍然存在于仓库中，进行 `git clone` 的时候这个文件也会被下载下来。如果不知道是哪个文件占用了如此大的空间，并且这个文件已经被 `gc` 打包了，可以通过以下命令找到：
```bash
# 按照大小排序
git verify-pack -v .git/objects/pack/pack-29…69.idx | sort -k 3 -n | tail -3

# 查看文件名
git rev-list --objects --all | grep <hash>

# 找到对这个文件进行改动的提交
git log --oneline --branches -- <file>

# 从历史中删除这个文件
# --index-filter表示对暂存区进行操作
# --ignore-unmatch表示当需要的文件不存在时不报错
git filter-branch --index-filter 'git rm --ignore-unmatch --cached git.tgz' -- <commit>^..
```
此时提交历史中已经不含这个文件，不过引用日志和 `fileter-branch` 命令在 `.git/refs/original` 下创建的新引用仍然存在这个文件，需要把它们移出然后重新打包数据库：
```bash
# 移除引用日志
rm -Rf .git/logs/

# 移除filter-branch命令创建的引用
rm -Rf .git/refs/original/

# 重新打包数据库
git gc
```
这个文件仍作为松散对象存在，但是不会被克隆，如果需要完全删除，可以：
```bash
git prune --expire now
```

## 环境变量
Git在shell中运行，它需要借助一些环境变量。

### 全局行为
- `GIT_EXEC_PATH` ：Git会到这里去寻找它的子程序，如 `git-commmit` ，可以用 `git --exec-path` 来查看
- `HOME` ：用户目录，Git会在这里寻找 `.gitconfig` 文件
- `PREFIX` ：用于系统级配置，Git会查看 `$PREFIX/etc/gitconfig` 配置文件
- `GIT_CONFIG_NOSYSTEM` ：如果设置了这个变量，Git就不会查看系统级配置文件
- `GIT_PAGER` ：控制多页显示使用的分页器
- `GIT_EDITOR` ：控制Git使用的编辑器

### 版本库位置
- `GIT_DIR` ：`.git` 目录的位置，如果不设置这个，Git会逐层向上查找直到到达用户目录或根目录
- `GIT_CEILING_DIRECTORIES` ：控制查找 `.git` 目录的行为，比如在访问加载很慢的目录时，提早停止查找等
- `GIT_WORK_TREE` ：版本库的工作目录的该目录，如果不设置，Git会使用 `GIT_DIR` 的父目录
- `GIT_INDEX_FILE` ：索引文件的位置
- `GIT_OBJECT_DIRECTORY` ：`.git/objects` 目录的位置
- `GIT_ALTERNATE_OBJECT_DIRECTORIES` ：一个冒号分隔的目录列表，如果在 `.git/objects` 目录下找不到对象，Git会到这些目录下查找

### 路径规则
以下规则也可以通过前缀来指定，如 `:(glob)*.c` 。
- `GIT_GLOB_PATHSPECS` ：如果设置了这个变量，通配符就进行通配
- `GIT_NOGLOB_PATHSPECS` ：如果设置了这个变量，通配符就被当做普通的星号
- `GIT_ICASE_PATHSPECS` ：如果设置了这个变量，就不区分大小写
- `GIT_LITERAL_PATHSPECS` ：如果设置了这个变量，不允许通配符，也不允许前缀

### 提交
- `GIT_AUTHOR_NAME` ：作者名字
- `GIT_AUTHOR_EMAIL` ：作者邮箱
- `GIT_AUTHOR_DATE` ：作者日期
- `GIT_COMMITTER_NAME` ：提交者名字
- `GIT_COMMITTER_EMAIL` ：提交者邮箱
- `GIT_COMMITTER_DATE` ：提交者日期

### 网络
- `GIT_CURL_VERBOSE` ：如果设置了这个变量，Git会打印出CURL的调试信息
- `GIT_SSL_NO_VERIFY` ：如果设置了这个变量，Git会跳过SSL证书的验证
- `GIT_HTTP_LOW_SPEED_LIMIT` ：如果设置了这个变量，Git会限制HTTP传输的最低速度
- `GIT_HTTP_LOW_SPEED_TIME` ：如果设置了这个变量，当低于最低速度一定的时间后，Git会中断HTTP传输
- `GIT_HTTP_USER_AGENT` ：HTTP请求的User-Agent头部信息

### 比较和合并
- `GIT_DIFF_OPTS` ：仅支持有效值 `-u<n>` 或 `--unified=<n>` ，指定 `git diff` 命令显示的行数
- `GIT_EXTERNAL_DIFF` ：指定一个外部程序来比较文件
- `GIT_DIFF_PATH_COUNTER` ：提供给外部文件使用，表示被比较文件的序号
- `GIT_DIFF_PATH_TOTAL` ：提供给外部文件使用，表示被比较文件的总数
- `GIT_MERGE_VERBOSITY` ：控制递归合并策略的输出，允许的值有以下几个，默认是2
    - 0：什么都不输出，除了错误信息
    - 1：只显示冲突
    - 2：还显示文件改变
    - 3：显示未改变而被跳过的文件
    - 4：显示处理的所有路径
    - 5：显示详细的调试信息

### 调试
- `GIT_TRACE` ：常规跟踪，跟踪别名的展开和子程序的调用
- `GIT_TRACE_PACK_ACCESS` ：访问打包文件的跟踪，显示被访问的包文件已经文件的偏移量
- `GIT_TRACE_PACKET` ：显示网络数据包的传输
- `GIT_TRACE_PERFORMANCE` ：显示性能数据，包括每个Git命令花费的时间
- `GIT_TRACE_SETUP` ：显示Git发现的版本库和交互环境的信息

### 其他
- `GIT_SSH` ：指定SSH客户端
- `GIT_ASKPASS` ：指定一个程序来获取身份验证信息
- `GIT_NAMESPACE` ：指定一个命名空间，主要在服务端有用
- `GIT_FLUSH` ：如果设置了这个变量，Git向标准输出进行无缓冲输出
- `GIT_REFLOG_ACTION` ：这个信息会被写到引用日志中

<br><br>

# 其他环境中的Git

## 图形界面

### gitk和git-gui
在安装Git的时候，它也装上了gitk和git-gui。gitk是一个图形化的历史查看器，可以通过以下命令启动：
```bash
# 移动到仓库中
cd <repo>

# 启动gitk（后面可以跟上命令行选项，它们会被传递给git log）
gitk
```
git-gui是一个用来制作提交的工具，可以通过以下命令启动：
```bash
git gui
```

### 其他
- [Git官方提供了一份清单](http://git-scm.com/downloads/guis)
- [Git Wiki上的一份清单](https://git.wiki.kernel.org/index.php/Interfaces,_frontends,_and_tools#Graphical_Interfaces)

## Bash中的Git
Git自带了一些shell脚本，可以在Bash中使用。比如在Git源码的 `contrib/completion` 下有个 `git-completion.bash` 文件，可以把它放到 `~/.git-completion.bash` 中，然后在 `~/.bashrc` 中加入以下内容：
```bash
source ~/.git-completion.bash
```
这样就可以使用Tab键来补全Git命令了。

## Zsh中的Git
Git同样为Zsh提供了补全脚本，可以在Git源码的 `contrib/completion` 下找到文件 `git-completion.zsh` ，然后在 `~/.zshrc` 中加载即可。

## PowerShell中的Git
有一个名为[Posh-Git](https://github.com/dahlbyk/posh-git)的扩展包，提供了命令补全和提示符增强等功能。安装GitHub的话它也会被顺带安装。把以下内容加入到 `%USERPROFILE%\Documents\WindowsPowerShell\profile.ps1` 中：
```powershell
. (Resolve-Path "$env:LOCALAPPDATA\GitHub\shell.ps1")
. $env:github_posh_git\profile.example.ps1
```
如果没有安装GitHub，也可以从上面的链接手动下载Posh-Git，并解压到 `WindowsPowerShell` 下，然后以管理员身份打开PowerShell，执行以下命令：
```powershell
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Confirm
cd ~\Documents\WindowsPowerShell\posh-git
.\install.ps1
```
它会自动设置好 `profile.ps1` 。

<br><br>

# 将Git嵌入你的应用

## 命令行Git方式
这很简单直接，而且能够支持Git所有的功能，但也有一些其他的问题。比如，命令输出是纯文本，需要自己解析；当出现问题时可能很难定位；如果出现多进程访问同一个仓库，可能会出现问题。

## Libgit2
[Litgit2](http://libgit2.github.com/)致力于为其他程序使用Git提供更好的API。接下来看一段C代码：
```c
// 打开一个版本库
git_repository *repo;
int error = git_repository_open(&repo, "/path/to/repository");

// 逆向引用 HEAD 到一个提交
git_object *head_commit;
error = git_revparse_single(&head_commit, repo, "HEAD^{commit}");
git_commit *commit = (git_commit*)head_commit;

// 显示这个提交的一些详情
printf("%s", git_commit_message(commit));
const git_signature *author = git_commit_author(commit);
printf("%s <%s>\n", author->name, author->email);
const git_oid *tree_id = git_commit_tree_id(commit);

// 清理现场
git_commit_free(commit);
git_repository_free(repo);
```
可以看出，用C语言来使用这个库有点痛苦，当然这个库还有其他语言的绑定。

### 高级功能
Libgit2允许自己实现后端，从而以不同于Git的方式来存储数据，自定义的后端被调用的流程大致如下：
```c
// 初始化一个空的数据库前端
git_odb *odb;
int error = git_odb_new(&odb);

// 初始化数据库的后端
git_odb_backend *my_backend;
error = git_odb_backend_mine(&my_backend, /*…*/);

// 为前端增加一个后端
error = git_odb_add_backend(odb, my_backend, 1);
git_repository *repo;

// 打开一个版本库，让它使用我们的前端来查找对象
error = git_repository_open(&repo, "some-path");
error = git_repository_set_odb(odb);
```
其中的 `git_odb_backend_mine` 函数是自定义的，需要完成结构体成员填写的工作：
```c
typedef struct {
    git_odb_backend parent;

    // 其它的一些东西
    void *custom_context;
} my_backend_struct;

int git_odb_backend_mine(git_odb_backend **backend_out, /*…*/)
{
    my_backend_struct *backend;

    backend = calloc(1, sizeof (my_backend_struct));

    backend->custom_context = …;

    backend->parent.read = &my_backend__read;
    backend->parent.read_prefix = &my_backend__read_prefix;
    backend->parent.read_header = &my_backend__read_header;
    // ……

    *backend_out = (git_odb_backend *) backend;

    return GIT_SUCCESS;
}
```
这里的 `my_backend_struct` 必须是一个 `git_odb_backend` 结构。

### 其它绑定
有其它语言的支持。

#### LibGit2Sharp
[LibGit2Sharp](https://github.com/libgit2/libgit2sharp)可以让.NET和Mono程序使用Libgit2。它是用C#写成的。

#### objective-git
[objective-git](https://github.com/libgit2/objective-git)是为了Objective-C程序准备的。它与Swift完美兼容。

#### pygit2
[pygit2](http://www.pygit2.org/)是为了Python程序准备的。

## JGit
对于Java来说，[JGit](http://www.eclipse.org/jgit)是一个功能齐全的Git库。

### 起步
有很多种方式来使用JGit，最简单的方式就是使用Maven。在 `pom.xml` 中加入以下内容：
```xml
<dependency>
    <groupId>org.eclipse.jgit</groupId>
    <artifactId>org.eclipse.jgit</artifactId>
    <version>3.5.0.201409260305-r</version>
</dependency>
```
以上的版本号到[这里](http://mvnrepository.com/artifact/org.eclipse.jgit/org.eclipse.jgit)查看。如果想要自己管理二进制包，可以从[这里](http://www.eclipse.org/jgit/download)下载，然后执行如下命令把它们构建进项目：
```bash
javac -cp .:org.eclipse.jgit-3.5.0.201409260305-r.jar App.java
java -cp .:org.eclipse.jgit-3.5.0.201409260305-r.jar App
```

### 底层命令
类似于Git的命令，JGit同样有高层API和底层API之分，高层是一个面向用户的友好前端，底层则更多地和Git进行交互。当使用JGit的时候，一般需要从获取一个 `Repository` 对象开始：
```java
// 创建一个新仓库
Repository newlyCreatedRepo = FileRepositoryBuilder.create(
    new File("/tmp/new_repo/.git"));
newlyCreatedRepo.create();

// 打开一个存在的仓库
Repository existingRepo = new FileRepositoryBuilder()
    .setGitDir(new File("my_repo/.git"))
    .build();
```
拥有了一个 `Repository` 对象，就可以使用它来执行一些操作：
```java
// 获取引用
Ref master = repo.getRef("master");

// 获取该引用所指向的对象
ObjectId masterTip = master.getObjectId();

// Rev-parse
ObjectId obj = repo.resolve("HEAD^{tree}");

// 装载对象原始内容
ObjectLoader loader = repo.open(masterTip);
loader.copyTo(System.out);

// 创建分支
RefUpdate createBranch1 = repo.updateRef("refs/heads/branch1");
createBranch1.setNewObjectId(masterTip);
createBranch1.update();

// 删除分支
RefUpdate deleteBranch1 = repo.updateRef("refs/heads/branch1");
deleteBranch1.setForceUpdate(true);
deleteBranch1.delete();

// 配置
Config cfg = repo.getConfig();
String name = cfg.getString("user", null, "name");
```

### 高层命令
相比于上面的API，高层API更加友好，首先需要获取一个 `Git` 对象：
```java
Repository repo;
// 构建仓库
Git git = new Git(repo);
```
Git类采用构造器风格，如下模拟 `git ls-remote` 命令：
```java
CredentialsProvider cp = new
UsernamePasswordCredentialsProvider("username", "p4ssw0rd");
Collection<Ref> remoteRefs = git.lsRemote()
    .setCredentialsProvider(cp)
    .setRemote("origin")
    .setTags(true)
    .setHeads(false)
    .call();
for (Ref ref : remoteRefs) {
    System.out.println(ref.getName() + " -> " + ref.getObjectId().name());
}
```