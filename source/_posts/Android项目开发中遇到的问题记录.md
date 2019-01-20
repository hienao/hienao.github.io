---
title: Android项目开发中遇到的问题记录
date: 2018-07-10 10:51:09
categories:
  - Android
tags:
  - Android
---

1. Android 6.0 解决recyclerview 在 scrollview 中不能全部显示，高度不正常的问题。
  - 问题描述：在Android 6.0上如果recyclerview被scrollerview包裹且设置高度为wrap_content，在recyclerviewitem足够多的情况下会只显示一部分item。
    
  - 解决方案：最终 stackoverflow 找到了解决办法：
    [](http://)http://stackoverflow.com/questions/27083091/recyclerview-inside-scrollview-is-not-working
  - 解决办法：在 recyclerview 外面再包一层 RelativeLayout
```xml
<RelativeLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:descendantFocusability="blocksDescendants">
            <!-- DEV NOTE: Outer wrapper relative layout is added intentionally to address issue
                 that only happens on Marshmallow & Nougat devices (API 23 & 24).
                 On marshmallow API 23, the "RecyclerView" `layout_height="wrap_content"` does NOT
                 occupy the height of all the elements added to it via adapter. The result is cut out
                 items that is outside of device viewport when it loads initially.
                 Wrapping "RecyclerView" with "RelativeLayout" fixes the issue on Marshmallow devices.
            -->
            <android.support.v7.widget.RecyclerView
                android:id="@+id/my_recycler_view"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                tools:listitem="@layout/row_list_item">

            </android.support.v7.widget.RecyclerView>

        </RelativeLayout>
```
2. 当使用的图片控件为CircleImagview的时候，Glide设置placeholder会出现第一次加载图片失败，当你再次刷新的时候，才能展现出图片

  ```java
     Glide.with(this).load(imgurl)
                  .placeholder(R.drawable.default_photo)
                  .diskCacheStrategy(DiskCacheStrategy.ALL)
                  .into(new SimpleTarget<GlideDrawable>() {
                      @Override
                      public void onResourceReady(GlideDrawable resource, GlideAnimation<? super GlideDrawable> glideAnimation) {
                          mCivUserPhoto.setImageDrawable(resource);
                      }
                  });
  ```
3. 关于Fragment（XXFragment） not attached to Activity 异常
  出现该异常，是因为Fragment的还没有Attach到Activity时，调用了如getResource()等，需要上下文Content的函数。实际情况中大多数是因为网络还未返回数据时，activity由于各种原因销毁重建导致该问题出现。
  解决方法：
 - 将调用的代码写在OnStart（）中
 - 在调用context处，如`getResources().getString(R.string.app_name);`
    之前增加一个判断isAdded()或者 isVisible()
4. 使用viewpager时报错
> FragmentManager is already executing transactions
> 报错在`viewPager.setOffscreenPageLimit(5);`

    解决方案：将原有的

``` java
FragmentStatePagerAdapter adapter =
        new FragmentStatePagerAdapter(getActivity().getSupportFragmentManager(), fragments, titleList);
```
替换为：

``` java
FragmentStatePagerAdapter adapter = new FragmentStatePagerAdapter(getChildFragmentManager(),fragments,titleList);
```
