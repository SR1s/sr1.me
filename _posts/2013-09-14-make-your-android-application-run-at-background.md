---
title: Android拦截返回键实现应用后台运行
category: way-to-explore
layout: post
---

# 目录 

1. 应用后台运行 

2. 拦截返回键 

3. 拦截返回键实现应用后台运行 

4. 再说后台运行

# 应用后台运行

在Activity中有个“moveTaskToBack(boolean nonRoot)”方法，从名字上就知道是把任务放到后台(即Activity不调用“finish()”方法)运行。参数为false代表只有当当前Activity为程序的根(即应用启动后的第一个Activity)时候才能放到后台，参数为true代表任意Activity都可以放到后台运行，而不被销毁(被finish())。

# 拦截返回键

要拦截返回键有两个方法:一是重载Activity的“onBackPressed()”方法；一是重载Activity的“onKeyDown(int keyCode, KeyEvent event)”方法，对参数“keyCode==KeyEvent.KEYCODE_BACK”的状况进行处理。

# 拦截返回键实现应用后台运行

以上两点相结合就能实现***拦截返回键实现后台运行***的效果。

第一种方法：重载“onBackPressed()”方法，代码如下：

    @Override
    public void onBackPressed() {
        moveTaskToBack(false);
    }

第二种方法：“onKeyDown(int keyCode, KeyEvent event)”方法，代码如下：

    @Override
    public boolean onKeyDown(int keyCode, KeyEvent event) {
        if (keyCode == KeyEvent.KEYCODE_BACK) {
            moveTaskToBack(false);
            return true;
        }
        return super.onKeyDown(keyCode, event);
    }

# 结合这个实现，再说说进入后台的方法参数取值的不同对实现效果的影响

以微信为例，假设微信的每个Activity都实现了这个效果，并且每个Activity的调用的方法“moveTaskToBack(boolean nonRoot)”的参数都一样。

当我们点击图标进入微信的主Activity(显示最近会话列表)，再点击进入一个会话的Activity，此时：

若参数为**false**，在这个Activity点击返回键，并不会让应用进入后台运行，而是返回到上一个Activity(也就是主Activity)，在主Activity点击返回键，才会让应用进入后台。

若参数为**true**，在这个Activity点击返回键，应用不会返回到上一个Activity(主Activity)，而是进入后台运行，也就是不管在哪个实现了这个效果的Activity，点击返回键都是直接进入后台运行。

因此，使用的时候要结合具体的需求，选择合适的取值，以免出现不符合预期的效果。

# 参考资料： 

[android 应用退到后台，类似最小化][1]

[Android按返回键退出程序但不销毁][2]

 [1]: http://blog.csdn.net/cool_ping/article/details/8237995
 [2]: http://blog.csdn.net/android_xiaoqi/article/details/8769327
