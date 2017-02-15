---
title: Android Toast 重复显示
date: 2017-02-15 15:22:35
categories: Android
tags: [Android基础,Android技巧]
---

测试提了一个 `bug` `Toast` 时间太长

> 当用户在某些情况下，用户连续误操作多次时，会导致出现很多个 Toast，依次显示，会在页面上停留很长时间，这个会严重影响软件的用户亲和性。我们可以通过一下方法来实现在一个Toast没有结束的时候再显示 Toast不累加时间，而是打断当前的Toast，显示新的Toast。这样Toast就不会停留在界面很久。而最多显示一个Toast提示时间的。

```java
import android.widget.Toast;
--------------------------------------------------------------------------------
    //使用的地方1
    showTextToast(getString(R.string.toast_irregular_number));
    
    //使用的地方2
    showTextToast(getString(R.string.toast_irregular_number2));
--------------------------------------------------------------------------------
    private Toast toast = null;
    
    private void showTextToast(String msg) {
        if (toast == null) {
            toast = Toast.makeText(getApplicationContext(), msg, Toast.LENGTH_SHORT);
        } else {
            toast.setText(msg);
        }
        toast.show();
    }
```

参考 [http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2013/0302/944.html](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2013/0302/944.html)

