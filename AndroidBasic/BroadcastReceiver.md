* [广播的概念](#广播的概念)
* [接受常用的系统广播](#接受常用的系统广播)
	* [SD卡状态监听](#sd卡状态监听)
	* [短信拦截器](#短信拦截器)
* [自定义无序广播](#自定义无序广播)
	* [动态注册无序广播](#动态注册无序广播)
	* [静态注册无序广播](#静态注册无序广播)
* [自定义有序广播](#自定义有序广播)
	* [动态注册有序广播](#动态注册有序广播)
	* [静态注册有序广播](#静态注册有序广播)
* [动态注册和静态注册的区别](#动态注册和静态注册的区别)
* [本地广播](#本地广播)

### 广播的概念

　　系统很多消息需要通过广播去通知给每一个应用程序，比如电量不足，网络状态的变化，SD卡被拔出插入，有新的apk被安装等等，如果某个应用程序关心某个广播，就需要注册一个广播接收器（BroadcastReceiver）
  
### 接受常用的系统广播

#### SD卡状态监听

 1. 创建广播接收器
 
``` java
public class SDCardReceiver extends BroadcastReceiver {

	@Override
	public void onReceive(Context context, Intent intent) {
		String action = intent.getAction();
		if (action.equals(Intent.ACTION_MEDIA_MOUNTED)) {
			//安装处理			
		}else if (action.equals(Intent.ACTION_MEDIA_UNMOUNTED)) {
			//卸载处理
		}
	}

}
```

 2. 在AndroidManifest.xml中配置该广播接收器
 
``` java
	<receiver android:name="com.yellow.SDCardReceiver">
		<intent-filter>
			<action android:name="android.intent.action.MEDIA_MOUNTED" />
			<action android:name="android.intent.action.MEDIA_UNMOUNTED" />
			<!-- 广播类型 -->
			<data android:scheme="file" />
		</intent-filter>
	</receiver>			
```



#### 短信拦截器

 1. 创建广播接收器

``` java
public class SmsReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        Bundle bundle = intent.getExtras();
        Object[] pdus = (Object[]) bundle.get("pdus");
        for (Object pdu : pdus) {
            SmsMessage sms = SmsMessage.createFromPdu((byte[]) pdu);
            String address = sms.getDisplayOriginatingAddress();//发信人
            String smsBody = sms.getMessageBody();//短信内容
            if (smsBody.equals("nihao!")) {
                abortBroadcast();//拦截短信
            }
        }
    }
}
```

 2. 在AndroidManifest.xml中配置该广播接收器
 
``` xml
        <receiver android:name="com.yellow.SmsReceiver">
            <!-- 设置优先级 -->
            <intent-filter android:priority="1000">
                <action android:name="android.provider.Telephony.SMS_RECEIVED" />
            </intent-filter>
        </receiver>
```

 3. 放开去读短信的权限

``` xml
<uses-permission android:name="android.permission.RECEIVE_SMS"/>
```


### 自定义无序广播
#### 动态注册无序广播

``` java
public class MainActivity extends AppCompatActivity {

    private DynamicReceiver dynamicReceiver;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        //创建拦截器
        IntentFilter filter = new IntentFilter();
        filter.addAction("DynamicReceiver");
        dynamicReceiver = new DynamicReceiver();

        //注册接收器
        registerReceiver(dynamicReceiver, filter);
    }

    //发送名字为DynamicReceiver的无序广播，系统会根据配置的IntentFilter找接收器
    public void sendDynamic(View view) {
        Intent intent = new Intent();
        intent.setAction("DynamicReceiver");
        intent.putExtra("name", "黄贝");
        sendBroadcast(intent);
    }

    //注销接收器
    @Override
    protected void onPause() {
        super.onPause();
        unregisterReceiver(dynamicReceiver);
    }

    //自定义动态广播接收器
    class DynamicReceiver extends BroadcastReceiver {
        @Override
        public void onReceive(Context context, Intent intent) {
            Toast.makeText(context, "动态广播：" + intent.getStringExtra("name"), Toast.LENGTH_SHORT).show();
        }
    }
}
```

#### 静态注册无序广播

 1. 创建接收器
 
``` java
//自定义动态广播接收器
public class StaticReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        Toast.makeText(context, "动态广播：" + intent.getStringExtra("name"), Toast.LENGTH_SHORT).show();
    }

}
```

 2. AndroidManifest.xml中静态注册

``` xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="app.yellow.broadcastreceiver">

    <application>
       ...

        <receiver android:name=".StaticReceiver">
            <intent-filter>
                <action android:name="StaticReceiver"></action>
            </intent-filter>
        </receiver>
    </application>

</manifest>
```


 3. sendBroadcast发送广播
 

``` java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

    public void sendStatic(View view) {
        Intent intent = new Intent();
        intent.setAction("StaticReceiver");
        intent.putExtra("name", "黄贝");
        sendBroadcast(intent);
    }
}
```


### 自定义有序广播
#### 动态注册有序广播

``` java
public class MyReceiver01 extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        Toast.makeText(context, "动态广播：" + intent.getStringExtra("name"), Toast.LENGTH_SHORT).show();
    }

}
```

``` java
public class MyReceiver02 extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        Toast.makeText(context, "动态广播：" + intent.getStringExtra("name"), Toast.LENGTH_SHORT).show();
        abortBroadcast(); //中断广播，不会再响比它有优先级低得广播再传播下去了
    }
}
```

``` java
public class MainActivity extends AppCompatActivity {

    private MyReceiver01 myReceiver01;
    private MyReceiver02 myReceiver02;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        //创建拦截器
        IntentFilter filter1 = new IntentFilter();
        filter1.addAction("MyReceiver");
        filter1.setPriority(5);

        IntentFilter filter2 = new IntentFilter();
        filter2.addAction("MyReceiver");
        filter2.setPriority(4);

        myReceiver01 = new MyReceiver01();
        myReceiver02 = new MyReceiver02();

        registerReceiver(myReceiver01, filter1);
        registerReceiver(myReceiver02, filter2);

    }

    public void sendDynamic(View view) {
        Intent intent = new Intent();
        intent.setAction("MyReceiver");
        intent.putExtra("name", "黄贝");
        sendOrderedBroadcast(intent,null);
    }

    @Override
    protected void onPause() {
        super.onPause();
        unregisterReceiver(myReceiver01);
        unregisterReceiver(myReceiver02);
    }
}
```

#### 静态注册有序广播

 1. 创建两个广播接收器

``` java
public class MyReceiver01 extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        Toast.makeText(context, "动态广播：" + intent.getStringExtra("name"), Toast.LENGTH_SHORT).show();
    }
}
```

``` java
public class MyReceiver02 extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        Toast.makeText(context, "动态广播：" + intent.getStringExtra("name"), Toast.LENGTH_SHORT).show();
        abortBroadcast(); //中断广播，不会再响比它有优先级低得广播再传播下去了
    }
}
```

 2. xml中注册，设置优先级，优先级数字越大优先级越高
 
``` xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="app.yellow.broadcastreceiver">

    <application>
       ...
        <receiver android:name=".MyReceiver01">
            <intent-filter android:priority="5">
                <action android:name="MyReceiver"></action>
            </intent-filter>
        </receiver>
        <receiver android:name=".MyReceiver02">
            <intent-filter android:priority="4">
                <action android:name="MyReceiver"></action>
            </intent-filter>
        </receiver>
    </application>

</manifest>

```


 3. 使用sendOrderedBroadcast发送
 
``` java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

    public void sendStatic(View view) {
        Intent intent = new Intent();
        intent.setAction("MyReceiver");
        intent.putExtra("name", "黄贝");
        sendOrderedBroadcast(intent);
    }
}
```


### 动态注册和静态注册的区别

　　动态注册广播不是常驻型广播，也就是会随着Activity的生命周期被注销移除，静态注册是常驻型，也就是说当应用程序关闭后，有信息广播来，程序也会被自动运行

### 本地广播

 1. 本地广播是只在程序内部进行传递的广播，发送和接收都只在本程序有效
 2. 不用担心数据泄密，因为广播不会被其他程序获得
 3. 其他广播无法发送到我们程序内部，所以不需要担心有安全漏洞
 4. 发送本地广播将更加高效，只能动态注册

``` java
public class MainActivity extends AppCompatActivity {

    private IntentFilter intentFilter;
    private LocalReceiver localReceiver;
    private LocalBroadcastManager localBroadcastManager;//本地广播管理器

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        //创建本地广播接受者并且注册
        localBroadcastManager = LocalBroadcastManager.getInstance(this);
        intentFilter = new IntentFilter();
        intentFilter.addAction("MyReceiver");
        localReceiver = new LocalReceiver();
        localBroadcastManager.registerReceiver(localReceiver, intentFilter);


    }

    public void sendDynamic(View view) {
        Intent intent = new Intent();
        intent.setAction("MyReceiver");
        intent.putExtra("name", "黄贝");
        localBroadcastManager.sendBroadcast(intent);//使用管理器发送广播
    }

    @Override
    protected void onPause() {
        super.onPause();
        localBroadcastManager.unregisterReceiver(localReceiver);//注销广播管理器
    }

    class LocalReceiver extends BroadcastReceiver {
        @Override
        public void onReceive(Context context, Intent intent) {
            Toast.makeText(context, "这是本地广播接收器", Toast.LENGTH_SHORT).show();
        }
    }
}

```
