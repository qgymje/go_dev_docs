# 关于权限系统设计的思考

权限控制分两类,一个是控制权限,第二个是在获取控制权限后,对数据的控制,称为数据权限

## 控制权限

#### 权限

通过有意设计的url规则, 如 HOST/blog/create, HOST/blog/delete 等操作来确定两个概念: 资源与操作,在这里资源是blog, 操作是create和delete
资源与操作组合成一个概念叫做权限. 
权限通常有的方法为hasPermission(ROLE, RESOURCE, OPERATOR)

#### 角色

角色是权限的集合,角色可以被继承, 可以被组合形成一个组,一个用户可以拥有多个角色,可以在多个组里.
角色通常有的方法为assignRole(USER, ROLE) dismissRole(USER, ROLE)


