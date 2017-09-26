From alpine:latest
MAINTAINER edward <edward@lxh.tech>
ENV SERVER_PORT=8999 \
PASSWORD=hellword \
METHOD=aes-256-cfb \
TIMEOUT=600 \
LOCAL_PORT=1080 \
SHADOWSOCKS_GO_URL=https://github.com/shadowsocks/shadowsocks-go/releases/download/1.2.1/shadowsocks-server.tar.gz \
SHADOWSOCKS_GO_CONFIG_URL=https://raw.githubusercontent.com/shadowsocks/shadowsocks-go/master/config.json \
ALPINE_VERSION=v3.6 \
TIMEZONE=Asia/Shanghai \
LANG=C.UTF-8 \
LC_ALL=C.UTF-8

RUN mkdir -p /opt/ss
RUN set -x && \
    echo "http://mirrors.aliyun.com/alpine/${ALPINE_VERSION}/main" > /etc/apk/repositories && \
    apk upgrade --update && \
    apk add --no-cache --update-cache libressl bash curl ca-certificates libstdc++ tzdata wget  && \
    #设定时区
    cp /usr/share/zoneinfo/${TIMEZONE} /etc/localtime && \
    echo "${TIMEZONE}" >  /etc/timezone && \
    echo  '219.76.4.4 github-production-release-asset-2e65be.s3.amazonaws.com' >> /etc/hosts && \
    wget -O /opt/ss/shadowsocks-server.tar.gz  ${SHADOWSOCKS_GO_URL} && \
    tar -zxvf /opt/ss/shadowsocks-server.tar.gz && \
#    chmod +x /opt/ss/shadowsocks-server && \ 
    wget -O /opt/ss/config.json ${SHADOWSOCKS_GO_CONFIG_URL} && \
    sed -i s/8388/${SERVER_PORT}/  /opt/ss/config.json && \
    sed -i s/1080/${LOCAL_PORT}/  /opt/ss/config.json && \
    sed -i s/barfoo!/${PASSWORD}/  /opt/ss/config.json && \
    sed -i s/aes-128-cfb-auth/${METHOD}/  /opt/ss/config.json && \
    sed -i s/600/${TIMEOUT}/  /opt/ss/config.json;

EXPOSE $SERVER_PORT

CMD ["/opt/ss/shadowsocks-server","-c","/opt/ss/config.json"]
