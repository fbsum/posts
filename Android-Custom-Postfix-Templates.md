# Custom Postfix Templates

使用插件 [Custom Postfix Templates](https://github.com/xylo/intellij-postfix-templates)

```
.xloge : log error
    NON_VOID                 →  XLog.e("$expr$ = " + $expr$);
    
.visible : set view visible
    android.view.View        →  $expr$.setVisibility(View.VISIBLE);

.invisble : set view invisble
    android.view.View        →  $expr$.setVisibility(View.INVISIBLE);

.gone : set view gone
    android.view.View        →  $expr$.setVisibility(View.GONE);
    
.isemp : check empty charSequence
	java.lang.CharSequence   →  TextUtils.isEmpty($expr$)
```
