---
title: (自以为)优雅的跨进程单例的实现思路
category: think-when-god-laugh
layout: post
tag: 爬坑之路
---

# 零 烫烫烫烫烫烫

>单例模式，也叫单子模式，是一种常用的软件设计模式。在应用这个模式时，单例对象的类必须保证只有一个实例存在。许多时候整个系统只需要拥有一个的全局对象，这样有利于我们协调系统整体的行为。

但这种设计模式有局限：只能在一个进程内生效。但项目开发中又难免会出现开启多个进程的情况。这个时候，原本设计的单例，在整个应用的范围来看，变成了两个单例。两个进程内的单例的内部状态(变量的取值)也就无法同步了，这也是这个问题的核心(单例的行为(方法)在不同进程是一致的，内部状态会影响到行为的结果)。

# 一 如何解决
解决数据不同步问题的方法很多，简单的做法有两种：持久化或者跨进程调用。

## 1 持久化
Android可用的持久化的方式有本地文件、SharedPreference和数据库这几种。

通过将数据持久化到本地，数据读写都通过操作持久化数据，可以实现数据的同步。

这种方案会引入新的问题：同时写文件的问题(数据可能会乱掉)，同时会增加读写本地IO的耗费。

在以上三种持久化方式里，本地文件、SharedPreference都有可能出现同时写文件的问题。数据库还好，而且Android组件里有ContentProvider可以帮助我们简化一些操作。

但这三种方法，都要额外做一些事情，比如数据存储格式(本地文件)、字段名的定义和维护(SharedPreference)、表的定义和维护和增删改查的实现(数据库)。光想一想就很头大。

最开始有想过ContentProvider的方式实现，但实现起来也挺麻烦挺蛋疼的，后来就不了了之了。

## 2 IPC(进程间调用)
IPC机制很适合用于解决这个问题，这个实现方式更接近后端的RPC(远程过程调用)。Android的进程间通讯机制采用AIDL来实现。

这种实现方式的方法步骤也不算简单：

1. 定义AIDL接口
2. 实现AIDL接口里的方法
3. 实现一个Service，在绑定的时候返回实现了AIDL接口Binder对象(被调用方)
4. 绑定Service，获得Binder对象，通过Binder对象进行方法调用(调用方)

虽然不简单，但也不复杂。但怎么应用到现有代码里呢？

依旧是最简单的解决思路：

1. 为每个单例的调用都封装一层(实际是两层，一层给业务，一层是AIDL，用于跨进程调用)
2. 在调用的时候，封装层里判断当前调用的执行环境，如果在单例所在的进程，则调用单例的对应方法，否则，发起一次进程间调用。

这个解决思路里，大部分是体力活：

1. 把单例里定义的方法添加到AIDL文件里
2. 实现AIDL文件里的方法(跨进程调用的封装)
3. 添加封装层(if (在单例的进程) { 调用单例的方法; } else { 发起跨进程调用; })
4. 修改原有业务的调用代码，把它改为封装层的调用

(我们不生产代码，我们只是代码的搬运工)

## 3 完(卒)

(╯‵□′)╯︵┻━┻
(╯‵□′)╯︵┻━┻
(╯‵□′)╯︵┻━┻
(╯‵□′)╯︵┻━┻
(╯‵□′)╯︵┻━┻
(╯‵□′)╯︵┻━┻

为什么要这样对我(抱头痛哭)
为什么要这样对我(抱头痛哭)
为什么要这样对我(抱头痛哭)
为什么要这样对我(抱头痛哭)
为什么要这样对我(抱头痛哭)
为什么要这样对我(抱头痛哭)

难道就没有更简单的方式了吗？！
难道就没有更简单的方式了吗？！
难道就没有更简单的方式了吗？！
难道就没有更简单的方式了吗？！
难道就没有更简单的方式了吗？！
难道就没有更简单的方式了吗？！

## 4 你说你要更简单的?

让我们来审视下上面的方案的实现步骤：

1. 定义AIDL接口
2. 实现AIDL接口里的方法
3. 实现一个Service，在绑定的时候返回实现了AIDL接口Binder对象(被调用方)
4. 绑定Service，获得Binder对象，通过Binder对象进行方法调用(调用方)

### 4.1 简化封装层

慢着！

既然我们都需要实现AIDL接口了，为什么不把单例的实现和AIDL接口的实现整合起来？

也就是说：通过这种方式实现的单例的实例，是一个可以用于跨进程传输的对象！

进一步说：我们可以在绑定的时候，把这个单例(Binder)返回，其他进程只有得到这个Binder(RPC里的Proxy)，就能操作到我们这个单例了，而这个单例也就成为了我们应用程序范畴内所需要的单例。

