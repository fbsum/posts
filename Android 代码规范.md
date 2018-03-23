# Android 代码规范（Java 版）

* 使用 TextUtils.equals(str1,str2) 替换 str1.equals(str2)

    说明：防止 str1 为空而抛出异常
    
* 需要与数据库表交互的 Model，所有字段都要写注释

* EventBus 代码集中放到类文件偏底部的地方

* 写 xml 需要用 tools 将具体预览数据显示出来 