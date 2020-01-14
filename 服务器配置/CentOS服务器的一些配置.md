> 服务器环境：CentOs 7.3 64位

## 设置ssh连接的公钥和私钥以及免密登录

在根目录查看有没有.ssh文件夹

```shell
[root@iZbp14zjtyipyqb6lkevt9Z ~]# ls -a
```

如果有 .ssh 文件夹，那么进入 .ssh，查看有没有 id_rsa 和  id_rsa.pub 文件

```shell
[root@iZbp14zjtyipyqb6lkevt9Z .ssh]# ls -a
.  .. id_rsa  id_rsa.pub
```

如果都有上面提到的文件，则说明已经生成过了公钥和私钥，不必进行下面的操作的；

如果没有 .ssh 或者没有 id_rsa 和  id_rsa.pub，则进行下面的操作

```shell
[root@iZbp14zjtyipyqb6lkevt9Z .ssh]# ssh-keygen -t rsa -b 4096 -C "1329105041@qq.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again:
```

执行了上面的指令后，一路回车。然后在查看根目录，会发现多出了 .ssh 文件夹及其目录下的 id_rsa 和  id_rsa.pub 文件

```shell
[root@iZbp14zjtyipyqb6lkevt9Z .ssh]# ls -a
.  .. id_rsa  id_rsa.pub
```

然后将其加入代理

```shell
[root@iZbp14zjtyipyqb6lkevt9Z .ssh]# eval "$(ssh-agent -s)"
Agent pid 19771
[root@iZbp14zjtyipyqb6lkevt9Z .ssh]# ssh-add ~/.ssh/id_rsa
Identity added: /root/.ssh/id_rsa (/root/.ssh/id_rsa)
```

 如果在 .ssh文件夹下没有 authorized_keys 文件，则可以进入 .ssh 文件夹进行生成，如果有该文件，则忽略此次操作

```shell
[root@iZbp14zjtyipyqb6lkevt9Z .ssh]# vi authorized_keys
```

执行完上面的指令后，会进入到 authorized_keys 的编辑页面，然后将本地电脑的公钥粘贴到此文件中。然后退出该文件。经过上述操作后，会发现 .ssh 文件夹多了一个 authorized_keys 文件。authorized_keys 文件是一个授权文件（此时该文件是在远程的服务器上），如果本地电脑想要登录该服务器，那么将本地电脑的公钥存放在该服务器的 authorized_keys 文件里。当本地电脑登录该服务器时，服务器会验证本地电脑的私钥以及刚才在 authorized_keys 文件保存的公钥，当算法匹配之后，本地电脑才可以**免密登录**到该服务器。

接着给 authorized_keys 文件设置权限

```shell
[root@iZbp14zjtyipyqb6lkevt9Z .ssh]# chmod 600 authorized_keys
```

重启 ssh 服务

```shell
[root@iZbp14zjtyipyqb6lkevt9Z .ssh]# sudo service ssh restart
```



现在在**本地电脑**（此处是 mac 的终端）可以免密登录服务器

```shell
~ ssh root@iZbp14zjtyipyqb6lkevt9Z
```

执行上面的指令即可**免密连接**到服务器。



## 安装nodejs环境

> 服务器环境：CentOS 7.3 64位
>
> 如无特殊说明，以下操作都是在该环境下配置

安装一些常用依赖

```shell
sudo yum install vim openssl build-essential libssl-dev wget curl git
```

