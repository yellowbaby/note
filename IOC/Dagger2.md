### 概述
　[Dagger2][1]是谷歌公司fork了Dagger1后接受开发后的产品，是Dagger1的升级版，现在由谷歌负责维护，总的来说Dagger2是一个依赖注入的框架，作用和Spring类似，只不过Spring用到了反射来处理，但是在Android中反射会影响性能，Dagger2在编译的时候就已经做好了处理
 
 ### 配置方法

> build.gradle

``` gradle
buildscript {

    ....

    dependencies {

        classpath 'com.android.tools.build:gradle:2.1.0'
        // 添加android-apt 插件
        classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
    }
}
```

> app/build.gradle

``` stylus
apply plugin: 'com.neenbedankt.android-apt'

dependencies {
    ...
    // dagger 2 的配置
    compile 'com.google.dagger:dagger:2.4'
    apt 'com.google.dagger:dagger-compiler:2.4'
    compile 'org.glassfish:javax.annotation:10.0-b28'// 添加java 注解库
}
```

### 常用注解

 - @Inject：告诉Dagger该属性需要注入
 - @Module：定义在类上，使用该注解告诉Dagger去哪里找需要的依赖
 - @Provides：定义在module类的方法上，告诉Dagger我们想构造对象并提供这些依赖
 - @Component：@Inject和@Module的桥梁，作用是连接这两个部分，一个Component可以对应多个Module
 - @Scope：自定义注解限定注解作用域

### 一个集合MVP使用的例子

 1. view层
 

> IView.java

``` java
public interface IView {
    /** 
     * 更新UI 
     * @param data 
     */  
    void updateUi(String data);  
}  
```


 2. presenter层
 
> IPresenter.java

``` java
public interface IPresenter {
    /** 
     * 加载数据 
     */  
    void loadData();  
} 
```


 3. 1

  [1]: https://github.com/google/dagger