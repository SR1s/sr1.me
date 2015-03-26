---
title: Android最佳实践之UI篇
category: way-to-explore
layout: post
---

# 引子
不管进行什么开发，桌面也好、移动端也罢，UI一直都是让人头大的一部分。那对于Android开发来说，在UI这一块，是否有什么最佳实践能让人少走一些弯路吗？这两天就这个问题搜了一圈，收获了不少。

# UI最佳实践的N条建议

## 1. 避免嵌套过多层级的布局

即使使用的全都是官方提供的基础布局和控件，也不意味着就能做出高效的UI布局设计。每个布局(layout)，控件(Button、TextView等)，都需要进行初始化，测量大小、定位以及绘制。布局里嵌套了过多的层级将带来相当大的性能开销。官方提供了Hierarchy Viewer工具来帮助我们查找可能的优化点。Hierarchy Viewer的使用方式这里就不作介绍了，官方文档说得很清楚。通过它我们能查看各个页面的布局层次和以及各个步骤(测量、定位、绘制)的耗时，并根据这些数据做出相应的优化。([使用文档传送门](http://developer.android.com/training/improving-layouts/optimizing-layout.html))

使用线性布局(LinearLayout)来组织界面是导致层级过多的主要原因，由于这种组织方式相当直观，因此深受新手的喜爱。为了避免这部分的开销，一般使用相对布局(RelativeLayout)来重组界面。使用相对布局能够很方便的将界面由层次多、每层控件少的狭长式树形机构，转换成层次少、每层控件多的扁平式树形结构。从而得到可观的性能提升。


## 2. 避免使用layout_weight属性

layout_weight属性能够让我们根据实际设备的界面大小来动态的调整控件的尺寸。但在Android系统的实现上，对每个指定了layout_weight属性的布局、控件，系统都会执行两次的测量计算。这个问题看起来似乎没什么，但在需要重复解析渲染控件的场合(ListView， GridView)将会由于重复计算变得更加严峻。因此要避免使用layout_weight属性。

那么在平时需要使用到layout_weight属性的场合，如何将layout_weight属性优化掉呢？举个例子：假如需要在水平方向上有两个按钮(Button)，我们想要让他们的宽度都是总宽度的一半。

使用layout_weight的做法是这样的：

    <LinearLayout 
        android:width="match_parent"
        android:height="wrap_content"
        android:orientation="horizontal"
        >
        <Button
            android:width="0dp"
            android:height="wrap_content"
            android:weight="1"
            />
        <Button
            android:width="0dp"
            android:height="wrap_content"
            android:weight="1"
            />
    </LinearLayout>

使用RelativeLayout的做法是这样的：

    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >
        <View
            android:id="@+id/divider"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_centerHorizontal="true"
            />
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_toLeftOf="@+id/divider"
            android:layout_alignParentLeft="true"
            />
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_toRightOf="@+id/divider"
            android:layout_alignParentRight="true"
            />
    </RelativeLayout>

是不是很简单呢？

## 3. 使用Lint工具来帮你找到可能的优化点

Lint工具是Android官方提供的一个优化点扫描工具，它会在每次构建APK包的时候运行。它会根据预设的规则，给出相应的优化建议。举几个例子：

1. 使用合适的Drawable：一个包含了ImageView和TextView的LinearLayout，用一个复合的Drawable来替代将会更加高效。
2. 用merge标签替代根节点是帧布局(FrameLayout)的布局：如果一个FrameLayout是一个布局文件的根布局，且没有设置内边距或背景等属性，那么可以用merge标签来代替。这会稍微提升下性能。
3. 移除无用的叶节点：如果一个布局没有子布局、没有子控件，也没有设置背景，那么这个布局将会是不可见的，因此也是可以移除的。
4. 移除无用的父节点：如果一个布局(1)不是ScrollView、(2)不是根节点、(3)只有一个子节点、(4)没有设置背景，那么它的子节点可以直接提取到这个父节点的层级上，代替父节点，以便得到一个更加扁平和高效的布局结构。
5. 层级过多的布局：层级过身将导致糟糕的性能。尽可能的使用RelativeLayout和GridLayout，让布局扁平化。布局层次的最大限制是10层。

## 4. 重用可重用的布局
将在多个布局中会用到的部分抽离出来放在一个xml文件中。然后使用include标签来导入这个布局。抽离出来的布局文件的根节点布局就是你希望它导入其他布局文件之后出现在那个位置的布局，如果不需要这样一个布局，则可以用merge标签作为根节点。这两种有什么不同呢？举个例子：

    <!-- 假设布局文件叫做common_layout.xml -->
    <LinearLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        >
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            />
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            />
    </LinearLayout>

    <!-- 假设布局文件叫做common_widget.xml -->
    <merge
        xmlns:android="http://schemas.android.com/apk/res/android"
        >
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            />
        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            />
    </merge>

假如导入common_layout.xml，标签是这样的：

    <LinearLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >
        <include layout="@layout/common_layout" />
    </LinearLayout>

导入之后，include标签区域实际上会被common_layout.xml里从LinearLayout开始的全部布局、控件代替。

那如果导入common_widget.xml呢？导入的标签跟上面一样，只不过layout属性的值换成了@layout/common_widget：

    <LinearLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >
        <include layout="@layout/common_widget" />
    </LinearLayout>

导入之后，include标签实际上会被两个Button代替，也就是使用merge标签的话，导入的时候merge标签会被忽略，merge标签下的控件会被直接放置在文件之中。

还有另一种重用布局的方式：使用fragment。碎片(fragment)是Android 3.0以后引进的一个类似Activity的组件。相比Activity，它更加的轻量，启动和加载更加的快，而且它可以根据需要加载和切换，并且不需要在AndroidManifest.xml文件中声明就能使用，但它必须依赖于Activity才能使用。顾名思义，fragment能够用来组织“小”块的布局，但除此之外，它还能封装一系列的逻辑。因此对于可以重用的布局，比如自定义的对话框，可以使用Fragment来组织管理，方便在代码中重用。

## 5. 根据需要来加载布局

有些布局内容(如进度条指示器，某个按钮点击后才会出现的额外内容等)并不需要一开始就显示在界面上，一般在开发中会将其可见性设置为invisible或者gone，在需要时候再设置为visible。虽然一开始这些内容以及没显示在界面上了，但实际上在界面初始化的时候，这些内容还是会被加载的。对于这种状况，使用ViewStub标签再适合不过了。

首先将需要动态加载的布局抽取出来到一个xml文件中。不过ViewStub不支持merge标签，这点要注意。假设抽取出来的文件是common_layout.xml，然后将ViewStub里的android:layout属性指向这个文件。如下示例代码：

    <ViewStub
        android:id="@+id/viewstub"
        android:inflatedId="@+id/inflated_layout"
        android:layout="@layout/common_layout"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        />

像上面这样，xml中就定义完毕啦。inflatedId属性稍后再解释。那代码中怎么使用呢？同样很简单。有两种方式：

    // 方式一
    findViewById(R.id.viewstub).setVisibility(View.VISIBLE);
    // 方式二
    View view = ((ViewStub) findViewById(R.id.viewstub)).inflate();

通过以上调用，就能把界面给加载出来了。inflatedId属性的值将会在加载完毕之后赋值给被加载页面的根节点的id属性，在这个例子里是common_layout里的最外层LinearLayout。

为什么需要这个属性呢？事情是这样的。ViewStub在加载完毕之后就从界面上消失了，这意味着通过ViewStub的id再也无法索引到这个ViewStub了。那么如果我们需要去动态调整加载进来的界面的控件该怎么办呢？inflatedId这个属性就是帮我们找到这个布局的关键了。如果使用的是方法二来加载页面，inflate()方法调用将会返回加载出来的布局的View给你，通过这个View也是可以操作加载进来的页面的。

## 6. 使用后台线程，让ListView流畅滚动

有经验的开发者经常建议：不要在主线程进行耗时的操作。但对于初学者来说，到底什么是主线程呢？这个问题我琢磨了很久。主线程其实就是UI线程，这个线程负责处理跟UI相关的操作。界面的绘制、界面的加载、控件的调整等操作都是由主线程来执行的。UI线程之所以被称作主线程，是因为在绝大多数情况下，我们写的代码都会在跑在UI线程里，比如界面的各个生命周期的回调方法(onCreate()，onResume()， onPause()等)。

由于主线程处理跟UI相关的操作，如果主线程处理耗时的操作(文件处理、网络请求等)，那么就会导致主线程无法及时处理其他的UI操作请求，这样我们在界面上就会明显的感觉到卡顿。这个问题在处理ListView这种需要重复的绘制列表元素的情况下会变得更加严峻。因此需要小心处理ListAdapter里getView()方法里可能存在的耗时操作。

但使用后台线程就要接触多线程了，而多线程操作又是众所周知的坑多。如果不想自己手动去维护自己的线程池或实现自己的多线程机制，那么可以使用AsyncTask类来实现后台处理任务的需求。这是Android提供的一个非常便捷的方法。

    // 使用AsyncTask在后台加载一张加载很慢的图片
    // 这里的三个泛型参数的意义分别是：
    // 1) UI控件、2）进度回调的类型、3)任务执行完毕后的返回类型
    new AsyncTask<ViewHolder, Void, Bitmap>() {

        // ViewHolder技术是实现复杂ListView的一个优化性能的技术，后面会介绍
        private ViewHolder v;

        // doInBackground()方法在后台线程执行
        @Override
        protected Bitmap doInBackground(ViewHolder... params) {
            v = params[0]; // 这里保存一份界面的引用

            // 这里假定有一个图片加载器，getImage()方法是它的一个耗时方法
            return mFakeImageLoader.getImage();
        }

        // onPostExecute()方法在主线程执行，确保所有耗时的操作在doInBackground()方法做完了
        @Override
        protected void onPostExecute(Bitmap result) {
            super.onPostExecute(result);
            if (v.position == position) {
                v.progress.setVisibility(View.GONE);
                v.icon.setVisibility(View.VISIBLE);
                v.icon.setImageBitmap(result);
            }
        }

    }.execute(holder);

从Android 3.0(API 11)开始，AsyncTask增加了一个方法：AsyncTask.executeOnExecutor()。这个方法会利用处理器的多核特性，进一步提升性能。这个方法的具体效果取决于具体设备的处理器核心数。

## 7. 使用ViewHolder技术来优化ListView

前面说到，在ListView这种需要重复的绘制列表元素的情况下，性能问题将变得更加严峻。由于内存的原因，ListView只有一个固定数量的View列表来显示列表的每一项，通过回收不可见的项，重新调整控件来显示新出现在界面上的项。因此在ListView的滚动过程中，将会频繁的调用ListAdapter.getView()方法。通过上面的优化建议，我们已经把耗时操作放到后台线程去执行了，只把必须的UI操作留在主线程。那还能不能再优化呢？答案是肯定的。

如果有经常在网上查找教程，那么应该对ListView的教程里的ViewHolder技术不陌生。这个技术的核心是减少主线程里的执行步骤，以达到优化性能的目的。通过使用ViewHolder，缓存每个View的引用，减少不必要的查找控件操作，以达到优化ListView性能的效果。

首先需要创建一个ViewHolder类来持有View的引用：

    static class ViewHolder {
        TextView text;
        TextView timestamp;
        ImageView icon;
        ProgressBar progress;
    }

然后实例化ViewHolder并为其赋值，再将ViewHolder存储在view的tag里：

    ViewHolder holder = new ViewHolder();
    holder.text = (TextView) convertView.findViewById(R.id.list_item_text);
    holder.timestamp = (TextView) convertView.findViewById(R.id.list_item_timestamp);
    holder.icon = (ImageView) convertView.findViewById(R.id.list_item_icon);
    holder.progress = (ProgressBar) convertView.findViewById(R.id.list_item_progress);
    convertView.setTag(holder);

之后就可以从convertView的tag里取出ViewHolder来直接访问对应的控件进行操作了。

    ViewHolder holder = (ViewHolder) convertView.getTag();
    holder.text.setText(text);
    // ...这里省略对其他控件的操作

## 8. 为不同尺寸、像素密度的设备提供对应分别率的图片资源

这个在学习Android工程目录结构的时候就应该有所了解。现在一般会提供mdpi、hdpi、xhdpi、xxhdpi四种大小的资源图片，这样就能保证你的应用在绝大部分设备上的拥有良好的图片显示效果。需要注意的是ldpi这个规格已经废弃了，不需要再提供这个大小的资源。如果你的程序会运行在比xxhdpi更大更精细尺寸的设备，可以考虑再提供一个xxxhdpi尺寸的资源文件，否则以上四种就足够了。

## 9. 为自定义控件的不同状态提供合适的表现

如果你实现了自己的控件，那么很有必要创建一个drawable为控件可能处于的状态提供对应的表现。这些表现是用户和控件交互能获得的直观反馈，设置控件不同状态下对应的表现的drawable文件大致如下：

    <?xml version="1.0" encoding="utf-8" ?>   
    <selector xmlns:android="http://schemas.android.com/apk/res/android"> 
        <!-- 默认时的背景图片 -->  
        <item android:drawable="@drawable/button_default" />    
        <!-- 没有焦点时的背景图片 -->  
        <item 
            android:state_focused="false"
            android:drawable="@drawable/button_default" />   
        <!-- 非触摸模式下获得焦点并单击时的背景图片 -->
        <item
            android:state_focused="true"
            android:state_pressed="true"   
            android:drawable= "@drawable/button_pressed" />  
        <!-- 触摸模式下单击时的背景图片 -->
        <item 
            android:state_focused="false" 
            android:state_pressed="true"   
            android:drawable="@drawable/button_pressed" />   
        <!--选中时的图片背景  -->  
        <item 
            android:state_selected="true"   
            android:drawable="@drawable/button_selected" />   
        <!--获得焦点时的图片背景  -->  
        <item 
            android:state_focused="true"   
            android:drawable="@drawable/button_selected" />   
    </selector>

## 10. 使用字体

Android系统自带了两种字体：Droid Sans和Roboto。其中Roboto字体是Android 4.0之后添加的字体，这款字体更加紧凑，在小屏幕设备上也有很好的显示效果。Android也支持使用TTF格式的自定义的字体，可以将字体放置在Asset文件夹下，也可以通过互联网下载来使用自定义字体。放在Asset文件夹下的字体由于会打包到安装包中，因此会稍微增大安装包体积，假如使用的是中文字体，那就更大了。如果只是程序的某些文字需要用到一些特殊的字体，可以考虑精简字体库，或者使用图片来代替。使用自定义字体的代码如下：

    Typeface font = Typeface.createFromAsset(getAssets(), "my_font.ttf");
    ((TextView)findViewById(R.layout.MyTextView)).setTypeface(font);

## 11. 使用点九图来适应不同大小的控件

点九图是一种特殊处理过的png图片，系统能够根据需要缩放图片的大小，去适应不同大小的控件。比如QQ和微信的聊天对话框里的气泡效果，长文字和短文字的气泡背景的显示效果都很好，这就是点九图能够实现的效果。

TODO 如何制作点九图

## 12. 对于简单的图形图片，尽可能使用向量图形来绘制。

Android提供了一种机制，允许通过xml来绘制简单的图形。因此对于简单的图形图片，如果可能使用xml来绘制它。由于绘制出来的图形是基于矢量的，因此这个图形文件拥有良好的伸缩性，同时也能够减少内存的占用。图片资源对内存的消耗很明显，如果使用图片过多的话，程序将会使用很多的内存来缓存图片，而且很容易引起OOM(OutOfMemory)错误。

TODO 通过xml绘制基础的图形

## 13. 巧妙使用android:tint属性来改变图片颜色

Android 5.0提供了一个新的属性android:tint。这个属性允许我们设置一个叠加颜色来改变图片的颜色，效果类似于在PhotoShop里设置ColorOverlay属性。那么对于Android 5.0之前的机器，由于没有这个属性，就无法实现同样的效果了吗？不是这样的，虽然xml属性没有，但我们可以通过代码来改变它。

    // 这段代码实现了将support v7包提供的返回按钮图片设置为白色的功能
    Drawable drawable = getDrawable(R.drawable.abc_ic_ab_back_mtrl_am_alpha);
    drawable.setColorFilter(getResources().getColor(R.color.white), PorterDuff.Mode.SRC_IN);

有了这种方式，就可以通过使用一张图片来变化出不同颜色的效果。再也不需要把为同一张图片生成不同颜色的版本，在放置在资源文件夹里管理了。

## 14. 使用样式(style、dimen、color、string)将布局文件和样式剥离

Android里的样式有点像CSS里的类。样式允许我们将一组属性集合起来，并指定一个名字，然后在其他地方通过引用这个名字来使用这组效果。样式还允许继承，然后通过重写父样式的某些属性来覆盖父样式的属性。这种理念遵循了一个很古老的编码原则(DRY: Don't Repeat Yourself)。散落一地的代码将会为你维护代码的工作带来额外的挑战。

举个例子，在编写xml过程中，layout_width和layout_height是两个必不可少的属性。我们可以通过定义如下四个样式来减少我们的代码书写量。

    <style name="Match">
      <item name="android:layout_width">match_parent</item>
      <item name="android:layout_height">match_parent</item>
    </style>

    <style name="Wrap">
      <item name="android:layout_width">wrap_content</item>
      <item name="android:layout_height">wrap_content</item>
    </style>

    <style
      name="MatchHeight"
      parent="Match">
      <item name="android:layout_width">wrap_content</item>
    </style>

    <style
      name="MatchWidth"
      parent="Match">
      <item name="android:layout_height">wrap_content</item>
    </style>

记住：保持布局xml文件的可维护性的秘诀是吧样式属性和定位属性区分开来。

## 15. 使用主题

主题是一系列决定应用程序外观样式的集合。系统内置了多重主题，如Android 4.0推出的Holo主题，Android 5.0推出的MaterialDesign。

基于主题做适当的定制化，可以使应用既具备自己的个性，又不会和系统的整体体验相差太远。主题在样式(style)文件里重写，在AndroidManifest.xml指定，根据需要你可以设定为全应用范围生效(在application标签的android:theme属性指定)，也可以设置为某一个Activity内生效(在activity标签的android:theme属性指定)。

另外需要注意的是，系统根据资源文件里的标签(resources下的style、selector、color等)来识别资源文件里定义的资源，而不是根据资源文件的文件名来定位。因此可以放心组织你的资源到不同的资源文件中去。

## 16. 保持资源的名字结构清晰、意思明确

1. 对于id资源，可采用这样的命名方式：哪个页面_哪种控件_代表含义，如：login_edt_username表示login页面下一个表示用户名的EditText。
2. 对于图片(drawable)资源，可以采用这样的命名方式：类型_哪种控件_含义，如：ic_ab_edit表示这是一个表示便捷的ActionBar图标(ic表示icon，ab表示ActionBar)。对于普通图片标，则可以把中间的哪种控件省略。
3. 对于颜色(color)资源，一般用作调色板使用，推荐采用通用的指代颜色的名称命名。对于主题相关的再额外定义一个文件，指向这些颜色。

## 17. 对在不同工程中通用的资源，采用通用的命名，以便在不同工程中重用这部分的资源。

例如通用的颜色资源：

    <?xml version="1.0" encoding="utf-8">
    <resources>
        <color name="primarycolor_dark">#0e5a83</color>
        <color name="primarycolor">#0680c3</color>
        <color name="primarycolor_light">#489dca</color>
        <color name="secondary_light">#999999</color>
        <color name="secondary">#565656</color>
        <color name="secondary_dark">#4b4b4b</color>
        <color name="secondary_extraDark">#231f20</color>
        <color name="highlight_one">#35791d</color>
        <color name="highlight_two">#ff5151</color>
        <color name="main_bg">#ecf0f3</color>
        <color name="button_pressed">#036194</color>
        <color name="button_default">#0680c3</color>
        <color name="button1_pressed">#c04033</color>
        <color name="button1_default">#df4534</color>
        <color name="button2_pressed">#279030</color>
        <color name="button2_default">#37ab41</color>
        <color name="button3_pressed">#4f4f4f</color>
        <color name="button3_default">#757575</color>
        <color name="line_bg">#d6d6d8</color>
    </resources>

以及通用的字体字号资源：

    <?xml version="1.0" encoding="utf-8">
    <resources>
        <dimen name="text_mirco">12sp</dimen>
        <dimen name="text_ultraMini">13sp</dimen>
        <dimen name="text_mini">14sp</dimen>
        <dimen name="text_ultraSmall">16sp</dimen>
        <dimen name="text_small">17sp</dimen>
        <dimen name="text_moderate1">18sp</dimen>
        <dimen name="text_moderate2">19sp</dimen>
        <dimen name="text_moderate3">21sp</dimen>
        <dimen name="text_big">25sp</dimen>
        <dimen name="text_extraBig">31sp</dimen>
    </resources>

这里为了直观，颜色资源直接使用了#FFFFFF形式的值。

## 18. 使用Toolbar、ActionBar或者支持库(support library)提供的同等控件

如果你的应用的界面使用了包含ActionBar的设计，那么使用SDK提供的Toolbar或ActionBar来实现。不要重复发明轮子，记住这个编程原则。安卓支持库v7(support library v7)为适配Android 2.1+的系统提供了支持。如果你使用ActionBar，那么使用ActionBar样式生成器来方便的定制化它。生成器地址([传送门](http://jgilfelt.github.io/android-actionbarstylegenerator/))

## 19. 让系统帮你完成适配。
Android系统提供了自动根据设备显示能力使用最合适的资源的能力。基本所有的资源都支持通过一定规则来动态切换。如可以通过提供一个res/layout-small/文件夹来为小尺寸的设备提供定制化的布局。


## 20. That's all, But NOT ALL.