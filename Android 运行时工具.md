# Android 运行时工具

### [Hugo](https://github.com/JakeWharton/hugo)

快速打印方法调用

Simply add @DebugLog to your methods and you will automatically get all of the things listed above logged for free.

```java
@DebugLog
public String getName(String first, String last) {
  SystemClock.sleep(15); // Don't ever really do this!
  return first + " " + last;
}
```
```java
V/Example: ⇢ getName(first="Jake", last="Wharton")
V/Example: ⇠ getName [16ms] = "Jake Wharton"
```
