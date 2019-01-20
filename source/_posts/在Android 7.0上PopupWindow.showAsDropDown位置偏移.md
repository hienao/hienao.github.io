---
title: 在Android 7.0上PopupWindow.showAsDropDown位置偏移
date: 2018-09-09 13:33:07
categories:
  - Android
tags:
  - Android
---
解决方案：
用
``` java
 if (Build.VERSION.SDK_INT < 24) {
                        popupWindow.showAsDropDown(view);
                    } else {
                        int[] location = new int[2];
                        view.getLocationOnScreen(location);
                        int x = location[0];
                        int y = location[1];
                        popupWindow.showAtLocation(view, Gravity.NO_GRAVITY, 0, y + view.getHeight());
                    }

```
替换：

``` java
popupWindow.showAsDropDown(view);
```

在部分机型上上述方法无效，可以尝试另一种方案：

``` java
if (Build.VERSION.SDK_INT > 24) {
                Rect rect = new Rect();
                mView.getGlobalVisibleRect(rect);
                int h = mView.getResources().getDisplayMetrics().heightPixels - rect.bottom;
                popWindow.setHeight(h);
            }
            categoryWindow.showAsDropDown(categoryView, 0, PhoneUtils.dp2px(getContext(), 0.5));
```