# Ghost

Ghost is a open source blogging platform, can put him as a WordPress a challenger. Now the WordPress can already clearly a bit bloated, both architecture and design and the realization of everywhere shows this is a 10 years ago on the birth of the items, especially the recently updated several versions, almost all just in the function and interaction level improvements are made and can not see the essence of progress and optimization of. So I prefer to the ghost as WordPress's successor. Also, ghost based on JavasSript node.js development. In the foreseeable future, JS undoubtedly than PHP has a more advantages.

simply say that I think that Ghost has several advantages: have or will be: tada:

``` 
(1) From scratch, less than WordPress and other existing blog platform, a lot of historical issues;
(2) Node.js itself is a very good platform;
(3) Using real-time architecture, greatly improve the access experience. While the WordPress front end rely entirely on jQuery, any action requires heavy load pages;
(4) Very simple, Zip installation package is only 1.7M, after the completion of the deployment in the 30M. The operating efficiency of a number of higher than WordPress;
(5) Focus on the content of the blog itself, and do not need users to care about all kinds of problems of the blog platform. Using the Markdown editor, has a very good writing experience;
(6) Build extremely simple (at least Windows local structures are such), it can be said that without any programming foundation, reducing the threshold of writing blog.
(7) Of course as a new blog platform, at this stage there are still a lot of problems. However, taking up to now is still in version 0.3, even the stable version of the first edition are not, visible developers still of the ghost is full of confidence.
(8) The current function is too small, the background can be said to be poor, but in the Ghost developer's blog mentioned in the 0.4 version will be added to the Dashboard function, then the current temporarily as a mini background bar;
(9) Currently supports node.js quotient space is less and less, it is difficult to ghost released out. This page is to illustrate the point, at present only Amazon EC2 digitalocean, Rackspace cloud cloud service providers, Webfaction, Dreamhost and VPS service providers can managed ghost, the cPanel a series of independent host and virtual space is for the PHP services, it can not support the ghost;
(10) The number of themes and plug-ins is far less than the WordPress, the current official website only ten or so for the theme to download or buy, and the quality of the compliment.
(11) Many of the basic function has not yet appeared in the current version 0.3, such as multi user landing, review initiated and. Currently only by Disqus comment system to achieve the comment function. But this is not a big problem, the next version certainly > will give the solution.
```

### Environmental Science
Ubuntu 14.04, 5.5.43 MySql, 1.4.6 Nginx, 0.10.33 Node

### step
Install MySql

### installation MySql

```
$apt-get update # update component
$apt get install mysql-server mysql-client - y # quick install Y represents the default choice y save return, then only need to set the root password MySQL
```

### set MySQL encoding

```
$sudo VI /etc/mysql/my.cnf # search to [mysqld] insert = collation-server utf8 Unicode CI init-connect ='SET names utf8'character-set-server = utf8
$service MySQL restart # restart
MySQL -u root -p $# input above the set password
$variables like'char%'show
Show variables like'collation%'$# check into UTF-8 or after the database is stored in memory Chinese garbled
```

### create database Ghost
```
$create database mousycoder # here mousycoder for you want to change the database name, advice and domain name consistent and convenient maintenance.
$create database mousycoderDev # the ghost starting modes of a development mode is a mode of production is the development model of the database, if you do not want to go to all that trouble, only build a database.
$create user'mousycoder'@'localhost'identified by' 123456 '# here new a user mousycoder password for 123456. Of course my password is certainly not 123456 slightly and change into your own! = =. Also, it is recommended to the user name, database name, service name, group name, and domain name consistent, here is to establish a user with only local operations, can not be operated by remote, security policy.
All privileges grant on mousycoder.* to'mousycoder'@'localhost'
$grant all privileges on mousycoderDev.* to here'mousycoder'@'localhost'# is given mousycoder the local user all of database mousycoder and mousycoderDev permission, of course here you can according to the actual needs of the given permission.
$flush privileges # re read permissions table data into memory, for the permission to take effect without restarting the mysql, the benefits can prevent and correct the mistakes, no room to renege.
```

### Supplementary explanation

