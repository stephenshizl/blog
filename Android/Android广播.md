## Android广播

##### android引入广播机制
  1. 从MVC的角度考虑引入广播机制可以方便几大组件的信息和数据交互。
  2. 程序间互通消息(例如在自己的应用程序内监听系统来电)
  3. 效率上(参考UDP的广播协议在局域网的方便性)
  4. 设计模式上(反转控制的一种应用，类似监听者模式)

##### 广播类型

**注册类型：**
  1.静态注册：在AndroidManifest.xml注册，即使程序关闭，广播还在监听
  2.动态注册：在activity注册，伴随着activity生命周期。注意：记得移除广播

**发送方式：**
  1.有序广播：所有接受者按照优先级顺序获取广播，然后处理传递到下一位或者直接终止广播
  2.无序广播：发送时异步，所有接受者接受顺序不固定，且不能传递


##### 生命周期
  1. 生命周期由调用它开始，onReceiver方法执行后结束
  2. 每次广播被接受处理完后，BroadcastReceiver会重建
  3. onReceiver方法不能在10s内完成，就会报ANR

##### 发送过程
  1. 广播发送者，将特定的广播发送给AMS(ActivityManagerService)
  2. AMS接收到广播后，先找到广播对应的接受者，然后将他们添加到广播调度队列中
  3. AMS不断处理广播调度队列，找到广播的接受者，并将对应的广播发送它们所运行的进程中
  4. 进程接收到广播后并非分发给广播接受者处理，而是封装成一个消息，发送到线程消息队列中
  5. 当消息被处理的时候才会发送给相应的广播接受者处理   

  注意：AMS向应用进程发送广播时，采用的是异步进程间通信Binder驱动体系。


##### 动态广播注册
  ```
  //注册静态广播
IntentFilter intentFilter = new IntentFilter();
intentFilter.addAction("yourAction");
registerReceiver(yourBrocastReceiver,intentFilter);

//取消绑定广播
 unregisterReceiver(cmdBrocastReceiver);
  ```

##### 静态广播注册
```
<receiver android:name=".yourBrocastReceiver">
  <intent-filter>
      <action android:name="yourAction" />
  </intent-filter>
</receiver>
```