想到了这一点，我们的封装层就可以废掉了。80%的体力活瞬间蒸发！

### 4.2 简化绑定处理过程

剩下的20%的体力活就变成了：

1. 定义AIDL接口，用单例对象实现这个AIDL接口
2. 使用到这个单例的都要执行一次绑定，绑定成功后，作为单例的实例保存下来即可。

第一点怎么都省不了了。但第二点呢？看起来是重复性很强的编码过程呢：

1. 修改Service实现，返回实现了AIDL的单例
2. onServiceConnected里，把得到的单例的代理，设为本进程的单例对象

如果能一次性就把所有的单例都传递过来，不就能少掉多次绑定调用，同时还统一了入口和出口。

写过AIDL的一定会跟另一个类打交道：Parcelable。Parcelable的实现需要需要我们处理数据的序列化和反序列化。在这里我们的入口和出口能实现统一，同时，Parcel对象还有两个重要的方法：`writeStrongInterface`和`readStrongBinder`，这两个方法实现了Binder对象的序列化和反序列化操作。

因此我们可以在这里把所有的单例通过`writeStrongInterface`序列化，传递到另一个进程，另一个进程再进行`readStrongBinder`，把对应的代理给取出来，并放置到单例里。

这样以来，我们的绑定处理过程就得到了简化。

## 5 Word is cheap, show me the code

