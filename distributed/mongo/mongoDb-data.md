

# 1.职工信息

```sql
db.emp.insert([
    {_id:1101,name:'鲁班' ,job:'讲师'    ,dep:'讲师部',salary:10000},
    {_id:1102,name:'悟空' ,job:'讲师' ,dep:'讲师部',salary:10000},
    {_id:1103,name:'诸葛'  ,job:'讲师' ,dep:'讲师部',salary:10000},
    {_id:1105,name:'赵云' ,job:'讲师'  ,dep:'讲师部',salary:8000},
    {_id:1106,name:'韩信',job:'校长' ,dep:'校办',salary:20000},
    {_id:1107,name:'貂蝉' ,job:'班主任'  ,dep:'客服部',salary:8000},
    {_id:1108,name:'安其' ,job:'班主任'  ,dep:'客服部',salary:8000},
    {_id:1109,name:'李白' ,job:'教务'  ,dep:'教务处',salary:8000},
    {_id:1110,name:'默子'  ,job:'教务',dep:'教务处',salary:8000},
    {_id:1111,name:'大乔',job:'助教' ,dep:'客服部',salary:5000},
    {_id:1112,name:'小乔' ,job:'助教' ,dep:'客服部',salary:3000},
]);
```

# 2. 学生信息

```sql
db.student.insertMany([
    {_id:"001",name:"陈霸天",age:5,grade:{redis:87,zookeper:85,dubbo:90}},
    {_id:"002",name:"张明明",age:3,grade:{redis:86,zookeper:82,dubbo:59}},
    {_id:"003",name:"肖炎炎",age:2,grade:{redis:81,zookeper:94,dubbo:88}},
    {_id:"004",name:"李鬼才",age:6,grade:{redis:48,zookeper:87,dubbo:48}}
])
```

# 3. 学生科目

```sql
db.subject.insertMany([
    {_id:"001",name:"陈霸天",subjects:["redis","zookeper","dubbo"]},
    {_id:"002",name:"张明明",subjects:["redis","Java","mySql"]},
    {_id:"003",name:"肖炎炎",subjects:["mySql","zookeper","bootstrap"]},
    {_id:"004",name:"李鬼才",subjects:["Java","dubbo","Java"]},
])


db.subject2.insertMany([
    {_id:"001",name:"陈霸天",subjects:[{name:"redis",hour:12},{name:"dubbo",hour:120},{name:"zookeper",hour:56}]},
    {_id:"002",name:"张明明",subjects:[{name:"java",hour:120},{name:"mysql",hour:10},{name:"oracle",hour:30}]},
    {_id:"003",name:"肖炎炎",subjects:[{name:"mysql",hour:12},{name:"html5",hour:120},{name:"netty",hour:56}]},
    {_id:"004",name:"李鬼才",subjects:[{name:"redis",hour:12},{name:"dubbo",hour:120},{name:"netty",hour:56}]}
])
```

# 4. 课程项目

```sql
db.project.insert([
    { _id: 1, name: "Java Script", description: "name is js and jquery" },
    { _id: 2, name: "Git", description: "Git is a free and open source distributed version control system designed to handle everything from small to very large projects with speed and efficiency" },
    { _id: 3, name: "Apache dubbo", description: "Apache Dubbo  is a high-performance, java based open source RPC framework.阿里 开源 项目" },
    { _id: 4, name: "Redis", description: "Redis is an open source (BSD licensed), in-memory data structure store, used as a database, cache and message broker. It supports data structures" },
    { _id: 5, name: "Apache ZooKeeper", description: "Apache ZooKeeper is an effort to develop and maintain an open-source server which enables highly reliable distributed coordination" }
])
```

