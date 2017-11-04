# Android 发布开源库到 JitPack

* 项目上传到 GitHub
* 根目录的 build.gradle 添加 

```gradle
classpath 'com.github.dcendents:android-maven-gradle-plugin:1.5'
```
* 在需要发布的 module 的 build.gradle 添加
 
```gradle
// JitPack Maven
apply plugin: 'com.github.dcendents.android-maven'
// Your Group
group='com.github.username' 
```

* 添加 gradle task，避免 JitPack 打包丢失源码

```groovy
// 打包源码jar
task sourcesJar(type: Jar) {
    from android.sourceSets.main.java.srcDirs
    classifier = 'sources'
}

task javadoc(type: Javadoc) {
    failOnError  false
    source = android.sourceSets.main.java.sourceFiles
    classpath += project.files(android.getBootClasspath().join(File.pathSeparator))
    classpath += configurations.compile
}

// 打包文档jar
task javadocJar(type: Jar, dependsOn: javadoc) {
    classifier = 'javadoc'
    from javadoc.destinationDir
}

artifacts {
    archives sourcesJar
    archives javadocJar
}
```

* 上传代码，并在 GitHub 上为项目创建 Release

### 参考：[Android 发布开源库到 JitPack、jCenter](http://www.jianshu.com/p/b7552cf8983b)