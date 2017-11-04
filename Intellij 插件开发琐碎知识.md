# Intellij Plugin 开发 —— 琐碎知识点

### 1. 判断是否显示插件
实现：覆写 update 方法，做相应的判断，并设置 event.presentation.isVisible 的值。

例子：根据当前文件来判断是否显示插件（代码如下），其中 isValidFile() 为自定义验证方法。

```kotlin
    override fun update(event: AnActionEvent) {
        super.update(event)
        val virtualFile = event.getData(LangDataKeys.VIRTUAL_FILE) ?: return
        event.presentation.isVisible = isValidFile(virtualFile)
    }
```

### 2. 格式化代码

```kotlin
	CodeStyleManager.getInstance(project).reformat(psiClass)

```

### 3. 在编辑器中打开

```kotlin
	FileEditorManager.getInstance(project)
		.openTextEditor(OpenFileDescriptor(project, virtualFile), true)
```