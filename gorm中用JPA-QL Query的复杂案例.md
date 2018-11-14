# gorm中用JPA-QL Query的复杂案例

##初始预期
+ 查询RoleResourceRelation关联的表Resource的id和实体记录
+ 代码
```
@Query("select roleResourceRelation.resource.id from $RoleResourceRelation as roleResourceRelation where roleResourceRelation.sysRole.id = ${roleId}")
    abstract List<RoleResourceRelation> findBySysRoleId(Long roleId)

@Query("select roleResourceRelation.resource from $RoleResourceRelation as roleResourceRelation where roleResourceRelation.sysRole.id = ${roleId}")
    abstract List<Resource> findResourceBySysRoleId(Long roleId)
```
##hql使用
+ 利用hql的特性可以直接在from RoleResourceRelation 的情况下select resource。
+ 第二个就是这里面的字段都是按照domain里定义的属性字段名
