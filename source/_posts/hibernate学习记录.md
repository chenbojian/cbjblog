title: hibernate学习记录
date: 2015-07-06 15:15:20
tags:
---
## merge 与 update

对于transient对象,指明id后,只能使用merge来更新对应数据库条目,使用update会抛出异常
对于detached对象,两者相似,update会将对象持久化,merge不会改变对象状态?(待验证)
对于persist对象,两者相同?(待验证)

## merge 与 persist
persist会将对象持久化,merge不会,其他方面类似
[参考stackoverflow回答](http://stackoverflow.com/questions/1069992/jpa-entitymanager-why-use-persist-over-merge)



未完待续...

