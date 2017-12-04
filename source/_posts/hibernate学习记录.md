---
title: hibernate学习记录
date: 2015-07-06 15:15:20
tags: 
- hibernate
- database

---

## merge 与 update

对于transient对象,指明id后,可以使用merge来更新对应数据库条目,也可以使用update来更新,但是如果当前session中存在该id的记录,使用update会抛出异常.

对于detached对象,两者相似,update会将对象持久化,merge不会改变对象状态?(待验证)

对于persist对象,两者相同?(待验证)

## merge 与 persist

persist会将对象持久化,merge不会,其他方面类似,比如如下代码

```java
User user = new User();
user.setName("good user");
sessionFactory.getCurrentSession().merge(user);
//user.getId() == 0;
```

merge后会执行insert语句,但并不会改变user的状态,user.id==0

```java
User user = new User();
user.setName("good user");
sessionFactory.getCurrentSession().persist(user);
//user.getId() != 0;
```

persist后也会执行insert语句,但会改变user的状态为persistent,user.id!=0,修改user的属性在transaction结束后会更新到数据库中.

[参考stackoverflow回答](http://stackoverflow.com/questions/1069992/jpa-entitymanager-why-use-persist-over-merge)


## orphanRemoval=true 与 cascade=CascadeType.DELETE

孤值删除包括了级联删除的情况. 同时孤值删除还会处理额外的情况, 当断开关系后update时会自动删除有外键的一方.


## hibernate继承

注解方式example

```java
@Entity
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
@DiscriminatorColumn(name = "field", discriminatorType = DiscriminatorType.STRING)
@DiscriminatorValue("dummy")
public class EntitySuperClass {
// here definitions go 
// but don't define discriminator column here
}

@Entity
@DiscriminatorValue(value="sub1")
public class Sub1Class extends EntitySuperClass {
// here definitions go
}
```


## hibernate在jsp中lazyloading失效的解决方法

配置enable_lazy_load_no_trans为true


未完待续...

