
## 摘要
本文介绍了在Android和iOS双端设备上查看APP崩溃日志的方法，包括使用adb命令获取Android崩溃日志以及通过克魔助手工具查看iOS设备的崩溃日志。同时提供了操作步骤和相关代码案例演示。

## 引言
在移动应用开发过程中，经常需要查看APP的崩溃日志以便进行调试和分析。针对Android和iOS双端设备，本文将介绍如何获取和分析APP崩溃日志，以便开发者能够更高效地定位和解决问题。

### Android端
在安卓设备上，可以通过以下两种方法获取APP的崩溃日志：

#### 方法1：使用adb命令获取崩溃日志
```bash
adb logcat group apk包名
adb logcat -v time >D:\log.txt
adb logcat -v time *:E >D:\log.txt
```

#### 方法2：使用克魔助手工具查看
克魔助手工具提供了强大的功能，可以根据UID、机型、出现崩溃的时间筛选日志，极大地简化了开发者的调试工作。

### iOS端
针对iOS设备，通过克魔助手工具可以实时查看设备的日志信息，并对APP的崩溃日志进行符号化、格式化和分析。以下是操作步骤：

1.. 连接iPhone到电脑，确保信任连接。
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/a29c5bbf7d7344f6998f090ace50a73c.png)


2.. 在电脑上打开克魔助手-实时日志。
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/d1d5578dd18e48459c23c73b2ad71db2.png)


3.. 选择需要查看的App并开始日志记录。
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/744c493353e54e109e256241e8e5a7bb.png)


4. 过滤关键字并导出日志以形成errorlog提交给开发团队。
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/cadbcc345d9d444cb5b4c47afc32aeaa.png)

## 代码案例演示
```java
// 示例代码
public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // 添加崩溃日志监控
        Thread.setDefaultUncaughtExceptionHandler(new Thread.UncaughtExceptionHandler() {
            @Override
            public void uncaughtException(Thread t, Throwable e) {
                // 将崩溃日志保存至本地文件
                saveCrashInfoToFile(e);
            }
        });
    }

    private void saveCrashInfoToFile(Throwable e) {
        // 实现崩溃日志保存逻辑
    }
}
```

## 奔溃日志分析
克魔助手还提供了奔溃日志分析查看模块，可以方便地导出和查看iOS设备上的奔溃日志，并对其进行符号化、格式化和分析。操作如下：

1. 选择需要查看的奔溃日志。
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/515f8088304e494882953664206127fc.png)

2. 点击“导出日志”，即可生成一个包含奔溃日志的文件夹，便于提交给开发团队进行分析。
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/47a7f89a2ee4483a986bab74f24365ec.png)

PS：数据连接时，先将iPhone通过数据线连接上电脑，iOS手机上一定要信任这次连接。（开启WiFi调试时，无需数据线）
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/af12991fc1ae43d9b99604f8d70906a7.png)

## 总结
通过本文介绍的方法，开发者可以轻松查看Android和iOS设备上的APP崩溃日志，并进行相应的分析和处理。这将极大地简化开发调试工作，提高开发效率。

## 参考资料
- [ 崩溃日志查看](https://keymob.com/doc/hot/viewing-ios-app-runtime-logs.html)
- [克魔助手官网](https://keymob.com/)

希望本文对您有所帮助，祝您开发顺利！ 👨‍💻📱
