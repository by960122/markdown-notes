# Docker 命令大全
- https://docs.docker.com/engine/reference/run/

## 常用命令
### 镜像
```sh
# 查看所有镜像
docker images

# 搜索镜像,过滤标星3000以下的
docker search mysql --filter=STARS=3000

# 删除镜像
docker rm -f 镜像id1 镜像id2 镜像id3
docker rm -f $(docker images -aq)

# 拉取镜像
docker pull centos

# 启动镜像
# --name='容器名字'
# -d 后台运行
# -it 交互方式运行
# -v 主机目录:容器目录,它俩相互同步,docker 删了数据不会丢 
# -p 端口 主机端口:容器端口 ip:主机端口:容器端口
# -P 随机端口
# --rm 用完就删,一般用于测试
docker run -it centos /bin/bash
docker run -d --name nginx01 -p 3344:80 nginx
# es 启动很占内存,这样启动
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m" elasticsearch:7.6.2
```

### 发布镜像
```sh
# 登录
docker login -u by960122

# 发布,一定要带版本号
docker push by960122/centos_custom:1.0

# 可以定义下版本号
docker tag 镜像id by960122/centos_custom:1.0
docker tag 84cecd33e2f8 by960122/centos_custom:1.0

# 镜像保存到本地
docker save -o C:\\Downloads\\centos_custom-1.0.tar centos_custom:1.0

# 镜像导入
docker load < centos_custom-1.0.tar
docker load -i centos_custom-1.0.tar

# 容器导出
docker export -o C:\\Downloads\\centos_custom.tar centos_custom

# 注意导入后变为镜像,而不是容器
docker import C:\\Downloads\\centos_custom.tar
```

### 容器
```sh
# 列出所有运行的容器
# -a 正在运行 + 历史
# -n 最近n个运行的容器
# -q 只显示容器编号
docker ps 

# 停止容器
docker start 容器id
docker restart 容器id
docker stop 容器id
docker kill 容器id

# 显示日志
docker logs -tf --tail 10 容器id

# 查看进程
docker top
docker stats

# 查看容器信息
docker inspect 容器id

# 进入容器
docker attach 容器id

# 执行命令
docker container exec --privileged a0a44148a673 ls /opt/software

# 把容器内文件拷贝出来
docker cp 容器id:/home/test.txt /home
docker cp C:\\Downloads\\Python-3.9.1.tgz a0a44148a673:/opt/software
# 把文件拷贝到容器里面

# 提交容器为一个镜像
docker commit -m="自定义容器-centos" -a="BYDylan" a0a44148a673 centos_custom:1.0
```

### 搭建网络
```sh
# 搭建子网络,以便子容器集群能相互通讯
# docker network ls
# docker network inspect by_network
docker network create --subnet=192.168.0.0/24 by_network

# 主服务器hadoop0端口映射到宿主机,映射10000,9870,8088,8082,8080,7077
docker run -itd --name by101 --hostname by101 --net by_network --ip 192.168.0.101 -p 8088:8088 -p 9870:9870 -p 8080:8080 centos-custom
docker run -itd --name by102 --hostname by102 --net by_network --ip 192.168.0.102 centos-custom
docker run -itd --name by103 --hostname by103 --net by_network --ip 192.168.0.103 centos-custom
```

## 搭建 Redis 集群
```sh
# 1.创建网络
# 2.shell 脚本创建集群
for port in ${seg 1 6};
do 
	mkdir -p/mydata/redis/node-${port}/conf 
	touch /opt/software/redis/node-${port}/conf/redis.conf 
	cat < EOF >/opt/software/redis/node-${port}/conf/redis.conf 
	port 6379
	bind 0.0.0.0
	cluster-enabled yes 
	cluster-config-file nodes conf 
	cluster-node-timeout 5000
	cluster-announce-ip 172.38.0.1${port}
	cluster-announce-port 6379
	cluster-announce-bus-port 16379
	appendon ly yes done
	EOF
done

# 3.启动6个,例如
docker run -p 6371:6379 -p 16371:16379 --name redis-1 -v /opt/software/redis/node-1/data:/data -v /opt/software/redis/node-1/conf:/etc/redis/redis.conf \
-d --net by_network --ip 192.168.1.1 redis:6.3.1 redis-server /etc/redis/redis.conf

# 4.创建集群
redis-cli --cluster create 192.168.1.1:6379 192.168.1.2:6379 192.168.1.3:6379 192.168.1.4:6379 192.168.1.5:6379 192.168.1.6:6379 --cluster-replicas 1
```