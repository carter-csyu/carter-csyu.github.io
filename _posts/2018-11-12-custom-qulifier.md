---
title: "사용자 지정 @Qualifier 어노테이션 정의"
categories:
  - spring
tags:
  - qualifier
  - custom quialifier
classes: wide
---

# Defineing Custom Qualifier Annotation

자바는 동일한 유형의 여러 어노테이션을 같은 항목에 반복할 수 없다. 따라서, 다음과 같이 `@Qualifier` 어노테이션을 중복으로 사용할 경우 아래와 같은 컴파일 오류가 발생한다.

> `Duplicate annotation of non-repeatable type @Qualifier. Only annotation types marked @Repeatable can be used multiple times at one target.`

```java
@Component
@Qualifier("cold")
@Qualifier("creamy")
public class IceCream implements Dessert { ... }
```
```java
@Autowired
@Qualifier("cold")
@Qualifier("creamy")
public void setDessert(Dessert dessert) {
  this.dessert = dessert
}
```

　  

위와 같은 문제가 발생할 경우 사용자 지정 어노테이션을 생성하여 해결할 수 있다.  
`@Qualifier` 어노테이션을 활용한 **새로운 지정 어노테이션**을 정의하여 위에서 발생한 문제를 해결해보자.

**`@Creamy` Annotation**
```java
@Target({ElementType.CONSTRUCTOR, ElementType.FIELD, 
         ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface Creamy {}
```
　  
**`@Cold` Annotation**
```java
@Target({ElementType.CONSTRUCTOR, ElementType.FIELD, 
         ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface Cold {}
```


위 코드를 통하여 `@Qualifier("creamy")`와 `@Qualifier("cold")`는 각각 `@Creamy`와 `@Cold`로 대체하여 사용할 수 있는 것을 확인할 수 있다.

> `@Qualifier("creamy")` Annotation -> `@Creamy` Annotation  
> `@Qualifier("Cold")` Annotation -> `@Cold` Annotation  

새로 생성된 어노테이션을 이용하여 위에서 발생한 문제를 해결할 수 있다.

```java
@Component
@Cold
@Creamy
public class IceCream implements Dessert { ... }
```
```java
@Autowired
@Cold
@Creamy
public void setDessert(Dessert dessert) {
  this.dessert = dessert;
}
```

### Reference

[Spring in action 4th edition - Craig Walls](https://www.manning.com/books/spring-in-action-fourth-edition)
