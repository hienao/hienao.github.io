---
title: 使用 URL Scheme 或android intent唤起应用打开指定的 Activity
date: 2018-08-24 14:17:09
categories:
  - Android
tags:
  - Android
---
Android 开发过程中经常会有 H5 页面和 Native 的交互，常见的如在网页中打开淘宝app到商品界面，还有在组件化工程中模块间通过路由调用有时候也是使用这种方案。

通过 URL 协议打开页面的应用场景大致有以下几种：
> * 从 H5 页面打开 APP 的某个页面
> * 从其他应用打开 APP 的某个页面
> * 从组件化工程中的一个组件调用另一个组件
> * 点击通知栏消息推送，跳转至某个页面

## Android URL Scheme 格式
```
Scheme://Host:Port/Path?Query=Value
```
获取Scheme Uri方式如下：
``` java
Uri uri = getIntent().getData();
```

| 参数名称 | 描述 | 可省略 |获取|
| --- | --- | --- |---|
| Scheme | 协议  | 否 |uri.getScheme()|
| Host | 地址 | 是 |uri.getHost()|
| Port | 端口 | 是 |uri.getPort()|
| Path | 页面 | 是 |uri.getPath()|
| Query | 参数 | 是 |uri.getQuery()或者uri.getQueryParameterNames()|

## 清单文件

``` xml
<activity android:name=".GoodActivity">
    <intent-filter>
        <data
            android:host="taobao"
            android:path="/router"
            android:port="8888"
            android:scheme="tb" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <action android:name="android.intent.action.VIEW" />
    </intent-filter>
</activity>
```

## 网页调用
-  Chrome for Android <= 18的情况下


``` html
<iframe src="tb://taobao:8888/router?token=fgdsfgsd"> </iframe>
```


- 在 Chrome for Android >= 25 的环境下，可以使用 Chrome Intent 来调起 Android App
    格式说明如下：

```
intent:HOST/URI-path // Optional host
#Intent;
package=[string];
action=[string];
category=[string];
component=[string]; 
scheme=[string];
end;
```
例如mainfest 中activity定义如下：

``` xml
<activity
            android:name=".business.headline.ui.HeadlineWebviewActivity"
            android:screenOrientation="portrait"
            android:windowSoftInputMode="adjustResize">
            <intent-filter>
                <action android:name="android.intent.action.VIEW"/>

                <category android:name="android.intent.category.DEFAULT"/>
                <category android:name="android.intent.category.BROWSABLE"/>
                <data
                    android:host="headline"
                    android:path="/webview"
                    android:scheme="ddd"/>
            </intent-filter>
        </activity>
```
> 注：只有添加了` <category android:name="android.intent.category.BROWSABLE"/>`的activity才可以被intent唤起

然后在浏览器中点击a标签，就可以启动应用程序的对应activity了，如果手机中没有相应的应用，那么是否会跳转到错误页面呢，将a标签设置为

``` html
<a href="intent://headline/webview#Intent;scheme=ddd;package=com.qima.kdt;S.url=https://www.baidu.com;end">Do Whatever</a>
```
这样如果没有对应应用，该链接就会跳转到S.browser_fallback_url指定的url上。
如果我们还需要对在a标签中对指定activity进行传值，可以将a标签设置为：

``` html
<a href="intent://headline/webview#Intent;scheme=ddd;package=com.qima.kdt;S.url=https://www.baidu.com;S.img=http://img.baidu.com/xxx.png;S.title=我是标题的;I.id=207;end">Do Whatever</a>
```
其中参数类型说明：

```
String => 'S'
Boolean =>'B'
Byte => 'b'
Character => 'c'
Double => 'd'
Float => 'f'
Integer => 'i'
Long => 'l'
Short => 's'
```
解析参数的方案：

``` java
 Bundle parametros=intent.getExtras();
            if (parametros!=null){
                url = Uri.decode(parametros.getString("url"));
                mContentId = parametros.getLong("id");
                mContentTitle = parametros.getString("title");
                mContentImgUrl = parametros.getString("img");
            }
```
## 原生调用

``` java
Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse("tb://taobao:8888/router?token=fgdsfgsd"));
startActivity(intent);
```
## 判断一个 Scheme 是否有效

``` java
PackageManager packageManager = getPackageManager();
Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse("tb://taobao:8888/router?token=fgdsfgsd"));
List<ResolveInfo> activities = packageManager.queryIntentActivities(intent, 0);
boolean isValid = !activities.isEmpty();
if (isValid) {
    startActivity(intent);
}
```

> 需要注意的是，很多第三方浏览器会拦截掉chrome intent启动应用的请求，像uc，微信内置浏览器，QQ浏览器等，在这些页面要做一个提示，让用户跳转到源生的浏览器上才能打开应用


