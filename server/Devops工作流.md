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

### 安装
- 首先需要安装java
- `wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -`导入`Jenkins`存储库`GPG`密钥
- `sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'`将`Jenkins`存储库添加到系统中
- `sudo apt update`和`sudo apt install jenkins`安装`jenkins`
- `systemctl status jenkins`打印服务状态
- `/etc/default/jenkins`可改变配置文件
- jenkins 默认用户为`jenkins`，需要更改为管理员
- 需要更改 jenkins 文件的执行权限
  - `sudo chown -R loloao:loloao /var/lib/jenkins`
  - `sudo chown -R loloao:loloao /var/log/jenkins`
  - `sudo chown -R loloao:loloao /var/cache/jenkins`

### Jenkins Job
代表一个任务或项目，并且可配置与可执行，执行后的记录称之为`Build`，所有文件集中保存
- `Freestyle Job`：
  - 需要在页面添加模块配置项与参数完成配置
  - 每个`Job`只能实现一个开发功能
  - 无法将配置代码化，不利于`Job`配置迁移与版本控制。不能记录和保存所有人的系统配置历史，也就是不知道谁更改了
  - 逻辑相对简单，无需额外学习成本
- `Pipeline Job`：匹配持续集成与持续交互的概念
  - 所有模块，参数配置都可以体现为一个`pipeline`脚本
  - 可以定义多个`stage`构建一个管道工作集
  - 所有配置代码化，方便`Job`配置迁移与版本控制

`Jenkins Job`构建配置
1. 配置`Jenkins server`本地`Gitlab DNS`
2. 安装`git client`，`curl`工具依赖
3. 关闭系统`Git http.sslVerify`安全认证
4. 添加`Jenkins`后台`Git client user`与`email`
5. 添加`Jenkins`后台`Git Credential`凭据，配置全局凭据`<jenkins host>/credentials/store/system/domain/_/newCredentials`

`Pipeline Job`编写规范，基础架构
- 所有代码包裹在`pipeline{}`层内
- `stages{}`层用来包含该`pipeline`所有`stage`子层
- `stage{}`层用来包含我们需要编写任务的`step{}`子层
- `steps{]`层用来添加我们具体需要调用的模块语句
- `agent`：定义`pipeline`在哪里运行，可以使用`any`，`none`或具体的`Jenkins node`主机名等，比如我们要特指在`node1`上执行，可以写成`agent{ node {label 'node1'}}`
- `environment`：可以使用`变量名称=变量值`定义环境变量，可以定义全局环境变量，应用所有`stages`任务。可以单独定义`stage`环境变量应用单独的`stage`任务
  `environment { PATH="/bin:/sbin:/usr/bin..."}`
- `script`：在`steps`内定义`script{}`，可以使用`groovy`脚本语言，用来进行脚本逻辑运算
- `steps`
  - `echo`：打印输出
  - `sh`：调用 Linux 系统 shell 命令
  - `git url`：调用`git`模块进行 git 相关操作
```
#!groovy

pipeline {
	agent {
		node {
			label 'master'
		}
	}

	environment {
		PATH="/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin"
	}

	parameters {
		choice(
			choices: 'dev\nprod',
			description: 'choose deploy environment',
			name: 'deploy_env'
		)

		string (
			name: 'version',
			defaultValue: '1.0.0',
			description: 'build version'
		)
	}

	stages {
		stage("Checkout test repo") {
			steps {
				dir("${env.WORKSPACE}") {
					git branch: 'master',
					credentialsId: '68764351-42af-4130-bbad-67ea8410e12c',
					url: 'http://192.168.3.2:8082/root/test-repo.git'
				}
			}
		}

		stage("Print env variable") {
			steps {
				dir ("${env.WORKSPACE}") {
					sh """
					echo "[INFO] Print env variable"
					echo "current deployment environment is $deploy_env" >> test.properties
					echo "The build is $version" >> test.properties
					echo "[INFO] Done"
					"""
				}
			}
		}

		stage("Check test properties") {
			steps {
				dir ("${env.WORKSPACE}") {
					sh """
					echo "[INFO] Check test properties"
					if [ -s test.properties ]
					then
						cat test.properties
						echo "[INFO] Done"
					else
						echo "test.properties is empty"
					fi

					echo "[INFO] Build finished"
					"""
				}
			}
		}
	}
}
```

### Jenkins 应用
