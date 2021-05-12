# DevOps 工作流
为了熟悉`DevOps`，我决定自己搭建一套工作流
## GitLab
GitLab 的主要构成
- `Logrotate`日志文件管理工具
- `Postgresql`数据库
- `Redis`缓存服务器

### 安装
安装之后需要生成证书
- `key`：`sudo openssl genrsa -out /etc/gitlab/ssl/gitlab.example.com.key 2048`
- `csr`：` sudo openssl req -new -key "/etc/gitlab/ssl/gitlab.example.com.key" -out "/etc/gitlab/ssl/gitlab.example.com.csr"`
- `crt`：`sudo openssl x509 -req -days 365 -in "/etc/gitlab/ssl/gitlab.example.com.csr" -signkey "/etc/gitlab/ssl/gitlab.example.com.key" -out "/etc/gitlab/ssl/gitlab.example.com.crt"`
- `tem`：`sudo openssl dhparam -out /etc/gitlab/ssl/dhparams.tem 2048`
同时需要`chmod 600 *`更改文件夹的权限
之后需要在`/etc/gitlab/gitlab.rb`中对证书进行配置
- `external_url`：将协议`http`开头改为`https`
- `nginx['redirect_http_to_https']`：将它配置为`true`
- `nginx['ssl_certificate']`：将它配置为`crt`证书路径
- `nginx['ssl_certificate_key']`：将它配置为`key`路径
- `nginx['ssl_dhparam']`：将它配置为`tem`文件路径
之后运行`gitlab-ctl reconfigure`
找到 gitlab 下的 nginx 代理工具更改`http`配置文件
- `sudo -e /var/opt/gitlab/nginx/conf/gitlab-http.conf`
- 找到`server_name`并在后面增加`rewrite ^(.*)$ https://$host$1 permanent;`
- `gitlab-ctl restart`
之后更改电脑中的`hosts`文件，如果为 mac 则打开`/etc/hosts`，将服务器域名映射为`gitlab.example.com`
 
## Ansible
Ansible 是一个开源部署工具，`SSH`协议通讯，全平台，无需编译，模块化部署管理。推送`Playbook`进行远程节点快速部署，适合中小规模快速部署。
优势
- 轻量级无客户端
- 开源免费，学习成本低
- 使用`Playbook`作为核心配置架构，统一的脚本格式，进行批量式部署
- 完善的模块化扩展，支持目前主流的开发场景
- 强大的稳定性和兼容性
- 有着活跃的官方社区问题讨论

### 安装
当我们安装 ansible 时，我们希望它所使用的`python`和其他软件使用的`python`不在同一环境下，此时需要安装`virtualenv`来对`python`环境进行隔离，首先需要下载`pip`
- `apt install pip`
- 之后可以创建软链接`ln -s /usr/local/bin/pip3 /usr/local/bin/pip`，使用`pip3`时可以以`pip`代替
- `pip install virtualenv`
之后创建 Ansible 账户
- `useradd deploy && su - deploy`创建并进入该账户的命令行界面
- `virtualenv -p /usr/local/bin/python3 .py3-a-env`前面一个指定`python`版本，后面的用于创建该名称下的`virtualenv`实例目录
Git 源代码安装`ansible2.5`
- `cd /home/deploy/py3-a-env`
- `git clone https://github.com/ansible/ansible.git`
- `cd ansible && git checkout stable-2.5`
加载`python3 virtualenv`环境
- `source /home/deploy/.py3-a-env/bin/active`
安装 ansible 依赖包
- `pip install paramiko PyYAML jinja2`
在`python3`的环境下加载`ansible 2.5`
- `source /home/deploy/.py3-a-env/ansible/hacking/env-setup -q`
- `ansible --version`进行验证

### Playbooks
`Test Playbooks`分为以下几个目录清单
- `inventory/`：`Server`详细清单目录，部署主机的相关域名，地址或是变量参数
  - `testenv`：具体清单与变量声明文件，部署到哪个环境，比如`develop`，`uat`，`production`等环境
- `roles/`：`roles`任务列表，存放一个或是多个`role`
  - `testbox/`：`testbox`详细任务，具体的项目名称
    - `tasks/`
      - `main.yml`：`testbox`主任务文件
- `deploy.yml`：`playbook`任务入口文件，它将调度`roles`下的所有任务，并部署到`inventory`主机中
编写规范
- `testenv`：由上下两部分组成
  - `testservers`：作为一个`Server`组列表，可保存一个或多个目标主机，域名或是ip地址
    - `test.example.com`：目标部署服务器主机名
  - `testservers:vars`：`Server`组列表参数，保存目标主机的键值对参数
    - `server_name=test.example.com`
    - `user=root`
    - `output=/root/test.txt`
- `main.yml`：主任务文件，保存`task`的任务乐章，以一个或多个`task`作为音符，`tash`一般由两部分组成
  - `name`：任务名称，方便编写好`task`后知道是干什么的
  - `shell`：具体要执行的任务，一般是使用`shell`模块执行命令，里面的变量在`testenv`当中
- `deploy.yml`：任务入口文件，它将`playbook`下的所有内容展示给`ansible`命令，最后部署到对应主机当中
  - `hosts: "testservers"`：`Server`列表，对应`testenv`定义的目标主机
  - `gather_facts: true`：获取目标主机的基本信息
  - `remote_user: root`：表示我们在目标主机下使用的是`root`主机权限
  - `roles: test`：进入`roles/testbox`执行任务

## Jenkins
主流的运维开发平台，兼容所有主流开发环境，插件市场可与海量业内主流开发工具实现集成。`Job`为配置单位与日志管理，使运维与开发人员能协同工作
根据权限划分不同`Job`不同角色，具有强大的负载均衡功能，保证我们项目的可靠性