[GayHub提交地址，基本框架和使用Sample](https://github.com/SR1s/AndroidPlayground/commit/de59913790b94ea65806cae5fecc9ae581d0c87d )

### 5.1 核心

说完了以上那么多，其实也就两个关键点：
1. 单例对象实现AIDL接口，以支持跨进程
2. Parcelable里统一序列化(Stub)和反序列化(Proxy)单例对象

### 5.2 实例-单例

这里假定有以下几个单例：

SingletonA（A表示是在A进程）
`SingletonA.aidl`是它的AIDL接口；
`SingletonAImp.java`是这个单例的实现。

SingletonB（B表示是在B进程）
`SingletonB.aidl`是它的AIDL接口；
`SingletonBImp.java`是这个单例的实现。

SingletonC（C表示是在C进程）
`SingletonC.aidl`是它的AIDL接口；
`SingletonCImp.java`是这个单例的实现。

获取他们的实例的方法统一为静态方法`getInstance`，代码如下，这里也是单例实现中唯一需要判断所处进程的地方：

    public static synchronized SingletonA getInstance() {
        if (ProcessUtils.isProcessA()) {
            if (INSTANCE == null) {
                INSTANCE = new SingletonAImp();
            }
            return INSTANCE;
        } else {
            if (INSTANCE == null) {
                /** 自发重连 */
                Intent intent = new Intent(
                    App.getContext(), ServiceA.class);
                App.getContext().bindService(intent, 
                    new InstanceReceiver(), 
                    Context.BIND_AUTO_CREATE);
            }
            return INSTANCE;
        }
    }

这个getInstance跟传统的单例不一样，它可能返回为空。

这里面有两个东西需要我们注意：
1. ServiceA.class
2. InstanceReceiver

### 5.3 实例-Service
ServiceA.class是A进程提供单例给其他进程的服务的类，每个进程都需要有一个(这样别的进程才能绑定过来)。所以在这个例子，会有ServiceB.class，ServiceC.class，这几个类的实现都是一样的，因此这里他们其实只是简单的继承了一个基类BaseService，并没有做其他改动，需要派生出来的原因是需要在AndroidManifest.xml里为不同进程指定一个Service。

代码如下：

    public class BaseService extends Service {
        @Nullable
        @Override
        public IBinder onBind(Intent intent) {
            return new InstanceTransferImp();
        }
    }
    
    public class ServiceA extends BaseService {}
    
    public class ServiceB extends BaseService {}
    
    public class ServiceC extends BaseService {}

### 5.4 实例-InstanceReceiver

InstanceReceiver.class是一个ServiceConnection的实现，这里把接收到的Binder对象转为一个`InstanceTransfer`，也就是封装的一个AIDL对象，这个对象的作用是把我们的单例传输过来。

代码：

    @Override
    public void onServiceConnected(ComponentName name, IBinder service){
        Log.i(TAG, "[onServiceConnected]" + name);
        try {
            /** 调用这句就会将单例(代理)实例传递过来了 */
            InstanceTransfer.Stub.asInterface(service).transfer();
        } catch (Exception e) {
            Log.e(TAG, "[onServiceConnected][exception when transfer instance]" + name, e);
        }
    }

    @Override
    public void onServiceDisconnected(ComponentName name) {
        /** 意外断开绑定的情况,这里可以重写成发起重连 */
        Log.e(TAG, "[onServiceDisconnected][exception when service disconnected]" + name);
    }

InstanceTransfer的定义：

    interface InstanceTransfer {
        InstanceCarrier transfer();
    }

### 5.5 InstanceCarrier

这里冒出了一个InstanceCarrier，这个InstanceCarrier实际上就是我们定义的一个`Parcelable`类，这个类干的事情，就是前面提到的：统一序列化(Stub)和反序列化(Proxy)单例对象。

代码大概是这样的：

    private static final String TAG = "InstanceCarrier";

    private static final int PROCESS_A = 1;
    private static final int PROCESS_B = 2;
    private static final int PROCESS_C = 3;

    /**
     * 在这里把单例转成IBinder传输到其他进程
     * @param dest
     * @param flags
     */
    @Override
    public void writeToParcel(Parcel dest, int flags) {

        if (ProcessUtils.isProcessA()) {
            dest.writeInt(PROCESS_A);
            dest.writeStrongInterface(SingletonAImp.getInstance());
            Log.i(TAG, String.format(
                        "[write][PROCESS_A][processCode=%s]", PROCESS_A));
        }else if (ProcessUtils.isProcessB()) {
            dest.writeInt(PROCESS_B);
            dest.writeStrongInterface(SingletonBImp.getInstance());
            Log.i(TAG, String.format(
                        "[write][PROCESS_B][processCode=%s]", PROCESS_B));
        }else if (ProcessUtils.isProcessC()) {
            dest.writeInt(PROCESS_C);
            dest.writeStrongInterface(SingletonCImp.getInstance());
            Log.i(TAG, String.format(
                        "[write][PROCESS_C][processCode=%s]", PROCESS_C));
        }
    }

    /**
     * 在这里把跨进程传递过来的IBinder赋值给对应的实例
     * @param in
     */
    protected InstanceCarrier(Parcel in) {

        int processCode = in.readInt();

        switch (processCode) {
            case PROCESS_A:
                SingletonAImp.INSTANCE = 
                        SingletonA.Stub.asInterface(in.readStrongBinder());
                Log.i(TAG, String.format(
                        "[read][PROCESS_A][processCode=%s]", processCode));
                break;
            case PROCESS_B:
                SingletonBImp.INSTANCE = 
                        SingletonB.Stub.asInterface(in.readStrongBinder());
                Log.i(TAG, String.format(
                        "[read][PROCESS_B][processCode=%s]", processCode));
                break;
            case PROCESS_C:
                SingletonCImp.INSTANCE = 
                        SingletonC.Stub.asInterface(in.readStrongBinder());
                Log.i(TAG, String.format(
                        "[read][PROCESS_C][processCode=%s]", processCode));
                break;
            default:
                Log.w(TAG, String.format(
                        "[unknown][processCode=%s]", processCode));
        }
    }

    public InstanceCarrier() {}

    @Override
    public int describeContents() {
        return 0;
    }

    public static final Creator<InstanceCarrier> CREATOR = new Creator<InstanceCarrier>() {
        @Override
        public InstanceCarrier createFromParcel(Parcel in) {
            return new InstanceCarrier(in);
        }

        @Override
        public InstanceCarrier[] newArray(int size) {
            return new InstanceCarrier[size];
        }
    };
    
这么一套下来，整个实现机制就搞好。

后续添加新的单例，只需要：
1. 定义单例的AIDL
2. 实现单例
3. 在InstanceCarrier里添加序列化和反序列化的两行代码
4. 如果添加了进程，需要在那个进程添加一个BaseService的派生类

如果是新增接口的话，也就简单的修改下AIDL文件，然后实现新的接口。

# 二 存在的问题或不足

0. 单例内使用到的数据类型，必须支持AIDL(Android IPC通讯的要求)，对于简单的数据，可以使用系统的Bundle对象
1. 实现的调用方法的时候，需要考虑到执行的线程可能不是调用的线程(跨进程调用的情况下是在Binder线程)，因为调用是同步的，对返回结果没有影响，但对于需要在主线程执行的逻辑来说，需要主动异步放到主线程去。
2. 线程安全：这个是编写单例的时候需要注意的问题，因为任何一个线程都能够访问到这个单例，使用这个方式支持跨进程可能会放大这个问题。
3. Android的IPC通讯机制本身的限制：Android的IPC通讯共享1M的内存，因此需要避免传输大量的数据，同时，处理逻辑也不宜很耗时(否则消费数据不及时，消费者处理能力低于生产者的生产力，迟早会耗光1M的内存)。