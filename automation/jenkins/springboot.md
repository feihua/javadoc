jenkins启动spring boot的脚本

```powershell
for i in authentication timer_task msg_center area eagle_eyes system_service jpush short_message register_center user_center project_center machine_center order_center clock_center employment_center;  
do
pid=`ps -ef | grep $i.jar | grep -v grep | awk '{print $2}'`
echo “旧应用进程id：$pid”
if [ -n "$pid" ]
then
kill -9 $pid
fi
echo "=== stop $i successful"
BUILD_ID=dontKillMe nohup java -jar  $i/target/$i.jar --spring.profiles.active=test >/root/logs/$i-log.txt &
echo "=== start $i successful"
done

```

