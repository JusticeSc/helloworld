# Linux常用命令

---

### 一、文件与目录操作
- **ls**：列出目录内容（常用选项：`-l` 详细信息，`-a` 显示隐藏文件，`-h` 人性化大小）
- **cd**：切换目录（`cd ~` 回家目录，`cd -` 返回上一目录）
- **pwd**：显示当前工作路径
- **mkdir**：创建目录（`-p` 递归创建）
- **rm**：删除文件或目录（`-r` 递归删除，`-f` 强制删除）
- **cp**：复制文件/目录（`-r` 复制目录）
- **mv**：移动或重命名文件
- **touch**：创建空文件或更新文件时间戳

---

### 二、查看与编辑文件
- **cat**：输出整个文件内容
- **less / more**：分页查看文件（`less` 支持上下滚动）
- **head / tail**：查看文件开头/末尾（`tail -f` 实时追踪日志）
- **grep**：文本搜索（常用选项：`-i` 忽略大小写，`-r` 递归搜索，`-v` 反向匹配）
- **vim / nano**：终端文本编辑器（Vim 功能强大，nano 更易上手）

---

### 三、权限与属性
- **chmod**：修改文件权限（如 `chmod 755 file`）
- **chown**：修改文件所有者（`chown user:group file`）
- **ln**：创建链接（`-s` 创建软链接）

---

### 四、系统信息与进程管理
- **ps**：查看进程状态（`ps aux` 查看所有进程）
- **top / htop**：实时监控系统资源和进程（htop 更友好）
- **kill / pkill**：终止进程（`kill -9 PID` 强制结束）
- **jobs / fg / bg**：管理后台任务
- **df**：查看磁盘空间（`df -h` 人性化显示）
- **du**：查看目录占用空间（`du -sh dir`）
- **free**：查看内存使用情况
- **uname -a**：查看系统内核信息
- **lsof**：列出打开的文件和端口（如 `lsof -i :80` 查看占用 80 端口的进程）

---

### 五、网络相关
- **ping**：测试网络连通性
- **ifconfig / ip addr**：查看/配置网络接口（`ip` 是现代推荐命令）
- **netstat / ss**：查看网络连接、监听端口（`ss -tuln` 更快）
- **curl / wget**：下载或测试 HTTP 请求（`curl -I url` 查看响应头）
- **ssh**：远程登录（配合 `ssh-keygen` 实现免密登录）

---

### 六、压缩与归档
- **tar**：打包/解包（`tar -czvf archive.tar.gz dir/` 压缩，`-xzvf` 解压）
- **gzip / gunzip**：压缩/解压 `.gz` 文件
- **zip / unzip**：处理 ZIP 格式

---

### 七、文本处理与管道
- **|（管道）**：将前一命令输出作为下一命令输入
- **> / >>**：重定向输出（覆盖 / 追加）
- **sort / uniq**：排序与去重（`sort file | uniq -d` 找重复行）
- **wc**：统计行数、单词数、字节数（`wc -l file`）
- **awk / sed**：高级文本处理（如 `awk '{print $1}'` 提取第一列）
- **xargs**：将标准输入转为命令参数（常用于批量处理）

---

### 八、软件包管理（依发行版而定）
- **Debian/Ubuntu**：`apt install`, `apt update`, `apt search`
- **RHEL/CentOS/Fedora**：`yum install` 或 `dnf install`
- **Arch Linux**：`pacman -S`
- **通用 Python 工具**：`pip install`

---

### 九、其他实用命令
- **history**：查看命令历史
- **alias**：设置命令别名（如 `alias ll='ls -l'`）
- **man**：查看命令手册（如 `man grep`）
- **which / type**：查找命令位置或类型
- **date / cal**：查看时间和日历
- **screen / tmux**：持久化终端会话（适合 SSH 远程作业）
