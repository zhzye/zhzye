---
title: sss
tags:
  - sss
originContent: |-
  ``` shell
   5  yum yum -y install epel-release
      6  yum -y install epel-release
      7   yum -y install python-pip
      8  yum -y install python-pip
      9  pip install shadowsocks
     10  cat /etc/shadowsocks.json
     11  vim /etc/shadowsocks.json
     12  ssserver -d start -c /etc/shadowsocks.json
  ```


  ``` xml
  {
      "server":"ip",
      "server_port":port,
      "local_address": "127.0.0.1",
      "local_port":1080,
      "password":"password",
      "timeout":300,
      "method":"aes-256-cfb",
      "fast_open": false
  }
  ```
categories:
  - 漫天说
toc: false
date: 2019-02-06 10:27:39
---

``` shell
yum -y install epel-release
yum -y install epel-release
yum -y install python-pip
yum -y install python-pip
pip install shadowsocks
cat /etc/shadowsocks.json
vim /etc/shadowsocks.json
ssserver -d start -c /etc/shadowsocks.json
```


``` xml
{
    "server":"ip",
    "server_port":port,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"password",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false
}
```