# Deception
基于Apline Linux 安装的mongodb

###如何执行？
* 使用Docker进行构建：

    docker build -t containerName：tag .
* 执行
    
    docker run -it --name mongodb containerName：tag
    
* 使用指定配置文件执行        

    docker run -d --name mongodb -v /data/mongodb/conf/mongod.conf:/etc/mongod.conf containerName：tag
    
       
###数据存储位置
默认存储／data／db目录下，可以通过-v 挂载到外部磁盘
    


