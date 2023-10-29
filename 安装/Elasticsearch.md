# 下载 Elasticsearch
- https://github.com/mobz/elasticsearch-head
- https://github.com/medcl/elasticsearch-analysis-ik/
- https://www.elastic.co/cn/downloads/past-releases

## 1.配置环境变量
```sh
echo $ELASTICSEARCH
```
## 2.创建用户
```sh
tar -zvxf elasticsearch-8.14.1-linux-x86_64.tar.gz
useradd -d /home/elastic -m elastic
usermod -a -G elastic elastic
chown -R elastic:elastic /opt/software/elasticsearch-8.14.1
```
## 3.修改配置文件
### /etc/sysctl.conf
```sh
vm.max_map_count=655300
# 生效
sysctl -p
```
### config/elasticsearch.yml
```sh
# 集群名称
cluster.name: elasticsearch-cluster
# 节点名称 - 对应修改
node.name: elasticsearch-201
# 启动时锁定内存
bootstrap.memory_lock: false
# 绑定 IP
network.host: 0.0.0.0
# 集群节点
discovery.seed_hosts: ["192.168.1.201","192.168.1.202", "192.168.1.203"]
# 主节点
cluster.initial_master_nodes: ["elasticsearch-203"]
# 设置es允许跨域访问
http.cors.enabled: true
http.cors.allow-origin: "*"
```
### config/jvm.options
```sh
-XX:+UseG1GC
-Xms2g
-Xmx4g
-Dfile.encoding=UTF-8
-Dsun.jnu.encoding=UTF-8

scp /opt/software/elasticsearch-8.14.1/config/* 192.168.1.202:/opt/software/elasticsearch-8.14.1/config/
scp /opt/software/elasticsearch-8.14.1/config/* 192.168.1.203:/opt/software/elasticsearch-8.14.1/config/
```
### bin/elasticsearch-env
```sh
# 看到java路径都改成
JAVA="/opt/software/jdk-17.0.9/bin/java"

scp elasticsearch-env 192.168.1.202:/opt/software/elasticsearch-8.14.1/bin/
scp elasticsearch-env 192.168.1.203:/opt/software/elasticsearch-8.14.1/bin/
```

## 重置密码

```sh
# 自动生成
elasticsearch-reset-password -u elastic
# 手动指定
elasticsearch-reset-password -u elastic -i
# 注意: 使用kibana 要使用 kibana_system 用户
elasticsearch-reset-password -u kibana_system -i
# By96o122

elasticsearch-setup-passwords interactive 

```
## 4.启动
```sh
su - elasticsearch
# 前台启动
bin/elasticsearch
# 后台启动
/opt/software/elasticsearch-8.14.1/bin/elasticsearch -d
# windows 直接双击 elasticsearch.bat
```
## 5.web界面
- http://192.168.1.201:9200

```sh
# 查看运行情况：
curl 'http://192.168.1.201:9200/_cluster/health?pretty'
# 如果用 DataGrip 连接, 
# 1. 需要将 License 设置为试用版, 进入 kibana 操作
# 更改 License 类型 - trial
POST _license/start_trial?acknowledge=true

# 更改 License 类型 - basic
POST _license/start_basic?acknowledge=true
# 1.1 idea 自带的也可以
GET https://127.0.0.1:9200
Content-Type: application/json
Authorization: Basic elastic By96o122

# 2. 连接的ssl配置, model: Verify CA, 只填写CA file: **.crt


```
## 6.一些报错信息
### elasticsearch-env: line 122: syntax error near unexpected token <'
```sh
# 修改 elasticsearch-env 122 行
#   done < <(env) 改为  done <<< 'env'
```

# 下载 Kibana
- https://www.elastic.co/cn/downloads/past-releases

## 1.修改配置文件
### kibana.yml 
```sh
server.host: "0.0.0.0"
elasticsearch.hosts: "http://192.168.1.203:9200"
```
## 2.启动
```sh
sh elasticsearch 
./kibana
```
## 3.web界面
- 192.168.1.201:5601

## 4.一些报错信息
### libnss3.so: cannot open shared object file: No such file or directory
```sh
yum install -y nss.x86_64
```
