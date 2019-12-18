# Java 中的软引用(SoftReference)、弱引用(WeakReference)和虚引用(PhantomReference) 解析和实际用例

在一般的 Java GC 中，JVM GC 的过程是对程序员透明的，既不可控又不可知的。
但在少数内存敏感情况下，开发者需要对 GC 有一定程度的掌控。
我们可能需要让一个类实例尽早或者尽量完被 GC，或者我们需要知道一个类实例是否被 GC。
这就是为什么 Java 引入了 3个 Reference 类。

网上已有大量文章描述了三个类的作用和区别。我这里不再相熟。
本文目的是提供三个类引用的实际用例，以便概念落地。

## GC 监控

Java 通过 ReferenceQueue 类提供了 GC 活动的通知机制。
在创建 Reference 类(无论是软、弱还是虚引用)时，开发者可以绑定 ReferenceQueue 实例。
当 JVM 确定某个类实例不可达时(既确定该实例可以被 GC)，就会把对应的 Reference 实例放入 ReferenceQueue。
开发中通过监控 ReferenceQueue 的状况来确定类实例是否被回收。

在下面的例子创建了3个 Video 类实例，释放 video2 引用，然后通过 ReferenceQueue 监控 video2 的 GC 时间。

```java
// 模拟把视频文件加载到内存中
import static java.lang.System.out;

public class Video {
    private byte[] data;

    public Video() {
        this.data = new byte[1024*1024*30]; //30MB
        out.println("Allocated " + this.data.length + " bytes memory");
    }
}
```  


```java
import java.lang.ref.PhantomReference;
import java.lang.ref.ReferenceQueue;

public class GcMonitorExample {

    public static void main(String[] args) throws InterruptedException {
        final ReferenceQueue<Video> referenceQueue = new ReferenceQueue<Video>();

        // 模拟加载3个视频到内存中
        final Video video1 = new Video();
        final PhantomReference<Video> videoRef1 = new PhantomReference<Video>(video1, referenceQueue);

        Video video2 = new Video();
        final PhantomReference<Video> videoRef2 = new PhantomReference<Video>(video2, referenceQueue);

        Video video3 = new Video();
        final PhantomReference<Video> videoRef3 = new PhantomReference<Video>(video3, referenceQueue);

        // 在另外的线程中监控 GC 情况
        new Thread() {
            @Override
            public void run() {

                for (int i = 0; i < 10; i++) {
                    try {
                        System.gc();
                        Thread.sleep(1000);

                        PhantomReference<Video> ref = (PhantomReference<Video>) referenceQueue.poll();
                        if (ref == videoRef1) {
                            System.out.println("video 1 has been GC");
                        } else if (ref == videoRef2) {
                            System.out.println("video 2 has been GC");
                        } else if (ref == videoRef3) {
                            System.out.println("video 3 has been GC");
                        }


                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }

            }
        }.start();

        // 释放 video2 的强引用
        video2 = null;

        while(true) {
            // 保持程序运行，否则 JVM 关闭，所有 video 实例都会被回收
            Thread.sleep(1000);
        }
    }

}
```

运行程序，你可以看到如下输出。你可以明确地知道 video2 是否被回收，被回收的时间。
```bash
Allocated 31457280 bytes memory
Allocated 31457280 bytes memory
Allocated 31457280 bytes memory
video 2 has been GC
```

需要注意的是，3类引用都支持同样的方式监控 GC，区别在于：
1. 虚引用不改变类实例的 GC 行为，是最适合用来监控普通类实例 GC 行为的。
2. 软引用和弱引用明确定义了实例的 GC 时机，相对来说没有太多监控 GC 行为的需求。


## 更灵活的资源回收

在 Java 中，你有几种方法可以在使用完一个类后回收相应的资源。  
通过实现 Closeable 接口，你可以通过 close-with-resource 保证资源在最后肯定会被回收。
但是这种方法需要开发者使用 close-with-resource 或者手动调用 close()。  
又或者，你可以通过重载 finalize() 来实现类实例被 GC 前先关闭资源。

基于众所周知的原因，finalize() 并不可靠，并且有复活实例的风险，已经被标记成 deprecated。

现在，你可以通过 PhantomReference 实现资源回收，不依赖于开发者编写正确的代码，也在某种程度上避免复活实例。

在上方 "GC 监控" 的例子中，当你知道某个实例会被 GC 时，你可以执行对应的资源回收操作。
PhantomReference#get() 永远 return null，所以在资源回收的过程中你没有任何办法获取该实例的强引用。
也就是你无法复活一个实例，从而避免了 finalize() 的缺陷。

## 更清晰地使用内存

考虑这样一种情况。我通过 -Xmx64MB 启动一个JVM。JVM自己占用了18MB。
然后我加载一个30MB的视频进内存中。此时，我剩余64-18-30=16MB。
然后，我释放了视频引用。
当我尝试加载另外一个30MB的视频文件时，理论上我有充足的内存可用，但是由于上一个视频未被 GC，程序报 OOM。

同样，在上方 "GC 监控" 的例子之上，你可以确保第一个视频实例被回收后，你再申请新视频的内存。从而避免 OOM。

## FileCleaningTracker

再提供一个在 commons-io 库中的真实例子。

FileCleaningTracker 通过实现一个 PhantomReference 的子类 Tracker 来实现文件删除操作。

https://github.com/apache/commons-io/blob/master/src/main/java/org/apache/commons/io/FileCleaningTracker.java#L241
