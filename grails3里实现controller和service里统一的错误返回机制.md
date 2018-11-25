# grails3里实现controller和service里统一的错误返回机制

## 期望的效果
+ 统一的方式返回错误信息
+ 返回对应的http的状态码
+ 对i18n友好
+ 最好能同时记录到日志文件（不是必须）

## 方案
+ 做业务判断后，不满足的抛出异常，然后通过UrlMappings去重定向内部异常。
+ throw new Exception 后状态为500。
```java
        if (tds.size() != ths.size()) {
            throw new Exception("head和value个数不匹配")
        }

```
+ 500的状态重定向到handleError的action
```aidl
   "500"(controller: "error", action: "handleError")
```
```asciidoc
 def handleError() {
        Exception exception = request.exception
        response.status = 400
        render ([
                result:false,
                errorMessag:ExceptionUtils.getRootCauseMessage(exception)
        ] as JSON)
        return
    }

```
+ 以上代码利用response.status去控制错误码，再通过ExceptionUtils.getRootCauseMessage去获取异常的message。并且这样的异常，通过log4j配置都会记录下错误的信息和错误堆栈。

## 展望
+ 通过异常怎么传错误码，和i18n的code，最蠢的做法就是在message里，然后去截取。有没有更优雅的做法
+ 整体实现上有没有其他的方案，可以借鉴学习下