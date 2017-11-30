### Plz click [here](https://www.aihoom.com/1274.htmla) ro view the original site.<br>

# #教程#v2ray+nginx+tls安全的爱国方式


## 环境
1.    本次教程中使用的是Debian8 × 64 mini

2.    SSH连接工具

3.    一台VPS

4.    域名

## update the system
`apt udpate && apt full-upgrade -y`

## 安装V2ray

因为本次的教程是mini系统，所以一些常规包并不具备，这里还需要安装unzip之类<br>

`apt install unzip daemon curl`

之后再执行

`bash <(curl -L -s https://install.direct/go.sh)`

## 安裝 EasyEngine

要想使用上述TLS以及nginx，只需要EasyEngine便能解决，它更加方便

`wget -qO ee rt.cx/ee && sudo bash ee`

中间需要输入NAME以及你的邮箱 完成setup以后输入

`source /etc/bash_completion.d/ee_auto.rc`

## 安装Nginx

`ee site create example.com --html --letsencrypt`

其中example.com是你的域名

`cd /var/www/example.com/conf/nginx`

进入nginx的conf

`vi v2ray.conf`

编辑配置文件如下

```
location /enterv2ray/ {
proxy_redirect off;
proxy_pass http://127.0.0.1:11054; #此处的11054为你的v2ray端口。请自己修改
proxy_http_version 1.1;
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection "upgrade";
proxy_set_header Host $http_host;
}
```
`cp /var/www/html/index.nginx-debian.html /var/www/example.com/htdocs/index.html && chown www-data: /var/www/example.com/htdocs/index.html`

重启nginx

`service nginx restart`

## 设置v2ray

备份之前数据

`cp /etc/v2ray/config.json /etc/v2ray/config.json.bak``

修改config服务端

`rm /etc/v2ray/config.json && vi /etc/v2ray/config.json`

将其中的端口port以及UUID修改成你自己的\

```
{
    "log": {
            "access": "/var/log/v2ray/access.log",
            "error": "/var/log/v2ray/error.log",
            "loglevel": "warning"
    },
    "inbound": {
            "port": 1025,
            "protocol": "vmess",
            "settings": {
                    "clients": [{
                            "id": "061dc924-dbe0-4175-953e-2b7e6255c86c",
                            "level": 1,
                            "alterId": 64,
                            "security": "auto"
                    }]
            },
            "streamSettings":{
                    "network":"ws",
                    "security": "auto",
                    "wsSettings":{
                            "connectionReuse": true,
                            "path": "/enterv2ray/"
                            }
                    }
    },
    "outbound": {
            "protocol": "freedom",
            "settings": {}
    },
    "outboundDetour": [
        {
            "protocol": "blackhole",
            "settings": {},
            "tag": "blocked"
        }
    ],
    "routing": {
        "strategy": "rules",
        "settings": {
            "rules": [
                {
                    "type": "field",
                    "ip": [
                        "0.0.0.0/8",
                        "10.0.0.0/8",
                        "100.64.0.0/10",
                        "127.0.0.0/8",
                        "169.254.0.0/16",
                        "172.16.0.0/12",
                        "192.0.0.0/24",
                        "192.0.2.0/24",
                        "192.168.0.0/16",
                        "198.18.0.0/15",
                        "198.51.100.0/24",
                        "203.0.113.0/24",
                        "::1/128",
                        "fc00::/7",
                        "fe80::/10"
                    ],
                    "outboundTag": "blocked"
                }
            ]
        }
    }
}
```
#### 如果你想获取一个全新的UUID请访问https://www.uuidgenerator.net/
#### 或者使用https://htfy96.github.io/v2ray-config-gen/ 配置全新的v2ray服务端以及客户端




##### My thanks to @EasonDIn in the group chat in telegrams.
