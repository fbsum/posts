# Android 应用内切换语言

在所有 Activity.onCreate() 里适配 >= Android N 的语言切换，无需重启 Activity

```
        registerActivityLifecycleCallbacks(object : ActivityLifecycleAdapter() {
            override fun onActivityCreated(activity: Activity, savedInstanceState: Bundle?) {
                // fix activity locale(>= Android N)
                if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
                    val savedLocale = AppLanguageUtils.getAppLocale()
                    if (!TextUtils.equals(activity.resources.configuration.locale.language, savedLocale.language)) {
                        LanguageUtils.changeAppLocale(activity, savedLocale)
                    }
                }
            }
        })
```

切换语言

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

重启 Activity，在高版本手机无法使用 Activity.recreate()

```
        val intent = Intent(this, MainActivity::class.java)
        intent.flags = Intent.FLAG_ACTIVITY_NEW_TASK or Intent.FLAG_ACTIVITY_CLEAR_TASK
        startActivity(intent)
        overridePendingTransition(0, 0)
```