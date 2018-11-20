## 经验目录



+ [尝试使用spring aop在gorm的save方法](https://github.com/linchanghui/grails3-/blob/master/%E5%B0%9D%E8%AF%95%E4%BD%BF%E7%94%A8spring%20aop%E5%9C%A8gorm%E7%9A%84save%E6%96%B9%E6%B3%95.md)
+ [自定service模板](https://github.com/linchanghui/grails3Experinece/blob/master/%E8%87%AA%E5%AE%9Aservice%E6%A8%A1%E6%9D%BF.md)
  - 自定义模板的位置
  - 使用spring data作为service的同时，兼顾service的可扩展性
+ 使用@Service标注的service里引用其他service bean，不能像以前一样简单的用def来声明，需要写出类名
+ [gorm中用JPA-QL Query的复杂案例](https://github.com/linchanghui/grails3Experinece/blob/master/gorm%E4%B8%AD%E7%94%A8JPA-QL%20Query%E7%9A%84%E5%A4%8D%E6%9D%82%E6%A1%88%E4%BE%8B.md )
+ [transients类型的domain属性，无法在controller的create或save时获取到(传进来)](https://github.com/linchanghui/grails3Experinece/blob/master/transients%E7%B1%BB%E5%9E%8B%E7%9A%84domain%E5%B1%9E%E6%80%A7%EF%BC%8C%E6%97%A0%E6%B3%95%E5%9C%A8controller%E7%9A%84create%E6%88%96save%E6%97%B6%E8%8E%B7%E5%8F%96%E5%88%B0.md)
+ transients类型的domain属性，无法在service.get时返回(获取时)
+ grails3的事件监听的三种机制
+ grails3里compile "org.grails.plugins:marshallers:0.5"这个插件不再存在，可以直接使用registerObjectMarshaller
+ grails3里实现controller和service里统一的错误返回机制
+ grails3里带环境变量的运行系统命令的方式
+ respond domain.errors, [view:'create'] 里会覆盖httpresponse里状态码
+ grails domain配置是否表继承的属性tablePerHierarchy
+ @Cacheable注解的用法
+ @GrailsTypeChecked(TypeCheckingMode.SKIP)避免静态代码检查
+ quartz里访问ont to many的外键关系如何解决LazyInitializationException的异常
+ grails url加lang参数，会自动设置语言的源码实现堆栈
+ grails3结合olingo odata2的实现

+ grails3一些莫名的报错

+ 未完待续
