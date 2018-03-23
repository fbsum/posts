# Android 应用内切换语言

在所有 Activity.onCreate() 里更新语言切换的资源，无需重启 Activity

```
		// In Application
        registerActivityLifecycleCallbacks(object : ActivityLifecycleAdapter() {
            override fun onActivityCreated(activity: Activity, savedInstanceState: Bundle?) {
                val savedLocale = AppLanguageUtils.getAppLocale()
                if (!TextUtils.equals(activity.resources.configuration.locale.language, savedLocale.language)) {
                    LanguageUtils.updateLocale(activity, savedLocale)
                }
            }
        })
```

切换语言

```
    /**
     * 更换应用语言，重启 Activity
     * PS：高版本手机使用 Activity.recreate() 会导致崩溃
     */
    private fun updateAppLocale(context: Context, locale: Locale) {
        AppLanguageUtils.setAppLocale(locale)

        val intent = Intent(this, MainActivity::class.java)
        intent.flags = Intent.FLAG_ACTIVITY_NEW_TASK or Intent.FLAG_ACTIVITY_CLEAR_TASK
        startActivity(intent)
        overridePendingTransition(0, 0)
    }
```

LanguageUtils

```
    public static void updateLocale(Context context, Locale locale) {
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

AppLanguageUtils（按 APP 具体需求修改）

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