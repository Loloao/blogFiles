# windows 下配置终端

没有一个好看的命令行工作实在是提不起劲，刚上了台新电脑，正好记录下配置的全过程。

### WSL

能在 windows 环境下使用 linux 实在是太令人惊喜，虽然只是个子系统，但总比虚拟机和没有好啊！

1. **控制面板** => **所有控制面板项** => **程序和功能**  => **启用或者关闭Windows功能** => **适用于Linux的Windows子系统** => **启动**

2. 下载(WSL2)[https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi] 

3. 默认设为 WSL2 

   `wsl --set-default-version 2`

4. windows market 下载 ubuntu 等 linux 系统镜像之后安装，之后命令行输入 bash 即可进入

5. 安装 zsh 并设置为默认 shell

   ```shell
   sudo apt-get install -y zsh
   chsh -s ${which zsh} ${$USER}
   echo $SHELL ## 用于确认是否设为默认 shell
   ```

6. 通过 oh-my-zsh 设置主题

   ```shell
   sh -c "$(wget -O- https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" ## 安装 oh-my-zsh
   vim ~/.zshrc
   
   ## 更改主题为 random
   ZSH_THEME="random"
   
   ## 每次更改 zshrc 配置需要重载
   source ~/.zshrc
   ```

7. 由于直接下载的 ubuntu 的 `apt-get`使用的是英国源，需要更改为国内的源下载速度才快

   ```shell
   ## 重命名原来的源为备份
   sudo mv /etc/apt/sources.list sources.list_backup
   ## 新建 sources.list
   sudo vim /etc/apt/sources.list
   ## 将文件内容替换如下
   
   deb http://cn.archive.ubuntu.com/ubuntu/ xenial main restricted
   deb http://cn.archive.ubuntu.com/ubuntu/ xenial-updates main restricted
   deb http://cn.archive.ubuntu.com/ubuntu/ xenial universe
   deb http://cn.archive.ubuntu.com/ubuntu/ xenial-updates universe
   deb http://cn.archive.ubuntu.com/ubuntu/ xenial multiverse
   deb http://cn.archive.ubuntu.com/ubuntu/ xenial-updates multiverse
   deb http://cn.archive.ubuntu.com/ubuntu/ xenial-backports main restricted universe multiverse
   deb http://security.ubuntu.com/ubuntu xenial-security main restricted
   deb http://security.ubuntu.com/ubuntu xenial-security universe
   deb http://security.ubuntu.com/ubuntu xenial-security multiverse
   
   ## 更新apt软件源
   sudo apt-get update
   ```

### windows terminal

1. windows market 下载即可

2. 设置默认打开的命令行工具为 wsl

   ```json
   // settings.json 中启动时默认的 profile, 需要与 WSL 的 Guid 一致
   "defaultProfile": "{07b52e3e-de2c-5db4-bd2d-ba144ed6c273}",
   
   // WSL 的 profile
   "profiles": {
       "list": [
           {
               "guid": "{07b52e3e-de2c-5db4-bd2d-ba144ed6c273}",
               // ...
           }
       ]
   }
   ```

3. 启动时默认打开的路径

   ```json
   // profile 中配置
   {
       // ...
       "startingDirectory": "../../../Project",
   }
   ```

4. 可以配置多个主题再自己选用一个

   ```json
   // 在 profile 中配置
   {
       // ...
       "colorScheme" : "PencilDark",
   }
   
   "schemes": [
           {
               "name" : "Frost",
               "background" : "#FFFFFF",
               "black" : "#3C5712",
               // ...
           }
       ]
   ```

### GIT

当使用一个新电脑时我们需要重新安装 git 并进行 ssh 的配置

1. 安装 git 并进行基本配置

   ```shell
   sudo apt-get update
   sudo apt-get install git
   
   git config --global user.name "yourname"
   git config --global user.email "youremail"
   
   ## 看是否配置过密钥
   cd ~/.ssh
   ## 没有则进行创建
   ssh-keygen -t rsa -C 'youremail@qq.com'
   ## 查看秘钥内容并进行复制
   cat ~/.ssh/id_rsa.pub
   ```

2. 将复制的秘钥内容复制到 github 中的 Settings => SSH and GPG keys => New SSH key，之后收到邮件就表示成功

### node

没有 node 怎么干活！

```
cd ~
## 此处下载的是 14 版本 node
curl -sL https://deb.nodesource.com/setup_14.x -o nodesource_setup.sh
sudo bash nodesource_setup.sh
sudo apt install nodejs
node -v
```


