### 概述
　[Retrofit][1]　是现在最流行的网络请求框架，同样出自Square公司，对okhttp做了一层封装。把网络请求都交给给了Okhttp，我们只需要通过简单的配置就能使用retrofit来进行网络请求了，其主要作者是Android大神JakeWharton

### 配置方法

``` gradle
dependencies {
    ...
    compile 'com.squareup.retrofit2:retrofit:2.2.0'
}
```

### 使用方法

> 基本使用步骤

 1. 创建Retrofit对象
 2. 创建代理类
 2. 创建访问请求
 3. 发送请求
 4. 处理结果

> Get请求接口 
 
``` java
public interface GitHubApi {
    // https://api.github.com/repos/square/retrofit/contributors
    @GET("repos/{owner}/{repo}/contributors")
    Call<ResponseBody> contributorsBySimpleGetCall(@Path("owner") String owner, @Path("repo") String repo);
}

```

> Post请求接口 
 
``` java
public interface GitHubApi {
    // https://api.github.com/repos/square/retrofit/contributors
    @POST("repos/{owner}/{repo}/contributors")
    Call<ResponseBody> contributorsBySimpleGetCall(@Path("owner") String owner, @Path("repo") String repo);
}

```

> 发送请求

``` java
//1. 创建Retrofit对象
Retrofit retrofit = new Retrofit.Builder().baseUrl("https://api.github.com/").build();

//2. 创建代理类
GitHubApi repo = retrofit.create(GitHubApi.class);

//3. 创建访问请求
Call<ResponseBody> call = repo.contributorsBySimpleGetCall("square","retrofit");

//4. 发送请求
call.enqueue(new Callback<ResponseBody>() {
	@Override
	public void onResponse(Call<ResponseBody> call, Response<ResponseBody> response) {
	   //5. 处理结果
	}

	@Override
	public void onFailure(Call<ResponseBody> call, Throwable t) {

	}
});
```



  [1]: https://github.com/square/retrofit