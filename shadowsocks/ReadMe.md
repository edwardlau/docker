# Deception
基于Apline Linux 安装的shadowsocks

###如何执行？
* 使用Docker进行构建：

        docker build -t containerName：tag .
* 执行
    
        docker run -it --name ss -p 8999:8999 containerName：tag
    
* 默认密码

        默认密码为hellworld