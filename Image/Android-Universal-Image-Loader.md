* [简介](#简介)
* [特性](#特性)
* [配置方法](#配置方法)
* [图片加载](#图片加载)
	* [loadImage方法加载](#loadimage方法加载)
	* [displayImage方法加载](#displayimage方法加载)
	* [GirdView,ListView加载图片](#girdviewlistview加载图片)

### 简介
　　[Android-Universal-Image-Loader][1]是一个流传已久著名的图片加载框架
### 特性
 1. 多线程下载图片，图片可以来源于网络，文件系统，项目文件夹assets中以及drawable中等
 2. 支持随意的配置ImageLoader，例如线程池，图片下载器，内存缓存策略，硬盘缓存策略，图片显示选项以及其他的一些配置
 3. 支持图片的内存缓存，文件系统缓存或者SD卡缓存
 4. 支持图片下载过程的监听
 5. 根据控件(ImageView)的大小对Bitmap进行裁剪，减少Bitmap占用过多的内存
 6. 较好的控制图片的加载过程，例如暂停图片加载，重新开始加载图片，一般使用在ListView,GridView中，滑动过程中暂停加载图片，停止滑动的时候去加载图片
 7. 提供在较慢的网络下对图片进行加载
 
 ### 配置方法
 
1. 添加依赖

``` xml
dependencies 
    ...
    compile 'com.nostra13.universalimageloader:universal-image-loader:1.9.5'
}
```

2. 创建全局的ImageLoader配置参数并且初始化

``` java
public class MyApplication extends Application{

    @Override
    public void onCreate() {
        super.onCreate();

        //创建默认的ImageLoader配置参数  
        ImageLoaderConfiguration configuration = new ImageLoaderConfiguration.Builder(this)  
        .writeDebugLogs() //打印log信息  
        .build();  

        //Initialize ImageLoader with configuration.
        ImageLoader.getInstance().init(configuration);
    }
}
```

3. 修改AndroidManifest.xml

``` xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <!-- Include following permission if you load images from Internet -->
    <uses-permission android:name="android.permission.INTERNET" />
    <!-- Include following permission if you want to cache images on SD card -->
	
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

    <application
        android:name=".MyApplication"
		...
    </application>

</manifest>
```

 ### 图片加载
 ####  loadImage方法加载
  1. 带完整监听的加载方法
 
``` java
    public void loadImageByListener() {
        final ImageView mImageView = (ImageView) findViewById(R.id.image);
        String imageUrl = "https://www.baidu.com/img/bd_logo1.png";
        ImageLoader.getInstance().loadImage(imageUrl, new ImageLoadingListener() {
            @Override
            public void onLoadingStarted(String imageUri, View view) {
            }

            @Override
            public void onLoadingFailed(String imageUri, View view,
                                        FailReason failReason) {
            }

            @Override
            public void onLoadingComplete(String imageUri, View view, Bitmap loadedImage) {
                mImageView.setImageBitmap(loadedImage);
            }

            @Override
            public void onLoadingCancelled(String imageUri, View view) {
            }
        });
    }
```

 2. 带监听适配器的加载方法
 
``` java
    public void loadImageByListenerAdapter() {
        final ImageView mImageView = (ImageView) findViewById(R.id.image);
        String imageUrl = "https://www.baidu.com/img/bd_logo1.png";
        ImageLoader.getInstance().loadImage(imageUrl, new SimpleImageLoadingListener() {
            @Override
            public void onLoadingComplete(String imageUri, View view,
                                          Bitmap loadedImage) {
                super.onLoadingComplete(imageUri, view, loadedImage);
                mImageView.setImageBitmap(loadedImage);
            }
        });
    }
```

 3. 使用DisplayImageOptions定制加载图片

``` java
    public void loadImageByDisplayImageOptions() {
        final ImageView mImageView = (ImageView) findViewById(R.id.image);
        String imageUrl = "https://www.baidu.com/img/bd_logo1.png";

        ImageSize mImageSize = new ImageSize(100, 100);//指定图片大小

        //显示图片的配置
        DisplayImageOptions options = new DisplayImageOptions.Builder()
                .cacheInMemory(true)
                .cacheOnDisk(true)
                .bitmapConfig(Bitmap.Config.RGB_565)
                .build();

        ImageLoader.getInstance().loadImage(imageUrl, mImageSize, options, new SimpleImageLoadingListener() {
            @Override
            public void onLoadingComplete(String imageUri, View view,
                                          Bitmap loadedImage) {
                super.onLoadingComplete(imageUri, view, loadedImage);
                mImageView.setImageBitmap(loadedImage);
            }
        });
```

 
``` java
/**
*  DisplayImageOptions的全部属性
*/
DisplayImageOptions options = new DisplayImageOptions.Builder()
        .showImageOnLoading(R.drawable.ic_stub) // resource or drawable
        .showImageForEmptyUri(R.drawable.ic_empty) // resource or drawable
        .showImageOnFail(R.drawable.ic_error) // resource or drawable
        .resetViewBeforeLoading(false)  // default
        .delayBeforeLoading(1000)
        .cacheInMemory(false) // default
        .cacheOnDisk(false) // default
        .preProcessor(...)
        .postProcessor(...)
        .extraForDownloader(...)
        .considerExifParams(false) // default
        .imageScaleType(ImageScaleType.IN_SAMPLE_POWER_OF_2) // default
        .bitmapConfig(Bitmap.Config.ARGB_8888) // default
        .decodingOptions(...)
        .displayer(new SimpleBitmapDisplayer()) // default
        .handler(new Handler()) // default
        .build();
```

####  displayImage方法加载

 1. displayImages方法不需要添加监听，尽量使用该方法来加载

``` java
    public void displayImageByDisplayImageOption() {
        final ImageView mImageView = (ImageView) findViewById(R.id.image);
        String imageUrl = "https://www.baidu.com/img/bd_logo1.png";
        
        //显示图片的配置
        DisplayImageOptions options = new DisplayImageOptions.Builder()
                .showImageOnLoading(R.drawable.ic_stub)
                .showImageOnFail(R.drawable.ic_error)
                .cacheInMemory(true)
                .cacheOnDisk(true)
                .bitmapConfig(Bitmap.Config.RGB_565)
                .build();

        ImageLoader.getInstance().displayImage(imageUrl, mImageView, options);
    }

```

 2. 加载时候显示进度条
 
``` java
    public void displayImageByDisplayImageOption() {
        final ImageView mImageView = (ImageView) findViewById(R.id.image);
        String imageUrl = "https://www.baidu.com/img/bd_logo1.png";

        //显示图片的配置
        DisplayImageOptions options = new DisplayImageOptions.Builder()
                .cacheInMemory(true)
                .cacheOnDisk(true)
                .bitmapConfig(Bitmap.Config.RGB_565)
                .build();

        ImageLoader.getInstance().displayImage(imageUrl, mImageView, options, new SimpleImageLoadingListener(), new ImageLoadingProgressListener() {
            @Override
            public void onProgressUpdate(String imageUri, View view, int current,
                                         int total) {

            }
        });
    }
```

 ####  GirdView,ListView加载图片
 
1. 当ListView快速滑动或者猛地滑动的时候我们希望停止图片的加载，可以通过PauseOnScrollListener来处理，pauseOnScroll代表滑动过程中是否加载，pauseOnFling代表猛地滑动的时候是否加载

``` java
listView.setOnScrollListener(new PauseOnScrollListener(imageLoader, pauseOnScroll, pauseOnFling));  
```
2. GirdView用法类似，setOnScrollListener中传入PauseOnScrollListener即可

``` java
girdView.setOnScrollListener(new PauseOnScrollListener(imageLoader, pauseOnScroll, pauseOnFling));  
```


 


  [1]: https://github.com/nostra13/Android-Universal-Image-Loader