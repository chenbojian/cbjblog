title: hibernate orphanRemoval and cascade
date: 2015-07-03 17:02:48
tags:
---

[查看原文](http://stackoverflow.com/questions/18813341/what-is-the-difference-between-cascade-and-orphan-removal-from-db)

##Cascading Remove

Marking a reference field with CascadeType.REMOVE (or CascadeType.ALL, which includes REMOVE) indicates that remove operations should be cascaded automatically to entity objects that are referenced by that field (multiple entity objects can be referenced by a collection field):

```java
@Entity
class Employee {
    @OneToOne(cascade=CascadeType.REMOVE)
    private Address address;
}
```

##Orphan Removal

JPA 2 supports an additional and more aggressive remove cascading mode which can be specified using the orphanRemoval element of the @OneToOne and @OneToMany annotations:

```java
@Entity
class Employee {
    @OneToOne(orphanRemoval=true)
    private Address address;
}
```

##DIFFERENCE:

The difference between the two settings is in the response to disconnecting a relationship. For example, such as when setting the address field to null or to another Address object.

If orphanRemoval=true is specified the disconnected Address instance is automatically removed. This is useful for cleaning up dependent objects (e.g. Address) that should not exist without a reference from an owner object (e.g. Employee).
If only cascade=CascadeType.REMOVE is specified no automatic action is taken since disconnecting a relationship is not a remove operation.
