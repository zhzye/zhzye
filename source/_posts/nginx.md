---
title: nginx
tags:
  - web
  - nginx
originContent: "# nginx\n\n## what\n\n- Web服务器\n- 反向代理服务器\n- 电子邮件（IMAP/POP3/SMTP）代理服务器\n\n### with java\n\nNginx反向代理服务器（负载均衡）\n\nNginx + Tomcat\n\n- 动态静态分离\n\t- Nginx处理静态页面\n\t- Tomcat处理JSP页面\n\n#### 安装java@centos7\n\n``` shell\nyum search java-1.8\nyum -y install java-1.8.0-openjdk-devel.x86_64\nvim /etc/profile\nsource /etc/profile\n```\n\n设置环境变量\n``` shell\nexport JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.191.b12-1.el7_6.x86_64\nexport PATH=$JAVA_HOME/bin:$PATH\nexport CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar\n```\n\n#### 安装tomcat\n\n``` shell\nwget http://mirrors.shu.edu.cn/apache/tomcat/tomcat-9/v9.0.14/bin/apache-tomcat-9.0.14.zip\nyum install unzip\nunzip apache-tomcat-9.0.14.zip\nchmod +x /usr/local/tomcat/bin/*.sh\nsh /usr/local/tomcat/bin/startup.sh\n```\n\n打开浏览器，输入：http://机器IP地址:8080/\n\n#### 配置tomcat\n\n``` shell\nvim /usr/local/tomcat/conf/tomcat-users.xml\n```\n\n增加权限\n``` xml\n<role rolename=\"manager-gui\"/>\n<role rolename=\"admin-gui\"/>\n<user username=\"tomcat\" password=\"1\" roles=\"admin-gui,manager-gui\"/>\n```\n\n重启\n``` shell\nsh /usr/local/tomcat/bin/shutdown.sh\nsh /usr/local/tomcat/bin/startup.sh\n```\n\nip权限赋予\n\n``` shell\nvim  -O /usr/local/tomcat/webapps/manager/META-INF/context.xml  /usr/local/tomcat/webapps/host-manager/META-INF/context.xml\n```\n\n``` xml\n<Valve className=\"org.apache.catalina.valves.RemoteAddrValve\"                                       |rg\\.apache\\.catalina\\.filters\\.CsrfPreventionFilter\\$LruCache(?:\\$1)?|java\\.util\\.(?:Linked)?HashMap\"\n         allow=\"^.*$\"/>\n```\n\n#### 安装nginx\n``` shell\n# 下载源码\nwget http://120.52.51.15/nginx.org/download/nginx-1.15.8.tar.gz\n\n# 为编译安装gcc\nyum -y install gcc-c++\n\n# configure\n ./configure --prefix=/usr/local/nginx --with-http_ssl_module --with-http_gzip_static_module --with-http_stub_status_module\n\n# ./configure: error: the HTTP rewrite module requires the PCRE library.\nYou can either disable the module by using --without-http_rewrite_module\noption\nyum -y install pcre-devel openssl openssl-devel\n```\n\n### 内部组成\n\n- `内核`和`模块`\n- 模块\n\t- 核心模块\n\t\t-  HTTP模块、EVENT模块和MAIL模块\n\t- 基础模块\n\t\t- HTTP Access模块、HTTP FastCGI模块、HTTP Proxy模块和HTTP Rewrite模块\n\t- 第三方模块\n\t\t- HTTP Upstream Request Hash模块、Notice模块和HTTP Access Key模块及用户自己开发的模块\n\n\n# 参看资料\n\nhttps://segmentfault.com/a/1190000007803704\n"
categories:
  - web
toc: false
date: 2019-02-07 10:28:49
---

# nginx

## what

- Web服务器
- 反向代理服务器
- 电子邮件（IMAP/POP3/SMTP）代理服务器

### with java

Nginx反向代理服务器（负载均衡）

Nginx + Tomcat

- 动态静态分离
	- Nginx处理静态页面
	- Tomcat处理JSP页面

#### 安装java@centos7

``` shell
yum search java-1.8
yum -y install java-1.8.0-openjdk-devel.x86_64
vim /etc/profile
source /etc/profile
```

设置环境变量
``` shell
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.191.b12-1.el7_6.x86_64
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```

#### 安装tomcat

``` shell
wget http://mirrors.shu.edu.cn/apache/tomcat/tomcat-9/v9.0.14/bin/apache-tomcat-9.0.14.zip
yum install unzip
unzip apache-tomcat-9.0.14.zip
chmod +x /usr/local/tomcat/bin/*.sh
sh /usr/local/tomcat/bin/startup.sh
```

打开浏览器，输入：http://机器IP地址:8080/

#### 配置tomcat

``` shell
vim /usr/local/tomcat/conf/tomcat-users.xml
```

增加权限
``` xml
<role rolename="manager-gui"/>
<role rolename="admin-gui"/>
<user username="tomcat" password="1" roles="admin-gui,manager-gui"/>
```

重启
``` shell
sh /usr/local/tomcat/bin/shutdown.sh
sh /usr/local/tomcat/bin/startup.sh
```

ip权限赋予

``` shell
vim  -O /usr/local/tomcat/webapps/manager/META-INF/context.xml  /usr/local/tomcat/webapps/host-manager/META-INF/context.xml
```

``` xml
<Valve className="org.apache.catalina.valves.RemoteAddrValve"                                       |rg\.apache\.catalina\.filters\.CsrfPreventionFilter\$LruCache(?:\$1)?|java\.util\.(?:Linked)?HashMap"
         allow="^.*$"/>
```

#### 安装nginx
``` shell
# 下载源码
wget http://120.52.51.15/nginx.org/download/nginx-1.15.8.tar.gz

# 为编译安装gcc
yum -y install gcc-c++

# configure
 ./configure --prefix=/usr/local/nginx --with-http_ssl_module --with-http_gzip_static_module --with-http_stub_status_module

# ./configure: error: the HTTP rewrite module requires the PCRE library.
You can either disable the module by using --without-http_rewrite_module
option
yum -y install pcre-devel openssl openssl-devel
```

配置nginx
``` xml
upstream tomcats {
        server localhost:8080 weight=1;
    }

    server {
        listen 80;
        server_name blog.zhzye.com;

        location ~ \.jsp$ {
            index index.jsp;
            proxy_pass http://tomcats;
            proxy_redirect off;
        }

        location ~ \.(htm|js|css|gif|png|jpg|bmp|swf|svg)$ {
            expires 30d;
            root /usr/local/tomcat/webapps/ROOT;
        }
    }
```

### 内部组成

- `内核`和`模块`
- 模块
	- 核心模块
		-  HTTP模块、EVENT模块和MAIL模块
	- 基础模块
		- HTTP Access模块、HTTP FastCGI模块、HTTP Proxy模块和HTTP Rewrite模块
	- 第三方模块
		- HTTP Upstream Request Hash模块、Notice模块和HTTP Access Key模块及用户自己开发的模块


# 参看资料

https://segmentfault.com/a/1190000007803704
