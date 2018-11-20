# transients类型的domain属性，在什么阶段设置合适。

##初始预期
+ transients类型的domain属性，无法在service的get函数获取到，设置他们的时机可以是controller获取到domain的list后遍历这个list,去设置。当然这样如果有多个类似list的方法，就要做多次，比较不友好。好处是这个阶段在controller内，可以用到所有的grails bean.
```$xslt
def listAllComponentByTree() {
        List<Resource> resources = Resource.findAllByTypeAndParentId(UriType.COMPONENT, -1)

        List ret = []

        List<Resource> root = resources.stream()
                .filter{r-> r.parentId == -1 && r.type == UriType.COMPONENT}.collect(Collectors.toList());
        root.each {
            ret.add(getResourceTree(it, null))
        }
```
+ 另一个阶段是在转json时，使用JSON.registerObjectMarshaller，去声明一个该类转json时的操作.类似
```$xslt
 JSON.registerObjectMarshaller(Resource) {Resource resource->
            def returnSet = [:]
            returnSet.id = resource.id //untransients properties
           ...//需要声明所有需要返回的字段

            if (resource.extend != null)
                returnSet.extendProperties = new groovy.json.JsonSlurper().parseText(resource.extend) //transients properties
            returnSet.childrens = resource.childrens
            if (returnSet.extendProperties != null)
                returnSet.putAll(returnSet.extendProperties)
            return returnSet
        }

```
好处是比较方便，一次声明，多有的as JSON后都会生效。缺点是这样做，所有的需要转json显示的字段都要显示声明，包括非transients属性。并且这个registerObjectMarshaller声明的位置如果在bootstrap，这个时候还没有完全初始化结束，就怕transients字段的逻辑或者条件还不够。比如必要的数据还没有初始化。