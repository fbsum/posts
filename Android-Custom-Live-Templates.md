# Custom Live Template

## Java

### classthis

快速生成 XxxClass.this，尤其是在匿名类中获取主类的 this 属性。

| Key           | Value 					   |
|:--------------|:-------------------------|
| Abbreviation  | classthis                |
| Description   | Generate Class.this      |
| Template Text | $CLASS_NAME$.this        |
| Applicable    | java: expression         |
| Variables     | CLASS_NAME = className() |

### classtag

快速生成主类的 TAG 属性。

| Key           | Value 					   |
|:--------------|:-------------------------|
| Abbreviation  | classtag                 |
| Description   | Generate class tag field |
| Template Text | private static final String TAG = $CLASS_NAME$.class.getSimpleName(); |
| Applicable    | java: declaration        |
| Variables     | CLASS_NAME = className() |

### xloge
| Key           | Value 					   |
|:--------------|:-------------------------|
| Abbreviation  | xloge                    |
| Description   | XLog.e("")               |
| Template Text | XLog.e("$text$");        |
| Applicable    | java: statement          |
| Variables     | text                     |

### xlogemethod

快速打印当前方法名。

| Key           | Value 					                  |
|:--------------|:----------------------------------------|
| Abbreviation  | xlogemethod                             |
| Description   | XLog.e("method")                        |
| Template Text | XLog.e(" ====== $METHOD_NAME$ ======"); |
| Applicable    | java: statement                         |
| Variables     | METHOD_NAME = methodName()              |

### xlogeparam

快速打印当前方法的参数。

| Key           | Value 					              |
|:--------------|:------------------------------------|
| Abbreviation  | xlogeparam                          |
| Description   | XLog.e("param0 = " + param0 + ...); |
| Template Text | XLog.e($content$);                  |
| Applicable    | java: statement                     |
| Variables     | content = [xloge-param expression](#xloge-param-expression) |

###### <a name="xloge-param-expression"></a>xloge-param expression

```
groovyScript("def result=''; def params=\"${_1}\".replaceAll('[\\\\[|\\\\]|\\\\s]', '').split(',').toList(); for(i = 0; i < params.size(); i++) {result += ((i == 0) ? '\"' : '\",') + params[i] + ' = \" + ' + params[i] + ((i < params.size() - 1) ? ' + ' : '');}; return result", methodParameters())
```