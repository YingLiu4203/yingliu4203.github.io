---
layout: post
title: Jpa Criteria API note
categories:
- Development
tags:
- JPA
---

This is a study note of Jpa criteria api from resources of http://www.ibm.com/developerworks/java/library/j-typesafejpa/ (all diagram are linked from this site) and the book [Pro JPA 2 2nd Edition](https://www.amazon.com/Pro-JPA-Experts-Voice-Java/dp/1430249269/). 

# 1. Basic Concepts
A JPA query is a tree of query-expression nodes that contains SQL query clauses such as `FROM`, `WHERE`, `ORDER BY`. The following diagram shows its constructs.  

![JPA query constructs diagram](http://www.ibm.com/developerworks/java/library/j-typesafejpa/UML-query.gif)


All constructs are subclass of an `Expression<T>` interface. The following is the query expression hierarchy.  

![Query Expression Hierarchy Figure](http://www.ibm.com/developerworks/java/library/j-typesafejpa/UML-expressions.gif)

Here are some expression examples: 

* A `Root<T>` is an expression acting as the SQL `FROM`. The query will be evaluated against a JPA entity type. A `Root<T>` is a special `Path<T>` with no parent. 
* A `Predicate` is a form of query expression that evaluates to either `true` or `false`. It is the SQL `WHERE`. 
* A path expression is the result of navigation from a root expression via one or more persistent attribute(s).  

# 2. Metamodel
JPA uses metamodel classes to describe persistent entity types. It can be generated at development time. A metamodel class is an easier way of refereing to meta information of a persistent entity. For example, `Person_.age` is type safe and simple, bettern than `Person.class.getField("age")`. For each of the entity type, JPA generates a static public field that is either a `SingularAttribute` or `PluraAttribute`. 

The top level metamodel structure. 

![JPA metamodel structure](http://www.ibm.com/developerworks/java/library/j-typesafejpa/UML-metamodel.gif)

The meetamodeal types hierarchy. 

![JPA metamodel type hierarchy diagram](http://www.ibm.com/developerworks/java/library/j-typesafejpa/UML-types.gif)

The Metamodel attributes hierarchy. 

![JPA metamodel attribute hierarchy diagram](http://www.ibm.com/developerworks/java/library/j-typesafejpa/UML-attrs.gif)

In JPA, a set of mutually referable classes is within a `persistence unit`. Metamodel classes are generated by an annotation processor. 

# 3. Build Queries
JPA provides many constructs to build queries. 

* Functional Expressions: apply a function to expressions or literal values to create a new expression. For example, `cb.avg`, `cb.greaterThan`.
* Complex predicates: such as `in()` applies to a variable number of expressions. 
* Join expressions: a join expression allows query multiple entities. 
* Parameter expresssion: a paramter expression is created with explicit type information. 
* Select expression: the `select()` and `multiselect()` expression using projection terms to specify result. There are methods such as `construct()`, `array()`, and `tuple()` to shape the result. 

# 4. Build Dynamic Queries
JPA support dynamic query by allowing persistent types and attributes to be referred by names. For example: 

```java
Class<Account> cls =Class.forName("domain.Account");
Metamodel model = em.getMetamodel();
EntityType<Account> entity = model.entity(cls); 
CriteriaQuery<Account> c = cb.createQuery(cls);
Root<Account> account = c.from(entity);
Path<Integer> balance = account.<Integer>get("balance");
c.where(cb.and
       (cb.greaterThan(balance, 100), 
        cb.lessThan(balance), 200)));
```

The APIs are weakly typed. There will be compiler wanrning for unchecked casts. 




