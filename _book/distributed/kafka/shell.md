```shell
#! /bin/bash
case $1 in
"start"){
        for i in hadoop101 hadoop102 hadoop103
        do
                echo " --------启动 $i Kafka-------"
                # 用于KafkaManager监控
                ssh $i "export JMX_PORT=9988 && /usr/local/software/kafka_2.12-2.6.0/bin/kafka-server-start.sh -daemon /usr/local/software/kafka_2.12-2.6.0/config/server.properties "
        done
};;
"stop"){
        for i in hadoop101 hadoop102 hadoop103
        do
                echo " --------停止 $i Kafka-------"
                ssh $i "/usr/local/software/kafka_2.12-2.6.0/bin/kafka-server-stop.sh stop"
        done
};;
esac
```
