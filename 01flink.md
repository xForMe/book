# flink

## 提交作业

```shell
-- -c 执行的类 
-- -p 并行参数
./flink run -c com.dgtis.flink.WordCount -p 1 /Users/xforme/Desktop/workspace/learn/sweet/flink/target/learn-flink.jar

```

### yarn提交

yarn提交分为session-cluster/pre-job-cluster

启动yarn session

```shell
./yarn-session.sh -n 2 -s 2 -jm 1024 -tm 1024 -nm test -d 
```

- -nm 在yarn上的名称

- -jm jobmanager的内存

- -tm 运行task的内存

- -n taskManager数量

- -s slot的数量

### 启动作业

-  -m yarn-cluster 通过yarn session cluster启动

```shell

./flink run -c com.dgtis.flink.WordCount -p 1 /Users/xforme/Desktop/workspace/learn/sweet/flink/target/learn-flink.jar
```

### 查看运行的作业列表

```shell
./flink  list
```

## udf函数

### 函数类

```java
public class CountMap implements MapFunction<String, CountVo> {
    @Override
    public CountVo map(String val) throws Exception {
        String[] arr = val.split("\\|");
        CountVo countVo = new CountVo(arr);
        return countVo;
    }
}
```



### 匿名函数

```java
DataStream<CountVo> countStream =text
                        .map(new CountMap())
                        .keyBy(vo->{ return vo.getInsureName(); })
```

### 富函数

可以获取到运行时上下文，及生命周期方法（open｜close）

- open完成一些初始化行为如数据库连接，在完成构造函数之后执行open。此时作业已经提交到结点

- close 完成一些收尾工作如清空状态

```java
public class CountRichMapFun extends RichMapFunction {
    @Override
    public Object map(Object value) throws Exception {
        return null;
    }

    @Override
    public void open(Configuration parameters) throws Exception {
        super.open(parameters);
    }

    @Override
    public void close() throws Exception {
        super.close();
    }
}
```

## sink

对外输出/输出到db的时候通过sink

```java
countStream.addSink(
                StreamingFileSink.forRowFormat(
                        new Path(path),
                        new SimpleStringEncoder<CountVo>("GBK")).build()
        );
```

### kafka

kafka sink数据写入kafka

```java
countStream.map(vo->{
    return vo.toString();
}).addSink(
         new FlinkKafkaProducer010<String>("127.0.0.1:9092","test",
                 new SimpleStringSchema())
);
```

## window

窗口类型

- Tumbling Window(滚动窗口)

- Sliding Window(滑动窗口)

- 会话窗口

- 全局窗口

基于滚动窗口10s获取一个窗口数据

```java
DataStream<CountVo> countStream = text
                .map(new CountMap())
                .keyBy(vo -> {
                    return vo.getInsureName();
                })
                .window(TumblingEventTimeWindows.of(Time.seconds(5)))
                .reduce((cur, next) -> {
                    CountVo countVo = new CountVo(cur.getInsureName());
                    return countVo;
                });
```

## watermaker

时间特征：TimeCharacteristic

-   ProcessingTime 事件的处理时间
-   IngestionTime 数据进入flink的时间
-   EventTime 数据中的处理时间

时间水位

```java
.assignTimestampsAndWatermarks(
        WatermarkStrategy.forBoundedOutOfOrderness(Duration.ofSeconds(10));
)
```

