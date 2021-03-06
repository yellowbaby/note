* [概述](#概述)
* [用法](#用法)
	* [使用xml](#使用xml)
	* [使用java代码](#使用java代码)
* [常用API](#常用api)

### 概述
　逐帧动画是一种常见的动画形式（Frame By Frame），其原理理是在“连续的关键帧”中分解动画动作，也就是在时间轴的每帧上逐帧绘制不不同的内容，使其连续播放而成动画
　因为逐帧动画的帧序列列内容不一样，不但给制作增加了负担而且最终输出的文件量量也很大
　但它的优势也很明显：逐帧动画具有非常大的灵活性，几乎可以表现任何想表现的内容，而它类似与电影的播放模式，很适合于表演细腻的动画
 
### 用法
#### 使用xml

 1. drawable中定义my_animlist.xml

``` xml
<?xml version="1.0" encoding="utf-8"?>
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot="true" >
    <!-- android:oneshot 动画只演示⼀一次-->
    <item android:drawable="@drawable/p1" android:duration="200"/>
    <item android:drawable="@drawable/p2" android:duration="200"/>
    <item android:drawable="@drawable/p3" android:duration="200"/>
    <item android:drawable="@drawable/p4" android:duration="200"/>
    <item android:drawable="@drawable/p5" android:duration="200"/>
    <item android:drawable="@drawable/p6" android:duration="200"/>
    <item android:drawable="@drawable/p7" android:duration="200"/>
    <item android:drawable="@drawable/p8" android:duration="200"/>
</animation-list>
```

 2. 布局文件src引用

``` xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="app.yellow.frameanimation.MainActivity">

    <ImageView
        android:id="@+id/iv"
        android:src="@drawable/my_animlist"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
</RelativeLayout>

```

 3. Activity中调用start

``` java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        ImageView iv = (ImageView) findViewById(R.id.iv);
        AnimationDrawable drawable = (AnimationDrawable) iv.getDrawable();
        drawable.start();//开始动画
        //drawable.stop();//停止动画

    }
}
```

#### 使用java代码

``` java

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        ImageView iv = (ImageView) findViewById(R.id.iv);
        AnimationDrawable anim = new AnimationDrawable();
        for (int i = 1; i <= 8; i++) {
            int id = getResources().getIdentifier("p" + i, "drawable", getPackageName());
            Drawable drawable = getResources().getDrawable(id);
            anim.addFrame(drawable, 200);
        }
        anim.setOneShot(false);
        iv.setImageDrawable(anim);
        anim.start();
    }
}
```


### 常用API

 1. void start() - 开始播放动画
 2. void stop() - 停止播放动画
 3. addFrame(Drawable frame, int duration) - 添加一帧，并设置该帧显示的持续时间
 4. void setOneShoe(boolean flag) - false为循环播放，true为仅播放一次
 5. boolean isRunning() - 是否正在播放