# nodejs-deploy-document
nodejs线上部署文档教程

## 目录
* [购买域名和服务器](#购买域名，服务器)
* [远程登录服务器](#远程登录服务器)
* [配置root及应用管理权限](#配置root及应用管理权限)
* [配置通过ssh实现无密码登录](#配置通过ssh实现无密码登录)
* [修改服务器默认登录端口，增强安全性](#修改服务器默认登录端口，增强安全性)
* [配置iptables和Fail2Ban增强安全防护](#配置iptables和Fail2Ban增强安全防护)
* [搭建服务器的nodejs环境](#搭建服务器的nodejs环境)
* [通过pm2让node服务常驻](#通过pm2让node服务常驻)
* [配置nginx反向代理nodejs端口](#配置nginx反向代理nodejs端口)
* [更改域名的DNS更服务器](#更改域名的DNS更服务器)
* [ubuntu安装mongodb](#ubuntu安装mongodb)
* [往线上mongodb导入数据库](#往线上mongodb导入数据库)
* [为线上数据库配置读取权限](#为线上数据库配置读取权限)
* [从一台服务器迁移数据到另一个线上mongodb](#从一台服务器迁移数据到另一个线上mongodb)
* [数据库备份](#数据库备份)
* [上传数据库备份到七牛私有云](#上传数据库备份到七牛私有云)
* [上传项目代码到线上私有git仓库](#上传项目代码到线上私有git仓库)
* [从本地发布上线和更新服务器的nodejs项目](#从本地发布上线和更新服务器的nodejs项目)
* [项目部署的一般流程总结](#项目部署的一般流程总结)
* [申请选购SSL证书](#申请选购SSL证书)


### 购买域名，服务器
推荐购买阿里云的服务器，新手有六个月的免费套餐，再去阿里云的万网购买域名，一定要备案

### 远程登录服务器
windows系统下载putty客户端 登陆ssh命令行 <br>
下载链接 [https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html "悬停显示")<br>
远程登录服务器     mac/linux 命令  
```
ssh root@公网ip
```
然后输入密码<br>
查看硬盘使用情况 `df -h` <br>
查看服务器硬盘列表 `fdisk -l` <br>
退出服务器 `control + d` <br>

### 配置root及应用管理权限
添加管理员
```
adduser 管理员名称
```
输入密码，full name以及room <br>
设置管理员权限,让他代理root的权限
```
gpasswd -a 管理员名称 sudo
```
进入编辑界面
```
sudu visudo
```
`root ALL=(ALL:ALL) ALL` 在这行下面添加一行 `yu_manager ALL=(ALL:ALL) ALL`
第一个ALL对所有sudo生效，第二个yu_manager可以以任何用户执行命令，第三个yu_manager可以以任何的组执行命令，第四个这个规则适用于所有命令<br>
一句话 yu_manager可以向root一样，只要提供密码可以运行任何root可以运行的命令<br>
加#的行是注释<br>
保存`control + x`，再按下`shift + y`，再点回车<br>
新开命令行  
```
ssh 管理员名称@公网ip
```
输入密码，就可以登录管理员账户<br>
如果失败  可以在root下执行`service ssh restart` 重启一下，然后再登录管理员账户

### 配置通过ssh实现无密码登录
在本地根目录输入`pwd`可以查看本地根目录地址<br>
通过输入`cd`进入本地根目录，输入`ls -a`查看有没有`.ssh`文件夹，如果为空没有配置过<br>
id_rsa(私钥)，id_rsa.pub(公钥)，如果有，更改先需要备份
如果没有.ssh文件夹，先创建文件夹`mkdir .ssh`<br>
本地生成私钥公钥命令，邮箱可以随便填
```
ssh-keygen -t rsa -b 4096 -C "chenyu@yunserver.com"
```
一路回车，默认选择<br>
进入.ssh目录，`cat id_rsa`查看私钥，`cat id_rsa.pub`查看公钥<br>
开启ssh代理   
```
eval "$(ssh-agent -s)"
```
`cd .ssh`进入.ssh目录<br>
ssh key加入代理中  
```
ssh-add ~/.ssh/id_rsa
```
新开命令行，登录服务器命令行执行相同命令
```
ssh-keygen -t rsa -b 4096 -C "chenyu@yunserver.com"
```

```
eval "$(ssh-agent -s)"
```
进入服务器.ssh目录
```
ssh-add ~/.ssh/id_rsa
```
windows下搜id_rsa生成私钥公钥方法

在.ssh目录下，接着输入授权文件钥匙   
```
vi  authorized_keys
```
输入:wq!回车    这样就创建了授权文件<br>

验证原理： 本地电脑的公钥放到服务器的验证公钥的文件下<br>
当本地电脑登录服务器的时候 ，服务器会去验证提交过来的私钥，以及之前拷贝过来的公钥，当算法匹配之后，才能登录服务器<br>

先切换到本地命令行下，在.ssh目录下`cat id_rsa.pub`打印出公钥，复制公钥<br>
切换到服务器命令行下，进入.ssh目录<br>
`vi authorized_keys`打开授权文件，先按i键，进入输入模式，粘贴刚才复制的公钥，按下esc键，输入:wq!保存<br>

接着在服务器命令行下首先授权这个文件，输入
```
chmod 600 authorized_keys
```
重启ssh服务
```
sudo service ssh restart
```
如果已经操作过sudo，一定时间内不用输入密码<br>
先不要退出，再新开一个命令行  输入
```
ssh yu_manager@公网IP
```
就可以直接登录无需输入密码

### 修改服务器默认登录端口，增强安全性
修改配置文件
```
sudo vi /etc/ssh/sshd_config
```
输入当前账号密码，进入编辑页面，有必要新开一个窗口登录服务器，防止修改错误，忘记了导致无法登录
将port改为xxxx(需要在阿里云服务器安全组配置规则里增加相同的端口号)，确保`UserDns`为no，最后一行添加`AllowUser yu_manager`<br>
按esc，:wq!保存退出<br>
重启ssh服务  
```
sudo service ssh restart 
```

关闭root密码登录，也是增强安全性
```
sudo vi /etc/ssh/sshd_config
```
修改`PermitRootLogin` 为 no，`PasswordAuthentication` 为 no，`PermitEmptyPasswords`为 no
设置以后无论是`ssh root@47.94.211.202`还是`ssh -p 3389 root@47.94.211.202`都登录不进去了


### 配置 iptables 和 Fail2Ban 增强安全防护
暂且跳过，不影响项目部署，主要是为了增强安全性

### 搭建服务器的nodejs环境
更新一下系统
```
sudo apt-get update
```
安装包文件   
```
sudo apt-get install vim openssl build-essential libssl-dev wget curl git
```
打开github 找到`creationix/nvm`<br>
复制`wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.2/install.sh | bash`<br>
新打开窗口登录服务器
```
nvm install v8.1.3
```
```
nvm use v8.1.3
```
指定系统的默认node版本
```
nvm alias default v8.1.3  
```
`node -v`  查看node版本<br>
安装cnpm 
```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```
输入命令  
```
echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
```
安装模块  
```
cnpm i pm2 webpack gulp grunt-cli -g
```

在服务器更目录下  
```
vim app.js
```
编辑文件
```
const http = require("http");

http.createServer(function(req, res) {
   res.writeHead(200, {'Content-Type': 'text/plain'})
   res.end('hello world!');

}).listen(8080)

console.log('server running on http://47.94.211.202:8080/');
```
启动服务
```
node app.js 
```
如果端口无法访问，需要到阿里云控制台安全组配置里面配置TCP端口，自定义TCP，端口范围8080/8080，地址段访问，授权对象 0.0.0.0/0

### 通过pm2让node服务常驻
启动node服务
```
pm2 start app.js
```
查看服务列表
```
pm2 list 
```
查看应用详细信息
```
pm2 show  应用名字
```
查看日志， control + c退出
```
pm2 logs
```

### 配置nginx反向代理 nodejs端口
原理：nodejs不具备权限通过外网访问80端口，用root权限启动对80端口的监听，把来自80端口的流量分配给node服务的另外一个端口，实现node服务的代理，实际上就是把80端口的请求都转发到nodejs启动的8080端口上<br>
先关闭apache服务
```
sudo service apache  stop
```
删除apache服务
```
update-rc.d -f apache2 remove
```
```
sudo apt-get remove apache2
```
更新包列表
```
sudo apt-get update
```
安装nginx
```
sudo apt-get install nginx
```
查看版本
```
nginx -v
```
进入目录`cd /etc/nginx/conf.d` `pwd`查看路径<br>
新建配置文件，文件命名以域名加端口的方式归类，多个项目对应多个服务，考虑负载均衡 
```
sudo vi cypws-cn-8080.conf
```
编辑配置文件
```
upstream cypws {
  server 127.0.0.1:8080;
}

server {
  listen 80;
  server_name 服务器公网ip地址;

  location / {
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forward-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_set_header X-Nginx-Proxy true;
    proxy_pass http://cypws;
    proxy_redirect off;

  }
}
```
返回上一层目录 `/etc/nginx`<br>
编辑配置文件
```
sudo vi nginx.conf
```
确保`include /etc/nginx/conf.d/*.conf;` 注释打开
检测配置文件有没有错误   
```
sudo nginx -t
```
重启nginx服务器    
```
sudo nginx -s reload
```
重启pm2   
```
pm2 restart all
```
在浏览器上直接访问服务器ip地址就可以了<br>
去掉浏览器网络的nginx版本信息，进入/etc/nginx目录
```
sudo vi nginx.conf
```
把`server_tokens off;`注释拿掉<br>
重启服务器`sudo service nginx reload`这样再访问浏览器是，network里的nginx版本信息就不会显示了


### 更改域名的DNS更服务器
### ubuntu安装mongodb
### 往线上mongodb导入数据库
### 为线上数据库配置读取权限
### 从一台服务器迁移数据到另一个线上mongodb
### 数据库备份
### 上传数据库备份到七牛私有云
### 上传项目代码到线上私有git仓库
### 从本地发布上线和更新服务器的nodejs项目
### 项目部署的一般流程总结
### 申请选购SSL证书