```
- MySQL remove the anonymous account, disable root remote login: $mysql_secure_installation; answer N, y, y, y, y, sudo
- Grant usage: grant permissions 1, permissions 2,... Permission on N database name. Table name to user name @ user address by identified 'password'
Which permissions 2, 1,... Permission n on behalf of insert,, update, delete, create, drop, select,
Index, alter, grant, reload, references, shutdown, process, file14 permissions.

* For example: select Insert, `grant, update, delete, create, on mousycoder.employee to drop
Hello@10.163.225.87 identified 'by 123456' '
Representative to the user Hello from the 10.163.225.87 distribution can be on the database employee mousycoder table select, insert, update, delete, create, drop and other operations of the permissions, and set the password for the 123456.
MySQL can refer to reason with the garbage was civil's blog
```

### Install Nginx
 installation nginx
```
$install nginx -y apt-get
Apt-get install curl -y # $curl is a command-line tool that is a network request, and then get to extract data.
Curl -i 127.0.0.1 Nginx $# to ensure the operation, the default listen on port 80
```
 set web directory and cache directory
```
$/var/www MKDIR
$MKDIR -p /var/cache/nginx -p # can all of a sudden there is no intermediate path of the folder with established, very practical
Chown www-data:www-data /var/www nginx $# installation will automatically create a user www-data and use the default user operation
$www-data:www-data /var/cache/nginx chown
```
 (generally do not modify the configuration file using the file)
```
$CP /etc/nginx/nginx.conf /etc/nginx/nginx.conf.old # backup original configuration
$VI /etc/nginx/nginx.conf # can modify the default user to other users
# Ghost create a separate nginx configuration file
$RM /etc/nginx/sites-enabled/default # delete the default configuration
$VI /etc/nginx/sites-available/mousycoder # established a nginx configuration file
```

### Nginx configuration file


```
server {
    listen 0.0.0.0:80; # 监听的端口号
    server_name mousycoder.com; # 把mousycoder.com换成自己的域名，如果没有域名或者网站还没备案下来这里可以写ip,例如120.25.150.209，如果配置多个网站的话，这里可以通过不同的端口对应不同的网站，例如：120.25.150.209:81等 前提是这些端口外网还能访问。
    access_log /var/log/nginx/mousycoder.log;

    location / {
        proxy_set_header Host $http_host;
        proxy_set_header X-NginX-Proxy true;
        proxy_set_header X-Real-IP $remote_addr;

        proxy_pass http://127.0.0.1:2368;  # 这里是Ghost启动时的默认端口，可以根据实际情况变化，默认也可以
        #proxy_buffering off;
        proxy_redirect off;
    }
}
```

### Then restart the service

```
$ ln -s /etc/nginx/sites-available/mousycoder /etc/nginx/sites-enabled/mousycoder # 建立软链接到到实际配置路径，方便统一维护配置文件变化。
$ service nginx restart # nginx安装好时已经默认注册了系统的服务，我们就可以直接重启nginx服务，让配置文件生效
```

### Supplementary explanation

### install Node.Js

Nginx here is mainly to do the role of the port forward mapping, of course, it can be very.
Install Node.Js

```
$ wget http://nodejs.org/dist/v0.10.39/node-v0.10.39-linux-x64.tar.gz
$ tar zxf node-v0.10.39-linux-x64.tar.gz && cd node-v0.10.39-linux-x64
$ cp bin/* /usr/bin # 拷贝执行目录，相当于去设置一个环境变量到用户的bin目录
```

Supplementary explanation

Downloaded here is not the latest version of the nodejs, in order to stabilize.
Ghost official website explained From the beginning of the ghost version 0.6.0, ghost integrity of the Chinese version of package has been integrated with nodejs version 0.12 SQLite3 native libraries, in (32 / 64 bit), windows, and Linux (32 / 64 bit), MAC (64 bit) operating system > can be run directly on the nodejs 0.10.x and 0.12.x version. However, we strongly recommend the use of the latest version of 0.10.x Node.js. Support for the 0.12.x Node.js version remains to be tested!
For details, see ghost Chinese network, of course, there are many kinds of NodeJs installation methods, personal feel that this is the most suitable way here.

### Install Ghost

Downloaded Ghost

```
$ cd /var/www/
$ curl -L http://dl.ghostchina.com/Ghost-0.6.3-zh.zip -o mousycoder.zip
$ unzip mousycoder.zip -d mousycoder
$ cd mousycoder/
```


### Configuration Ghost

Ghost有两种运行模式：开发模式和产品模式，通过config.js配置

```
$ cp config.example.js config.js
$ vi config.js
```

在config.js配置文件里配置

```
- production # 生产模式
    production:{
      url: 'http://mousycoder.com',
      main:{},
      database:{
          client :'mysql',
          connection:{
              host:'127.0.0.1',
              user:'mousycoder',  # 数据库连接的用户
              password:'123456',
              database:'mousycoder',
              charset:'utf-8'
          }
      }
    }

- development # 开发模式
    production:{
      url: 'http://mousycoder.com',
      main:{},
      database:{
          client :'mysql',
          connection:{
              host:'127.0.0.1',
              user:'mousycoder',  # 数据库连接的用户
              password:'123456',
              database:'mousycoderDev',
              charset:'utf-8'
          }
      }
    }
```


### Installation and operation
Depending on the package.json installation package, enter the current mousycoder directory

```
$ cd /var/www/mousycoder
$ npm install --production  # 产品模式；只安装运行的包
$ npm install # 开发模式，默认是开发模式
```
### Run mousycoder with Ghost (non root account to run Ghost more secure)

```
$ adduser -shell /bin/bash --gecos 'mousycoder blog' mousycoder
$ chown -R mousycoder:mousycoder /var/www/mousycoder
```

### Install forever, keep Ghost running in the background

```
$ cd /var/www/mousycoder
$ npm install forever -g # 全局安装forever模块
$ NODE_ENV=production forever start index.js # 生产模式后台运行ghost
```

### Installation system service

```
$ curl https://raw.githubusercontent.com/TryGhost/Ghost-Config/master/init.d/ghost -o /etc/init.d/mousycoder # 下载Ghost提供的脚本到/etc/init.d/目录，该目录是系统服务目录
$ chmod +x /etc/init.d/mousycoder # 给脚本赋予执行权限
$ usermod -aG mousycoder www-data # 把www-data用户加入mousycoder组，让其可以操作源文件等目录
$ update-rc.d mousycoder defaults # 用update-rc.d 安装服务 mousycoder
$ update-rc.d mousycoder enable # 刷新一遍服务,防止之前有重名的
$ service mousycoder status # 查看mousycoder 服务的状态
$ service mousycoder start # 这样开机就会自动启动ghost生产环境，不信reboot一下
```

### 补充说明
```
$ curl -L https://raw.githubusercontent.com/TryGhost/Ghost-Config/master/init.d/ghost -o ghost # -L 解决网站地址自动跳转后拿不到文件
$ curl -v www.baidu.com  # 显示详细过程包含http头
$ curl -u username:password url 解决页面需要授权输入用户名密码情况
$ curl -u username --data "param1=value1&param2=value2" https://api.github.com  # post请求
$ curl -I -X DELETE https://api.github.com  # 解决get post以外的请求方式
$ curl --form "fileupload=@filename.txt" http://hostname/resource  # 上传文件
```
###  Chmod command

Chmod -R A-W ABC $# cancel /abc directory -w (write permissions)
Drwxr-xr-x the first column of the D directory 2-4 column owners have access to the 5-7 RWX column R-X the same group of user rights R-X is other groups of user rights, where RWX corresponds to 4,2,1
### system service startup sequence
```
$A start update-rc.d 5012345 stop 5106
$start 50 1 2 3 4 5 # said in 1, 2, 3, 4 and 5 of the five run level, in order of precedence, ascending, the 50th began to run the script
$stop 50 0 6 # said at 0. 6 the two run level, in sequence, from small to perform, the 52 stop running the script
$update-rc.d mousycoder remove # uninstall mousycoder boot service
start-up
```

Open the browser, enter the configuration of the IP or domain
* Home http://hostname.com/ghost
* Ghost background http://hostname.com/ghost
Now you can enjoy the Ghost to bring you the ultimate simple, quick to write it!


## Ghost.theme

Download packages are the corresponding titles for each subject to download:


1.```Ghost Theme Based on PureCSS``` 

## PureCSS is a set of small, responsive CSS modules that you can use in your various web project.

参考链接: http://purecss.io/ 

![image](http://purecss.io/img/layouts/blog@2x.jpg)

2. ```Solar Theme by Matt Harzewski```

## Solar is best Ghost theme based on the Solarized color palette. It includes stylesheets for the default light-on-dark version and for the alternate dark-on-light themes.

参考链接:https://camo.githubusercontent.com/

[image](https://camo.githubusercontent.com/623d46f1926b771ecb2cbbea9fbbb302cab3851d/687474703a2f2f692e696d6775722e636f6d2f5039344a3639532e706e67)

3. ```Albireo by Marco Santonocito```

## Albireo is a simple theme designed specifically for Ghost which you can download without having to shell extra penny from your pocket.
参考链接:https://github.com/santonocito/albireo| 
![image](https://camo.githubusercontent.com/ef9b29304b2d432a7b320fc5fe392ab03869c31f/68747470733a2f2f7261772e6769746875622e636f6d2f73616e746f6e6f6369746f2f616c626972656f2f6d61737465722f73637265656e73686f742e706e67)


4. ```Linen by Theme Spectre```

## Linen is a Ghost starter theme for developers, designers, and theme builders. It provides a solid foundation for building high quality Ghost themes.
参考链接:http://demo.themespectre.com/   ![image](http://static.open-open.com/news/uploadImg/20131120/20131120090418_326.jpg)


5. ```Aura by Tom Kenny```

## Aura is a simple, user-friendly blog theme for Ghost that is available for free download.
参考链接:http://tomkenny.design/articles/aura-a-free-theme-for-ghost/  ![image](http://inspectelement.com/wp-content/uploads/2013/10/ghost-aura-theme.jpg)

6. ```Ghostwriter by JollyGoodThemes```

##  Ghostwriter is a simple AJAX driven theme for Ghost that is open source theme. It is available on github for free download.
参考链接:https://github.com/roryg/ghostwriter
![image](https://github.com/roryg/ghostwriter/raw/master/screenshot.png?raw=true)

7. ```The Classy by Theme Spectre```

## The Classy as the name clearly says is an elegant, clean, and simple That together speak out loud for it being perfectly balanced design between type and illustrations.
参考链接:http://demo.themespectre.com/#theclassy

![image](http://static.open-open.com/news/uploadImg/20131120/20131120090420_324.jpg)

8. ```Personally by Mr. T. Spectre```

## Personally is a theme that is designed for the individual blogger for reaching the widest possible audience.
参考链接:http://personally.themespectre.com/
![image](http://static.open-open.com/news/uploadImg/20131120/20131120090422_980.jpg)


9. ```Magnum by Durgesh Priyaranjan```

## Magnum is simple and responsive theme for ghost. You can easily download this ghost theme for free and install it to your blog that runs with ghost platform.
参考链接:https://github.com/durgesh-priyaranjan/magnum

![image](http://static.open-open.com/news/uploadImg/20131120/20131120090422_169.jpg)

10. ```GhostScroll by Yonatan Wolowelsky```

##  Designed with the aim to bring you the readability and user experience of Medium while using Ghost to the content and host the stuff wherever you choose, GhostScroll is a fully responsive with oodles of user-friendly features.
参考链接:http://www.svenread.com/readium-ghost-theme/

![image](http://static.open-open.com/news/uploadImg/20131120/20131120090423_143.jpg)

11. ```Ghost UiKIT Theme```

## Ghost UIKit Theme is a stylishly designed Ghost theme that looks little mediocre but is simply brilliant.
参考链接：https://github.com/Ghostrrr/ghost-theme-uikit
![image](https://camo.githubusercontent.com/139f5287144a05d7776c257ba66e671602db84d3/68747470733a2f2f7261772e6769746875622e636f6d2f47686f73747272722f67686f73742d7468656d652d75696b69742f6d61737465722f696d616765732f7468756d626e61696c2e6a7067)

13. ```Vapor by Seth Lilly```

## Vapor is a minimal, typography-focused responsive ghost theme, that comes with a core concept of Ghost and fades into the background thereby bring the entire focus on your content. All in all, Vapor is a pretty nice theme that comes loaded with features which enable the need of customization.
参考链接：https://github.com/sethlilly/Vapor

![image](http://static.open-open.com/news/uploadImg/20131120/20131120090424_188.jpg)

14. ```Ice Breaker Ghost Theme ```

## Ice breaker is a simple yet cool Ghost theme by Justin Klemm.
参考链接：http://justinklemm.com/ghost-blog-theme-ice-breaker/
![image](http://justinklemm.com/wp-content/uploads/2013/10/ice-breaker.png)

15. ```Ghost Theme by Adam Cooke```

##  Ghost Theme is designed by Adam who is a software developer, designer and managing director of aTech Media, Niftyware and Dial 9. It is a fully responsive theme that is worth downloading for it comes loaded with amazing features.
参考链接：https://github.com/adamcooke/ghost-theme
![image](http://static.open-open.com/news/uploadImg/20131120/20131120090427_37.jpg)

