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

### AppbarLayout 与 NestedScrollView 嵌套 RecyclerView 使用时，触发 Fling 会导致 Header 布局在未滚动到顶部时，就出现
```
public class EnhancedNestedScrollView extends NestedScrollView {
    
    private boolean flingEnable = true;

    public EnhancedNestedScrollView(Context context) {
        super(context);
    }

    public EnhancedNestedScrollView(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    public EnhancedNestedScrollView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    @Override
    public void fling(int velocityY) {
        if (flingEnable) {
            super.fling(velocityY);
        }
    }

    public void setFlingEnable(boolean flingEnable) {
        this.flingEnable = flingEnable;
    }
}
```
```
        nestedScrollView.setFlingEnable(false);
        recyclerView.setNestedScrollingEnabled(false);
        appbarLayout.addOnOffsetChangedListener(new AppBarLayout.OnOffsetChangedListener() {
            @Override
            public void onOffsetChanged(AppBarLayout appBarLayout, int verticalOffset) {
                if (Math.abs(verticalOffset) - appBarLayout.getTotalScrollRange() == 0) {
                    //  Collapsed
                    nestedScrollView.setFlingEnable(true);
                } else {
                    //Expanded
                    nestedScrollView.setFlingEnable(false);
                }
            }
        });
```

### 360 加固后需要 zipalign 才能上传 google play
```
./zipalign -v 4 zipalign处理前的apk zipalign处理后的apk
```

### 监听虚拟按键
```
    class InnerReceiver extends BroadcastReceiver {

        final String SYSTEM_DIALOG_REASON_KEY = "reason";
        final String SYSTEM_DIALOG_REASON_RECENT_APPS = "recentapps";
        final String SYSTEM_DIALOG_REASON_HOME_KEY = "homekey";

        @Override
        public void onReceive(Context context, Intent intent) {
            String action = intent.getAction();
            if (Intent.ACTION_CLOSE_SYSTEM_DIALOGS.equals(action)) {
                String reason = intent.getStringExtra(SYSTEM_DIALOG_REASON_KEY);
                if (reason != null) {
                    if (reason.equals(SYSTEM_DIALOG_REASON_HOME_KEY)
                            || reason.equals(SYSTEM_DIALOG_REASON_RECENT_APPS)) {
                    }
                }
            }
        }
    }
```

### 悬浮窗权限 type 设置
```
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            layoutParams.type = WindowManager.LayoutParams.TYPE_ACCESSIBILITY_OVERLAY;
        } else if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N_MR1) {
            layoutParams.type = WindowManager.LayoutParams.TYPE_PHONE;
        } else if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
            layoutParams.type = WindowManager.LayoutParams.TYPE_TOAST;
        } else {
            layoutParams.type = WindowManager.LayoutParams.TYPE_PHONE;
        }
```

### 应用内切换语言

Application：

```
        registerActivityLifecycleCallbacks(object : ActivityLifecycleAdapter() {
            override fun onActivityCreated(activity: Activity, savedInstanceState: Bundle?) {
                val savedLocale = AppLanguageUtils.getAppLocale()
                if (!TextUtils.equals(activity.resources.configuration.locale.language, savedLocale.language)) {
                    LanguageUtils.changeAppLocale(activity, savedLocale)
                }
            }
        })
```

Activity：

```
    /**
     * 更换应用语言，重启 Activity
     */
    private fun updateAppLocale(context: Context, locale: Locale) {
        AppLanguageUtils.setAppLocale(locale)

        val intent = Intent(this, MainActivity::class.java)
        intent.flags = Intent.FLAG_ACTIVITY_NEW_TASK or Intent.FLAG_ACTIVITY_CLEAR_TASK
        intent.putExtra(ARG_MOTTO_INDEX, viewModel.getCurrentMottoIndex())
        startActivity(intent)
        overridePendingTransition(0, 0)
    }
```

LanguageUtils

```
    public static void changeAppLocale(Context context, Locale locale) {
        Resources resources = context.getResources();
        Configuration config = resources.getConfiguration();
        DisplayMetrics dm = resources.getDisplayMetrics();
        if (Build.VERSION.SDK_INT < Build.VERSION_CODES.JELLY_BEAN_MR1) {
            config.locale = locale;
        } else {
            config.setLocale(locale);
        }
        resources.updateConfiguration(config, dm);
    }
```

AppLanguageUtils

```
        /**
         * 获取当前语言配置
         */
        fun getAppLocale(): Locale {
            val defaultLocale = if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
                LocaleList.getDefault().get(0)
            } else {
                Locale.getDefault()
            }
            val defaultLanguage = if (TextUtils.equals(Locale.CHINESE.language, defaultLocale.language)) {
                Locale.CHINESE.language
            } else {
                Locale.ENGLISH.language
            }
            val language = PreferencesUtils.getString(PreferencesKey.LANGUAGE, defaultLanguage)
            return Locale(language)
        }

        /**
         * 保存语言配置
         */
        fun setAppLocale(locale: Locale) {
            val language = if (locale == Locale.CHINESE) {
                Locale.CHINESE.language
            } else {
                Locale.ENGLISH.language
            }
            PreferencesUtils.putString(PreferencesKey.LANGUAGE, language)
        }
```

### 代码混淆
```
#将可移动的类都移动到同一个包下
-repackageclasses 

#设置混淆字典
-obfuscationdictionary dictionary.txt
-classobfuscationdictionary dictionary.txt
-packageobfuscationdictionary dictionary.txt

```

### 修改打包时的 apk 名
```gradle
    applicationVariants.all { variant ->
        variant.outputs.all {
            if (outputFileName.endsWith(".apk")) {
                outputFileName = variant.applicationId + "_v" + variant.versionName + ".apk"
            }
        }
    }
```

### Button 样式修改
```
    <style name="ColorAccentRaisedButton">
        <item name="colorButtonNormal">@color/colorAccent</item>
    </style>

    <style name="ColorAccentFlatButton" parent="Theme.AppCompat.Light">
        <item name="colorControlHighlight">@color/colorAccent</item>
    </style>
```

### 问题：需要出现多条通知
解决：让 notification id 有变化

### 问题：多条通知，点击最先发布的通知不能执行 Intent
解决：通知的 PentingIntent 的 requestCode 需要有变化。


### 问题：同时在 xml 中设置 EditText 的 inputType="textMultiLine" 和 imeOptions="actionDone"，不能很好地生效

解决：在代码里设置

```java
edittext.setImeOptions(EditorInfo.IME_ACTION_NEXT);
edittext.setRawInputType(InputType.TYPE_CLASS_TEXT);
```

### 问题：在 RecyclerView 中， itemView 设置android:animateLayoutChanges="true" 没有效果
解决：adapter 在调用 notifyItem＊ 时，需要传入 payload 参数

### 依赖 aar 包
module 的 build.gradle 添加

```gradle
repositories {
    flatDir {
        dirs 'libs'
    }
}
```
```gradle
    implementation(name:'file-name',ext:'aar')
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