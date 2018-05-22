通过几个简单的步骤启动并运行Flink示例程序。

## 安装程序：下载并启动Flink
Flink运行在Linux，Mac OS X和Windows之上。为了能够运行Flink，唯一的要求是安装一个可以工作的Java 7.x（或更高版本）。Windows用户请查看[Windows上的Flink指南](https://ci.apache.org/projects/flink/flink-docs-master/start/flink_on_windows.html)，其中介绍了如何在Windows上运行Flink以进行本地设置。
您可以用以下命令检查java安装正确：
```
java -version
```
如果你安装了java 8，将会得到如下的输出：
```
java version "1.8.0_111"
Java(TM) SE Runtime Environment (build 1.8.0_111-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.111-b14, mixed mode)
```

### 下载并编译
从我们的一个[库](http://flink.apache.org/community.html#source-code)中克隆源代码，例如：
```
$ git clone https://github.com/apache/flink.git
$ cd flink
$ mvn clean package -DskipTests # 这需要10分钟
$ cd build-target               # 这是Flink安装到的地方。
```
### 启动一个本地Flink集群
```
$ ./bin/start-cluster.sh  # Start Flink
```
在[http://localhost:8081](http://localhost:8081)检查调度器的Web前端，并确保所有内容都已启动并正在运行。Web前端应报告一个可用的任务管理器实例。
![](jobmanager-1.png)
你也可以通过检查日志目录中的日志文件来验证系统是否正在运行：
```
$ tail log/flink-*-standalonesession-*.log
INFO ... - Rest endpoint listening at localhost:8081
INFO ... - http://localhost:8081 was granted leadership ...
INFO ... - Web frontend listening at http://localhost:8081.
INFO ... - Starting RPC endpoint for StandaloneResourceManager at akka://flink/user/resourcemanager .
INFO ... - Starting RPC endpoint for StandaloneDispatcher at akka://flink/user/dispatcher .
INFO ... - ResourceManager akka.tcp://flink@localhost:6123/user/resourcemanager was granted leadership ...
INFO ... - Starting the SlotManager.
INFO ... - Dispatcher akka.tcp://flink@localhost:6123/user/dispatcher was granted leadership ...
INFO ... - Recovering all persisted jobs.
INFO ... - Registering TaskManager ... under ... at the SlotManager.
```
## 阅读代码
你可以在GitHub上找到此SocketWindowWordCount示例[scala](https://github.com/apache/flink/blob/master/flink-examples/flink-examples-streaming/src/main/scala/org/apache/flink/streaming/scala/examples/socket/SocketWindowWordCount.scala)和[java](https://github.com/apache/flink/blob/master/flink-examples/flink-examples-streaming/src/main/java/org/apache/flink/streaming/examples/socket/SocketWindowWordCount.java)的完整源代码。

Scala
```
object SocketWindowWordCount {

    def main(args: Array[String]) : Unit = {

        // 要连接的端口
        val port: Int = try {
            ParameterTool.fromArgs(args).getInt("port")
        } catch {
            case e: Exception => {
                System.err.println("No port specified. Please run 'SocketWindowWordCount --port <port>'")
                return
            }
        }

        // 获得执行环境
        val env: StreamExecutionEnvironment = StreamExecutionEnvironment.getExecutionEnvironment

        // 通过连接到套接字获取输入数据
        val text = env.socketTextStream("localhost", port, '\n')

        // 解析数据，对它进行分组，窗口化，并聚合计数
        val windowCounts = text
            .flatMap { w => w.split("\\s") }
            .map { w => WordWithCount(w, 1) }
            .keyBy("word")
            .timeWindow(Time.seconds(5), Time.seconds(1))
            .sum("count")

        // 用单线程而不是并行打印结果
        windowCounts.print().setParallelism(1)

        env.execute("Socket Window WordCount")
    }

    // word与count的数据类型
    case class WordWithCount(word: String, count: Long)
}
```
Java
```
public class SocketWindowWordCount {

    public static void main(String[] args) throws Exception {

        // 要连接的端口
        final int port;
        try {
            final ParameterTool params = ParameterTool.fromArgs(args);
            port = params.getInt("port");
        } catch (Exception e) {
            System.err.println("No port specified. Please run 'SocketWindowWordCount --port <port>'");
            return;
        }

        // 获得执行环境
        final StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();

        // 通过连接到套接字获取输入数据
        DataStream<String> text = env.socketTextStream("localhost", port, "\n");

        // 解析数据，对它进行分组，窗口化，并聚合计数
        DataStream<WordWithCount> windowCounts = text
            .flatMap(new FlatMapFunction<String, WordWithCount>() {
                @Override
                public void flatMap(String value, Collector<WordWithCount> out) {
                    for (String word : value.split("\\s")) {
                        out.collect(new WordWithCount(word, 1L));
                    }
                }
            })
            .keyBy("word")
            .timeWindow(Time.seconds(5), Time.seconds(1))
            .reduce(new ReduceFunction<WordWithCount>() {
                @Override
                public WordWithCount reduce(WordWithCount a, WordWithCount b) {
                    return new WordWithCount(a.word, a.count + b.count);
                }
            });

        // 用单线程而不是并行打印结果
        windowCounts.print().setParallelism(1);

        env.execute("Socket Window WordCount");
    }

    // word与count的数据类型
    public static class WordWithCount {

        public String word;
        public long count;

        public WordWithCount() {}

        public WordWithCount(String word, long count) {
            this.word = word;
            this.count = count;
        }

        @Override
        public String toString() {
            return word + " : " + count;
        }
    }
}
```

## 运行示例
现在，我们要运行这个Flink应用程序。它将从套接字读取文本，并且每5秒打印一次在前5秒内每个不同单词的出现次数，即只要单词浮入其中，就是处理时间的滚动窗口。
- 首先，我们使用netcat来启动本地服务器，通过：
```
$ nc -l 9000
```

- 提交Flink程序：
```
$ ./bin/flink run examples/streaming/SocketWindowWordCount.jar --port 9000
Starting execution of program
```

程序连接到套接字并等待输入。 你可以检查Web界面以验证作业是否按预期运行：
![](jobmanager-2.png)![](jobmanager-3.png)
- 单词在5秒（处理时间，滚动窗口）的时间窗口中计数并打印到标准输出。监视任务管理器的输出文件并在nc中写入一些文本（输入在敲回车被逐行发送到Flink）：
```
$ nc -l 9000
lorem ipsum
ipsum ipsum ipsum
bye
```
.out文件将在每个时间窗口结束时打印计数，只要文字浮入，例如：
```
$ tail -f log/flink-*-taskexecutor-*.out
lorem : 1
bye : 1
ipsum : 4
```
完成后要停止Flink，请输入：
```
$ ./bin/stop-cluster.sh
```
下一步
查看更多[示例](../examples/overview.html)以更好地体验Flink的编程API。 完成此操作后，请继续阅读[DataStream指南](../dev/datastream_api.html)。
