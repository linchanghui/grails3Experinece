# transients类型的domain属性，无法在controller的create或save时获取到

##初始预期
+ transients类型的domain属性，无法在controller的create或save的函数，如果是入参是domain类型的时候，请求里的字段是transients声明的，不会被自动绑定，所以需要设置bindable：true

+ 属性解释：Statically typed instance properties are bindable by default. Properties which are not bindable by default are those related to transient fields, dynamically typed properties and static properties.