安装 node 版本管理器（[nvm-sh/*nvm*](https://github.com/nvm-sh/nvm)）

```shell
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.0/install.sh | bash
```

安装对应的 node 版本（此处安装的是v10.16.3）

```shell
nvm install v10.16.3
# 查看 node 版本
node -v
nvm use v10.16.3
nvm alias default v10.16.3
```

使用 registry 参数，让 npm 在淘宝镜像源下载

```shell
npm --registry=https://registry.npm.taobao.org install -g npm
```

或者直接给 npm 的下载源设置为淘宝的镜像源，这样每次用 npm 下载的时候都是在淘宝镜像源下载的

```shell
npm config set registry https://registry.npm.taobao.org
```

也可以还原官方的下载源

```shell
npm config set registry https://registry.npmjs.org/
```



增加系统文件的监控数目

```shell
echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
```

为了保证更快更稳定的安装速度，有时候可以采用 cnpm 替代 npm

```shell
npm --registry=https://registry.npm.taobao.org install -g cnpm
```

如果网络不是连不上或者太慢的情况下，推荐使用 npm；如果一些模块的确是拉取不到，是可以用 cnpm 进行同步的，比如，现在想安装 koa，如果 npm 安装不上的话，可以通过 cnpm 同步一下这个模块，它会强制从 npm 那里把模块拿到国内的镜像中去。

```shell
cnpm sync koa
```

安装常见模块

```shel
npm i pm2 webpack gulp grunt-cli -g
```



测试 node 环境

开放你所要测试的服务器端口，操作详见 [阿里云服务器配置开放端口](https://www.jianshu.com/p/fc9012820f08) ，也可以编写 [iptable](#iptable) 来开放一些端口，但这样做会比较麻烦，建议还是使用前者配置方法

写一个 app.js 文件，然后 `ndoe app.js` 运行该文件

```js
// app.js
const http = require('http')

http.createServer(function(req, res) {
  res.writeHead(200, { 'Content-Type': 'text/plain;charset=utf8' })
  res.end('这是首页')
}).listen(8081)

console.log('server running on http://172.16.142.162:8081')
```

然后在本地浏览器访问 `http://172.16.142.162:8081` ，如果浏览器页面出现 “这是首页” 的字样，说明 node 环境搭建成功。



## pm2

pm2 能够让 node 程序在服务器中稳定的运行，即使关闭了连接服务器的会话窗口，node 程序也还在服务器中稳定的运行。

```shell
# 运行 app.js
pm2 start app.js

# 列出当前服务器正在运行的 node 服务
pm2 list

# 查看某个 node 服务的详细信息，例如，app.js
pm2 show app

# 查看当前的实时日志
pm2 logs

# 停止所有的应用程序
pm2 stop all                  

# 停止 id为 0的指定应用程序
pm2 stop 0                   
```



## nginx

安装 nginx

```shell
sudo yum install nginx
```

配置nginx

因为在 etc/nginx/nginx.conf 的文件里有下面这一句

```shell
# Load modular configuration files from the /etc/nginx/conf.d directory.
# See http://nginx.org/en/docs/ngx_core_module.html#include
# for more information.
include /etc/nginx/conf.d/*.conf;
```

所以把自定义的配置文件（例如：vigor666-com-8081.conf）放在 /etc/nginx/conf.d 这个目录下都是有效的。

首先进入 etc/nginx 目录下的conf.d 文件夹，然后创建配置文件 vigor666-com-8081.conf（我的命名规则：域名-域名后缀-端口号），

```shell
cd /etc/ngnix/conf.d
vi vigor666-com-8081.conf
```



配置  vigor666-com-8081.conf 文件

```shell
upstream vigor666 {
  server 127.0.0.1:8081;
}

server {
  listen 80;
  server_name 47.98.233.30;

  location / {
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forward-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_set_header X-Nginx-Proxy true;
    proxy_pass http://vigor666;
    proxy_redirect off;
  }
}
```

此处的配置是将服务器的 8081端口映射到公网 ip 地址的 80 端口，则本地电脑访问公网 ip 地址的 80 端口时，实际上访问的是服务器上的 8081端口的服务

> 注意：要记得开放服务器的 80 端口访问权限，否则是访问不到服务器的 80 端口的



配置完 vigor666-com-8081.conf 文件后，返回上一层目录（即 nginx 目录），

```shell
[root@iZbp14zjtyipyqb6lkevt9Z conf.d]# cd ../
[root@iZbp14zjtyipyqb6lkevt9Z nginx]# sudo vi nginx.conf
```

查看 nginx.conf 文件里面的 `include /etc/nginx/conf.d/*.conf;` 有没有去掉注释，如果没有去掉注释，则应该去掉注释，其他不用更改

```shell
# for more information.
    include /etc/nginx/conf.d/*.conf;
```

测试刚才的 vigor666-com-8081.conf 文件有没有配置成功

```shell
[root@iZbp14zjtyipyqb6lkevt9Z conf.d]# sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

出现上面情况说明已成功配置



重启 nginx

```shell
[root@iZbp14zjtyipyqb6lkevt9Z nginx]# sudo nginx -s reload
nginx: [error] invalid PID number "" in "/run/nginx.pid"
```

上面的执行出现错误，此时应该先执行 `nginx -c /etc/nginx/nginx.conf` ，再执行 `sudo nginx -s reload`

，这样就不会错了。



## iptable



```shell
*filter 
:INPUT ACCEPT [0:0] 
:FORWARD ACCEPT [0:0] 
:OUTPUT ACCEPT [0:0] 
-A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT 
-A INPUT -p icmp -j ACCEPT 
-A INPUT -i lo -j ACCEPT 

#ssh port 
-A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT 

#vsftpd 
-A INPUT -p TCP --dport 61001:62000 -j ACCEPT 
-A OUTPUT -p TCP --sport 61001:62000 -j ACCEPT 
-A INPUT -p TCP --dport 20 -j ACCEPT 
-A OUTPUT -p TCP --sport 20 -j ACCEPT 
-A INPUT -p TCP --dport 21 -j ACCEPT 
-A OUTPUT -p TCP --sport 21 -j ACCEPT 

#mysql port 
-A INPUT -p tcp -m tcp --dport 3306 -j ACCEPT 

#tomcat remote debug port 
-A INPUT -p tcp -m tcp --dport 5005 -j ACCEPT 
-A INPUT -p tcp -m tcp --dport 8080 -j ACCEPT 

#nginx (allow http https)
-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT
-A INPUT -p tcp -m tcp --dport 80 -j ACCEPT 
-A INPUT -j REJECT --reject-with icmp-host-prohibited 
-A FORWARD -j REJECT --reject-with icmp-host-prohibited 

# 以下是在玉山基础上增加的，没有验证是否正确
# allow out traffic
-A OUTPUT -j ACCEPT

# ping
-A INPUT -m limit --limit 5/min -j LOG --log-prefix "iptable denied:" --log-level 7

# mongodb connect
-A INPUT -s 127.0.0.1 -p tcp --destination-port 27017 -m state --state NEW,ESTABLISHED -j ACCEPT
-A OUTPUT -d 127.0.0.1 -p tcp --source-port 27017 -m state --state ESTABLISHED -j ACCEPT

# drop incoming sensitive connections
-A INPUT -p top --dport 80 -i eth0 -m state --state NEW -m recent --set
-A INPUT -p tcp --dport 80 -i eth0 -m state --state NEW -m recent --update --seconds 60 --hitcount 150 -j DROP

# reject all other inbound
-A 	INPUT -j REJECT
-A FORWARD -j REJECT

COMMIT
```



## MongoDB

详见：[Install MongoDB Community Edition on Red Hat Enterprise or CentOS Linux](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/#install-mongodb-community-edition-on-red-hat-enterprise-or-centos-linux) 

使用.rpm包**（推荐）**

1. 配置程序包管理系统（yum）。

   创建一个 `/etc/yum.repos.d/mongodb-org-4.2.repo` 文件，以便您可以使用yum直接安装MongoDB：

   ```shell
   [mongodb-org-4.2]
   name=MongoDB Repository
   baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.2/x86_64/
   gpgcheck=1
   enabled=1
   gpgkey=https://www.mongodb.org/static/pgp/server-4.2.asc
   ```

   > 注意：
   >
   > 您可以在存储库本身中找到每个发行版的.repo文件。 编号为奇数的次要发行版本（例如3.5）是开发版本，不适合生产使用。

2. 安装MongoDB软件包。

   要安装最新的稳定版MongoDB，请执行以下命令：

   ```shell
   sudo yum install -y mongodb-org
   ```

   另外，要安装特定版本的MongoDB，请分别指定每个组件包，并将版本号附加到包名中，如以下示例所示：

   ```shell
   sudo yum install -y mongodb-org-4.2.0 mongodb-org-server-4.2.0 mongodb-org-shell-4.2.0 mongodb-org-mongos-4.2.0 mongodb-org-tools-4.2.0
   ```

   您可以指定任何可用的MongoDB版本。 但是，当有新版本可用时，yum会升级软件包。 为防止意外升级，请固定包装。 要固定软件包，请在 `/etc/yum.conf` 文件中添加以下exclude指令：

   ```shell
   exclude=mongodb-org,mongodb-org-server,mongodb-org-shell,mongodb-org-mongos,mongodb-org-tools
   ```



使用Tarballs

预备条件

MongoDB .tar.gz tarball需要安装以下依赖项：

```shell
yum install libcurl openssl
```

安装过程

1. 下载MongoDB .tar.gz tarball。

   从[MongoDB下载中心](https://www.mongodb.com/download-center/community?jmp=docs)下载适用于您系统的tarball。

2. 从下载的档案中提取文件。

   例如，从系统外壳程序中，可以使用tar命令提取：

   ```shell
   tar -zxvf mongodb-linux-*-4.2.0.tgz
   ```

3. 确保二进制文件在PATH环境变量中列出的目录中。

   MongoDB二进制文件位于tarball的 `bin/` 目录中。 您必须：

   - 将这些二进制文件复制到PATH变量中列出的目录中，例如 `/ usr / local / bin` ，
   - 从**PATH**变量中列出的目录中创建指向这些二进制文件中每个文件的符号链接
   - 或者修改用户的PATH环境变量以包含此目录。

   例如，您可以将以下行添加到Shell的初始化脚本中（例如，〜/ .bashrc）：

   ```shell
   export PATH=<mongodb-install-directory>/bin:$PATH
   ```

   将 `<mongodb-install-directory>` 替换为提取的MongoDB归档文件的路径。



开启 MongoDB 服务

```shell
sudo service mongod start
```

查看 MongoDB 的日志，看是否已经开启了 MongoDB 的服务

```shell
cat /var/log/mongodb/mongod.log
```

尝试连接 MongoDB

```shell
mongo
```

终止MongoDB服务

```shell
sudo service mongod stop
```

重启 MongoDB 服务

```shell
sudo service mongod restart
```



## yarn

在CentOS，Fedora和RHEL上，您可以通过我们的RPM软件包存储库安装Yarn。

```shell
curl --silent --location https://dl.yarnpkg.com/rpm/yarn.repo | sudo tee /etc/yum.repos.d/yarn.repo
```

如果尚未安装Node.js，则还应该配置NodeSource存储库：

```shell
curl --silent --location https://rpm.nodesource.com/setup_8.x | sudo bash -
```

然后，您可以简单地：

```shell
sudo yum install yarn
## OR ##
sudo dnf install yarn
```

环境变量设置

如果未在 PATH 环境变量中找到 yarn，请按照以下步骤添加 yarn 到 PATH 环境变量中，使其可以随处运行。

注意：您的配置文件可能是 `.profile`、`.bash_profile`、`.bashrc`、`.zshrc` 等。

1. 将此项加入您的配置文件： `export PATH="$PATH:/opt/yarn-[version]/bin"` （路径可能根据您安装 Yarn 的位置而有差异）
2. 在终端中，执行登录并登出以使更改生效

为了可以全局访问 Yarn 的可执行文件，你需要在您的终端中设置 `PATH` 环境变量。若要执行此操作，请添加 `export PATH="$PATH:`yarn global bin`"` 到您的配置文件中。

通过如下命令测试 Yarn 是否安装成功：

```shell
yarn --version
```



## SSL 证书

可免费申请的 SSL 证书的平台有：[又拍云](https://www.upyun.com/products/ssl)、[腾讯云](https://cloud.tencent.com/document/product/400/8422)、[阿里云](https://common-buy.aliyun.com/?spm=5176.2020520163.cas.4.646b56a7JJXklA&commodityCode=cas#/buy)、[七牛云](https://www.qiniu.com/ssl)。

服务器帮助文档：https://help.aliyun.com/?spm=5176.8097504.fszjobuve.19.2ec76fb5rFu3lS





















