From alpine:latest
MAINTAINER edward <edward@lxh.tech>
ARG REGION=zh_CN
ARG REGION_CODE=UTF-8
ARG ALPINE_VERSION=v3.6
ARG MYSQL_VERSION=5.7.19
ARG MYSQL_MAJOR=5.7
ARG CFLAGS=-DSIGEV_THREAD_ID=4
ARG IS_CLEAN=default

ENV PS1='[\u@\h \W]\$' \
LANG=zh_CN.UTF-8 \
LC_ALL=zh_CN.UTF-8 \
TIMEZONE=Asia/Shanghai
 
ARG MYSQL_DOWNLOAD_VERSION=https://dev.mysql.com/get/Downloads/MySQL-${MYSQL_MAJOR}/mysql-boost-${MYSQL_VERSION}.tar.gz

RUN mkdir -p /tmp /opt

COPY patch/fix_posix_timers.patch /fix_posix_timers.patch
ADD conf/master.cnf /etc/my.cnf
COPY docker-entrypoint.sh /entrypoint.sh
COPY healthcheck.sh /healthcheck.sh

#Setting new repositories
RUN echo "http://mirrors.aliyun.com/alpine/${ALPINE_VERSION}/main" > /etc/apk/repositories && \
echo "http://mirrors.aliyun.com/alpine/${ALPINE_VERSION}/community" >> /etc/apk/repositories && \
echo "https://mirror.tuna.tsinghua.edu.cn/alpine/${ALPINE_VERSION}/community" >> /etc/apk/repositories && \
echo "https://mirror.tuna.tsinghua.edu.cn/alpine/${ALPINE_VERSION}/main" >> /etc/apk/repositories && \
echo "net.core.somaxconn = 3072" >> /etc/sysctl.conf && \
echo "net.ipv4.tcp_max_syn_backlog = 4096" >> /etc/sysctl.conf && \
echo "net.ipv4.conf.default.rp_filter = 0" >> /etc/sysctl.conf && \
echo "net.ipv4.tcp_keepalive_time = 120" >> /etc/sysctl.conf && \
echo "fs.file-max = 2097152" >> /etc/sysctl.conf && \
apk upgrade --update && \
apk add --no-cache  bash curl ca-certificates libstdc++ tzdata wget cmake automake gcc make libtirpc-dev boost linux-headers rpcgen  build-base ncurses-dev bison libxml2-dev libressl libevent-dev libc-dev libedit-dev perl pwgen libaio-dev boost-dev makepasswd && \
echo "export LANG=${REGION}.${REGION_CODE}" > /etc/profile.d/startup.sh && \
echo "export LC_ALL=${REGION}.${REGION_CODE}" >> /etc/profile.d/startup.sh && \
cp /usr/share/zoneinfo/${TIMEZONE} /etc/localtime && \
echo "${TIMEZONE}" >  /etc/timezone && \
wget -c -O /tmp/mysql.tar.gz ${MYSQL_DOWNLOAD_VERSION} && \
tar -zxvf /tmp/mysql.tar.gz -C /opt && \
ln -s -f /usr/include/tirpc/rpc /usr/include/rpc && \
ln -s -f /usr/include/tirpc/netconfig.h  /usr/include/netconfig.h && \
mv /fix_posix_timers.patch /opt/mysql-${MYSQL_VERSION}/ && \ 
cd /opt/mysql-${MYSQL_VERSION} && \
cmake -DCMAKE_INSTALL_PREFIX=/opt/mysql -DMYSQL_UNIX_ADDR=/tmp/mysql.sock -DDEFAULT_CHARSET=utf8mb4 -DDEFAULT_COLLATION=utf8mb4_general_ci -DEXTRA_CHARSETS=all -DWITH_INNOBASE_STORAGE_ENGINE=1 -DMYSQL_DATADIR=/opt/mysql/data -DMYSQL_TCP_PORT=3306 -DENABLED_LOCAL_INFILE=1 -DENABLED_PROFILING=1 -DWITH_PERFSCHEMA_STORAGE_ENGINE=1 -DWITH_READLINE=1 -DWITH_BOOST=boost -DWITH_EMBEDDED_SHARED_LIBRARY=OFF -DWITH_EMBEDDED_SERVER=OFF -DWITH_PARTITION_STORAGE_ENGINE=1 -DWITH_FEDERATED_STORAGE_ENGINE=1 -DWITH_BLACKHOLE_STORAGE_ENGINE=1 && \
patch -p0 mysys/posix_timers.c  < fix_posix_timers.patch && \
make && make install && make clean && \
mkdir -p /opt/mysql/logs/innodb /opt/mysql/data /opt/mysql/tmp /opt/mysql/mysql-files /docker-entrypoint-initdb.d && \
adduser mysql -D -H -s /sbin/nologin && \
chown -R mysql:mysql /opt/mysql && \
for file in /opt/mysql/bin/* ; do echo "$file"  | awk '{split($0,a,"/"); print a[5]}' | while read binname ; do ln -s -f $file /usr/bin/$binname ; done  ; done && \
cd / && apk del tzdata cmake automake gcc make rpcgen build-base ncurses-dev bison perl libxml2-dev libevent-dev libc-dev libedit-dev perl pwgen boost-dev linux-headers libstdc++ && \
rm -rf /opt/mysql-${MYSQL_VERSION} /tmp/* /var/cache/apk/* && \
sed -i -e "s/bin\/ash/bin\/bash/" /etc/passwd && \
#export PATH=/opt/mysql/bin:$PATH && \
#export PS1='[\u@\h \W]\$'
if [ "${IS_CLEAN}" == "default" ]; then \
  rm -rf /opt/mysql/mysql-test; \
fi

#ENV REGION= \
#REGION_CODE= \
#ALPINE_VERSION= \
#MYSQL_MAJOR= \ 
#MYSQL_VERSION= \
#CFLAGS= \
#IS_CLEAN= \
#MYSQL_DOWNLOAD_VERSION= 
ENTRYPOINT ["/entrypoint.sh"]
HEALTHCHECK CMD /healthcheck.sh
CMD ["mysqld"]
