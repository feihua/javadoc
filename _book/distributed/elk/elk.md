# 1.Elasticsearch

## 1.1配置文件elasticsearch.yml

**(如果是单机测试可以不用配置直接默认启动就可以了)**

```yaml
配置集群名称，保证每个节点的名称相同，如此就能都处于一个集群之内了
cluster.name: elasticsearch-cluster
# 每一个节点的名称，必须不一样
node.name: es-node1
# http端口（使用默认即可）
http.port: 9200
# 主节点，作用主要是用于来管理整个集群，负责创建或删除索引，管理其他非master节点（相当于企业老总）
node.master: true
# 数据节点，用于对文档数据的增删改查
node.data: true
# 集群列表
discovery.seed_hosts: ["192.168.0.100", "192.168.0.101", "192.168.0.102"]
# 启动的时候使用一个master节点
cluster.initial_master_nodes: ["es-node1"]
```
## 1.2启动

```plain
./bin/elasticSearch  –d 启动。-d表示后台启动
```
## 1.3访问

```plain
127.0.0.1:9200
```
# 2.Logstash

## 2.1创建配置文件logstash.conf

```properties
input {
        file {
                type => "log" 
                path => ["/export/home/tomcat/domains/*/*/logs/*.out"]
                start_position => "end"
		ignore_older => 0
		codec=> multiline {             //配置log换行问题
                        pattern => "^%{TIMESTAMP_ISO8601}"
                        negate => true
                        what => "previous"
                }
        }
        beats {
            port => 5044
        }
}
output {
        if [type] == "log" {
		elasticsearch {
			hosts => ["http://127.0.0.1:9200"]
			index => "log-%{+YYYY.MM}"
		}	
	}
}
```
## 2.2启动

```bash
./logstash -f ../config/logstash.conf
后台启动：nohup ./bin/logstash -f config/log.conf > log.log &
```
配置多个文件：./logstash -f ../config 指定启动目录，然后启动目录下配置多个*.conf文件。里面指定不同的logpath。

# 3.Kibana

## 3.1配置kibana.yml

```yaml
elasticsearch.url: http://localhost:9200
server.host: 0.0.0.0
```
## 3.2启动

```bash
启动命令：./kibana
后台启动：nohup ./bin/kibana &
```
## 3.3访问

```http
http://localhost:5601
```

