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
windows系统下载putty客户端 登陆ssh命令行 
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
### 配置通过ssh实现无密码登录
### 修改服务器默认登录端口，增强安全性
### 配置 iptables 和 Fail2Ban 增强安全防护
### 搭建服务器的nodejs环境
### 通过pm2让node服务常驻
### 配置nginx反向代理 nodejs端口
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
