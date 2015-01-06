---
title: Java中的动态代理
category: way-to-explore
layout: post
---

# 动态代理

代理模式是一种为另一个对象提供一个替身或占位符以便控制对这个对象的访问的一种设计模式。[Wiki](http://zh.wikipedia.org/wiki/%E4%BB%A3%E7%90%86%E6%A8%A1%E5%BC%8F )

以Android开发为例。众所周知，Android系统碎片化很严重，倘若我们需要自行开发一个播放器，在高版本的系统使用新的API来实现，而在低版本的系统使用低版本的API来实现。为了让这两个不同的播放器实现跟调用方解耦，降低代码之间的耦合度，这个时候我们就可以通过使用代理模式提供一个代理播放器，从而隐藏我们底层不同播放器的实现。这里主要想分享下两种不同的代理模式的实现，同时对下这几天学习动态代理模式过程中遇到的问题做一个记录。

# 普通的实现方式

    /**
     * 播放器的接口
     */
    interface Player {
      public void play();
      public void stop();
      public void pause();
      public void next();
    }
    
    /**
     * 旧版本系统使用的播放器实现
     */
    class OldPlayer implements Player {
      @Override public void play() {
        System.out.println("Old player start play.");
      }
    
      @Override public void stop() {
        System.out.println("Old player stop play.");
      }
      
      @Override public void pause() {
        System.out.println("Old player pause play.");
      }

      @Override public void next() {
        System.out.println("Old player play next.");
      }
    }
    
    /**
     * 新版本系统使用的播放器实现
     */
    class NewPlayer implements Player {
      @Override public void play() {
        System.out.println("New player start play.");
      }
      
      @Override public void stop() {
        System.out.println("New player stop play.");
      }
      
      @Override public void pause() {
        System.out.println("New player pause play.");
      }
      
      @Override public void next() {
        System.out.println("New player play next.");
      }
    }
    
    /*
     * 代理播放器，根据不同的系统版本加载不同的播放器实现
     */
    class ProxyPlayer implements Player {
    
      // 实际执行所要求动作的播放器
      private Player player;
      private Random rand = new Random(System.currentTimeMillis());

      public ProxyPlayer() {
        // 构造的时候根据不同情况创建不同版本的播放器对象
        if (isOldVersion()) {
          player = new OldPlayer();
        } else {
          player = new NewPlayer();
        }
      }

      private boolean isOldVersion() {
        // 这里用随机数来模拟不同系统环境
        return rand.nextBoolean();
      }
      
      @Override public void play() {
        player.play();
      }
      @Override public void stop() {
        player.stop();
      }
      
      @Override public void pause() {
        player.pause();
      }
      
      @Override public void next() {
        player.next();
      }
    }
    
    /*
     * 测试类
     */
    public class ProxyPattern {
    
      public static void main(String[] args) {
        Player player = new ProxyPlayer();

        player.play();
        player.pause();
        player.play();
        player.next();
        player.stop();
      }
    }

传统的实现方式是再创建一个类，即上面的`ProxyPlayer`，在这个类里根据需要去动态的创建实际的实现类对象`player`，然后将每个调用操作都委托给这个实际的对象去执行。这种实现方式很简单、直观，但缺点是我们要对每一个调用操作都写上委托调用的语句，如果方法多了，编写这么多类似的代码其实很影响效率。

# 动态代理的方式

Java里还有一种方式可以实现代理模式，这种实现方式称之为动态代理。先看下代码。

    class PlayProxyHandler implements InvocationHandler {
      private Player player;
      private Random rand = new Random(System.currentTimeMillis());
      
      public PlayProxyHandler() {
        // 构造的时候根据不同情况创建不同版本的播放器对象
        if (isOldVersion()) {
            player = new OldPlayer();
        } else {
            player = new NewPlayer();
        }
      }
      
      private boolean isOldVersion() {
        return rand.nextBoolean();
      }
      @Override
      public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        return method.invoke(player, args);
      }
    }

    public class ProxyPattern {
      public static void main(String[] args) {
      
        // 通过这种方式，在程序运行时，创建并实例化一个代理类
        Player player = (Player) Proxy.newProxyInstance(ClassLoader.getSystemClassLoader(),
                new Class[]{ Player.class },
                new PlayProxyHandler());

        player.play();
        player.pause();
        player.play();
        player.next();
        player.stop();
      }
    }

这是跟第一种实现的差异代码，重复的代码就没贴上来了(`Player`接口以及两个对应的实现类)。跟第一种方式比较，确实可以把我们从繁琐的代码编写中解放出来，提高效率。

使用动态代理技术的核心就是上面的`InvocationHandler`接口，以及`public static Object Proxy.newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h)`方法。

`Proxy.newProxyInstance`方法根据我们传进去的常数来创建一个代理类(可以理解为一个作用类似第一种方法里的`ProxyPlayer`的类)，并返回一个它的对象。这个方法的几个参数解释一下：

1. `ClassLoader loader`：从名字就能看出来，这是一个用来加载类的对象。可以理解为就是它将生成的代理类导入我们的运行时环境的。
2. `Class<?>[] interfaces`：生成的代理类要实现的接口数组，接口的所有方法都会被这个代理类代理。
3. `InvocationHandler`：由这个接口的实现来将每个方法分发给实际的实现类。

关于动态代理这部分的实现有好多可以说的。限于时间关系先写到这。后面再补上这部分的笔记。

That's all. But not ALL.
