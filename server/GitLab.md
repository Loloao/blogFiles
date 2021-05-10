# GitLab
GitLab 的主要构成
- `Logrotate`日志文件管理工具
- `Postgresql`数据库
- `Redis`缓存服务器

## 安装
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
 

