# 尝试使用spring aop在gorm的save方法

场景是想要做一个权限的缓存在内存里，所以在新建权限时想用aop在调用domain的save的同时做一个缓存的新建

但是我service层用的是gorm,类似
```
@Service(RoleResourceRelation)
abstract class RoleResourceRelationService {
  RoleResourceRelation save(RoleResourceRelation roleResourceRelation)
}
```
## Getting Started

我创建一个aop的类

```
@Aspect
//@Component
class RoleResourceInterceptor {

    @Before(value = 'execution(* com.sinosonar.rbac.relation.RoleUserRelationService.save(..))')
    void test(JoinPoint joinPoint) {
        println "tttttttttttttttttt"
    }
}
```
简单的声明RoleUserRelationService.save()并没有触发对应的函数

查资料后，看到因为RoleResourceRelationService是抽象类，所以实际上是它的子类调用了save方法，所以将声明改成

```
    @Before('execution(* com.sinosonar.rbac.relation.RoleUserRelationService+.save(..))')
```

修改后依旧没有生效

在代码里打断点，观察到RoleResourceRelationService这个注入，实际上的bean实现类名是$RoleResourceRelationServiceImplementation

所以我进一步修改表达式

```
    @Before('execution(* com.sinosonar.rbac.relation.*RoleUserRelationService+.save(..))')
```
当然，依旧无法生效

这时候我继续观察，尝试寻找真正触发的save函数，最后单步到GormEntity的save方式。

gorm应该有生成一个接口实现的中间代理类吧，在代理的invoke在调用这边的方法吧，save方法在GormEntity只不过是一个接口用动态代理实现。

对动态代理用aop,就要做一个AspectJProxyFactory 的实现了，挺复杂的。


