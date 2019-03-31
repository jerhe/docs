# SpringMVC下的Shiro权限框架的使用

# SpringMVC+Shiro权限管理

 

博文目录

博客源地址：http://doc.orz520.com/a/doc/2014/0529/2022241.html?from=haosou

1. [权限的简单描述](http://doc.orz520.com/a/doc/2014/0529/2022241.html?from=haosou)
2. [实例表结构及内容及POJO](http://doc.orz520.com/a/doc/2014/0529/2022241.html?from=haosou)
3. [Shiro-pom.xml](http://doc.orz520.com/a/doc/2014/0529/2022241.html?from=haosou)
4. [Shiro-web.xml](http://doc.orz520.com/a/doc/2014/0529/2022241.html?from=haosou)
5. [Shiro-MyShiro-权限认证,登录认证层](http://doc.orz520.com/a/doc/2014/0529/2022241.html?from=haosou)
6. [Shiro-applicationContext-shiro.xml](http://doc.orz520.com/a/doc/2014/0529/2022241.html?from=haosou)
7. [HomeController](http://doc.orz520.com/a/doc/2014/0529/2022241.html?from=haosou)
8. [三个JSP文件](http://doc.orz520.com/a/doc/2014/0529/2022241.html?from=haosou)

 **什么是权限呢**？举个简单的例子：
我有一个论坛，注册的用户分为normal用户，manager用户。
对论坛的帖子的操作有这些：
添加，删除，更新，查看，回复
我们规定：
normal用户只能：添加，查看，回复
manager用户可以：删除，更新
normal，manager对应的是角色（role）
添加，删除，更新等对应的是权限（permission）
我们采用下面的逻辑创建权限表结构（不是绝对的，根据需要修改）
一个用户可以有多种角色（normal,manager,admin等等）
一个角色可以有多个用户（user1,user2,user3等等）
一个角色可以有多个权限（save,update,delete,query等等）
一个权限只属于一个角色（delete只属于manager角色）

![img](http://img.orz520.com/t01107cba7e76d43d1a.png)

 我们创建四张表：
**t_user**用户表:设置了3个用户
\-------------------------------
id + username   + password
---+----------------+----------
1  +   tom           +  000000
2  +   jack           +  000000
3  +   rose          +  000000
\---------------------------------
**t_role**角色表：设置3个角色
\--------------
id + rolename 
---+----------
1  + admin
2  + manager
3  + normal
\--------------
**t_user_role**用户角色表：tom是admin和normal角色，jack是manager和normal角色，rose是normal角色
\---------------------
user_id  +  role_id
-----------+-----------
1            +     1
1            +     3
2            +     2
2            +     3
3            +     3
\---------------------
**t_permission**权限表：admin角色可以删除，manager角色可以添加和更新，normal角色可以查看
\-----------------------------------
id  +  permissionname  +  role_id
----+------------------------+-----------
1   +   add                     +     2
2   +   del                       +    1
3   +   update                +     2
4   +   query                   +    3
\-----------------------------------