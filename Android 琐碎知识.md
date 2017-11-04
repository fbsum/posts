# Android 琐碎知识

### 未整理
```
 /**
     * 修复 Android 11~17 中，{@link android.graphics.Canvas#clipPath(Path)} 引起的
     * {@link UnsupportedOperationException} 问题。
     * <p/>
     * 解决办法原文：<a href="http://stackoverflow.com/a/30354461">http://stackoverflow.com/a/30354461</a>
     */
    public static void fixClipPathUnsupportedOperationException(View view)
    {
        if (Build.VERSION.SDK_INT < Build.VERSION_CODES.JELLY_BEAN_MR2
            && Build.VERSION.SDK_INT >= Build.VERSION_CODES.HONEYCOMB)
        {
            view.setLayerType(View.LAYER_TYPE_SOFTWARE, null);
        }
    }
```

```
    <style name="ColorAccentRaisedButton">
        <item name="colorButtonNormal">@color/colorAccent</item>
    </style>

    <style name="ColorAccentFlatButton" parent="Theme.AppCompat.Light">
        <item name="colorControlHighlight">@color/colorAccent</item>
    </style>
```

### 依赖 aar 包
```groovy
    compile(name:'file-name',ext:'aar')
```

### ImageView 重复平铺小图
使用 Bitmap Shader

### TextView 添加下划线／中划线
```java
	// 加下划线
	textView.getPaint().setFlags(Paint.UNDERLINE_TEXT_FLAG);

	// 加中划线（可加抗锯齿）
	if (isAntiAlias) {
        textView.getPaint().setFlags(Paint.STRIKE_THRU_TEXT_FLAG | Paint.ANTI_ALIAS_FLAG);
    } else {
        textView.getPaint().setFlags(Paint.STRIKE_THRU_TEXT_FLAG);
    }
    
    // 清除所有划线
    textView.getPaint().setFlags(0);
        
```

### 严格模式
```java
        StrictMode.setThreadPolicy(new StrictMode.ThreadPolicy.Builder()
                .detectAll()
                .penaltyLog()
                .build());
        StrictMode.setVmPolicy(new StrictMode.VmPolicy.Builder()
                .detectAll()
                .penaltyLog()
                .penaltyDeath()
                .build());
```
* penaltyDeath()：检查整个过程。
* penaltyDeathOnNetwork()：检查在任何网络使用情况下崩溃过程。
* penaltyDialog()：检测到的错误行为时向开发者显示一个讨厌的对话框。
* penaltyFlashScreen()：检查到问题时闪烁屏幕。
* penaltyLog()：将检测到的问题情况记录到系统日志中。



### 问题：toolbar 设置 app:elevation 没有效果
解决：搭配 AppBarLayout 使用

### 问题：去除 toolbar 自带的 padding
解决：设置 contentInset 属性

```xml
app:contentInsetEnd="0dp"
app:contentInsetLeft="0dp"
app:contentInsetRight="0dp"
app:contentInsetStart="0dp"
```