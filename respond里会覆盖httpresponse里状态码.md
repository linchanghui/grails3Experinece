# respond domain.errors, [view:'create'] 里会覆盖httpresponse里状态码

## 描述
+ 因为我用了rest api的controller模板。在contoller里大量使用到了以下代码
```java
   try {
            roleResourceRelationService.save(roleResourceRelation)
        } catch (ValidationException e) {
            respond roleResourceRelation.errors, [view:'create',status: BAD_REQUEST] //todo DefaultJsonRenderer.groovy:89  context.setStatus(errorsHttpStatus) will cover status parameter
            return
        }
```
+ 这里的respond的虽然支持status的参数，但是其实这个无效。会在DefaultJsonRenderer.groovy:89,被设置为	UNPROCESSABLE_ENTITY(422, "Unprocessable Entity")状态

```java
    context.setStatus(errorsHttpStatus)
    HttpStatus errorsHttpStatus = HttpStatus.UNPROCESSABLE_ENTITY
    UNPROCESSABLE_ENTITY(422, "Unprocessable Entity"),

```
