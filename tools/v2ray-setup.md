# Endpoint
## option1
https://www.v2ray.com/chapter_00/install.html
```
bash <(curl -L -s https://install.direct/go.sh)
```

## option2
https://github.com/v2fly/fhs-install-v2ray
```
wget https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh

# Debian 9/10, curl is used in the .sh
# apt update
# apt install curl
# if Debian 13, curl is already installed
bash install-release.sh

systemctl enable v2ray
systemctl start v2ray
systemctl status v2ray

vi /usr/local/etc/v2ray/config.json

systemctl stop v2ray
systemctl start v2ray
```

config.json
```json
{
  "log": {
    "loglevel": "warning",
    "access": "/var/log/v2ray/access.log",
    "error": "/var/log/v2ray/error.log"
  },
  "inbounds": [
    {
      "port": 443,
      "protocol": "vmess",
      "settings": {
        "clients": [
          {
            "id": "12341234-1234-1234-1234-123412341234",
            "alterId": 64
          }
        ]
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {
        "domainStrategy": "AsIs"
        # "domainStrategy": "UseIPv4"
      }
    }
  ]
}
```

# Local
```json
{
  "log": {
    "loglevel": "warning"#,
#    "access": "/Users/xx/log/v2ray/access.log",
#    "error": "/Users/xx/log/v2ray/error.log"
  },
  "inbounds": [
    {
      //"listen": "127.0.0.1",
      "port": 1080,
      "protocol": "socks",
      "settings": {
        "auth": "noauth"
      }
    }
    #,{ "port": 1081, "protocol": "http", "settings": { "auth": "noauth" } }
  ],
  "outbounds": [
    {
      "protocol": "vmess",
      "settings": {
        "vnext": [
          {
            "port": 443,
            # "address": "ec2-xx-xx-xx-xx.ap-east-1.compute.amazonaws.com",
            #"address": "xx::1b51",
            "address": "xx::1057",
            "users": [
              {
                "id": "12341234-1234-1234-1234-123412341234",
                "level": 0,
                "alertId": 64
              }
            ]
          }
        ]
      }
    }
  ]
}
```

# AWS ipv6
```json
{
  "log": {
    "loglevel": "warning",
    "access": "/var/log/v2ray/access.log",
    "error": "/var/log/v2ray/error.log"
  },
  "routing": {
    "rules": [
      {
        "type": "field",
        "domain": [
          "full:www.google.com",
          "full:translate.google.com",
          "full:translate.google.us",
          "domain:gmail.com",
          "domain:youtube.com",
          "domain:googlevideo.com",
          "domain:github.io",
          "domain.mozilla.org",

          "domain:javtiful.com",
          "domain:missav.com",
          "domain:spankbang.com",
          "domain:xsijishe.net",

          "domain:v2ray.com",
          "domain:v2fly.org"
        ],
        "inboundTag": [
          "eth0"
        ],
        "outboundTag": "direct"
      },
      {
        "type": "field",
        "domain": [
          "domain:openai.com",
          "domain:chatgpt.com",
          "domain:gstatic.com",
          "full:bard.google.com",
          "full:gemini.google.com",
          "domain:github.com",
          "domain:githubassets.com",
          "domain:github.blog",
          "domain:stackoverflow.com",
          "domain:twitter.com",
          "domain:x.com"
        ],
        "inboundTag": [
          "eth0"
        ],
        "outboundTag": "us-proxy-racknerd"
      },
      {
        "type": "field",
        "inboundTag": [
          "eth1"
        ],
        "outboundTag": "us-proxy-racknerd"
      }
    ]
  },
  "inbounds": [
    {
      "port": 443,
      "listen": "xx:eth0:1b51",
      "protocol": "vmess",
      "settings": {
        "clients": [
          {
            "id": "12341234-1234-1234-1234-123412341234",
            "alterId": 64
          }
        ]
      },
      "tag": "eth0"
    },
    {
      "port": 443,
      "listen": "xx:eth1:1057",
      "protocol": "vmess",
      "settings": {
        "clients": [
          {
            "id": "12341234-1234-1234-1234-123412341234",
            "alterId": 64
          }
        ]
      },
      "tag": "eth1"
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "tag": "direct",
      "settings": {}
    },
    {
      "protocol": "vmess",
      "tag": "us-proxy-racknerd",
      "settings": {
        "vnext": [
          {
            "address": "xx:racknerd:2",
            "port": 443,
            "users": [
              {
                "id": "12341234-1234-1234-1234-123412341234",
                "alertId": 64
              }
            ]
          }
        ]
      }
    }
  ]
}
```
### update service on AWS
```
ps -aux | grep v2

sudo vi /usr/local/etc/v2ray/config.json

sudo systemctl stop v2ray
sudo systemctl start v2ray

ps -aux | grep v2
```
