---
title: "Apache commons lang3"
categories:
  - java
tags:
  - apache commons
  - apache commons lang
  - apache commons lang3
  - reflection
layout: posts
classes: wide
---

# Apache commons lang3 (ToStringBuilder, HashCodeBuilder, EqualsBuilder)

Apache commons lang 에서 제공하는 편리한 클래스인 `ToStringBuilder`, `HashCodeBuilder`, `EquailsBuilder`의 리플랙션(replection) 기능을 소개하고자 한다. 위 세 클래스에서 제공하는 리플랙션(reflection) 메소드를 이용하면 객체(Object)의 출력, 객채(Object)의 비교 등을 쉽게 처리할 수 있다.

## 구현 결과물

각 편의 기능에 알아보기 전에 어떤 방식으로 사용하면 될지에 대하여 구현 결과물로 알아보자.

```java
import org.apache.commons.lang3.builder.EqualsBuilder;
import org.apache.commons.lang3.builder.HashCodeBuilder;
import org.apache.commons.lang3.builder.ToStringBuilder;
import org.apache.commons.lang3.builder.ToStringStyle;

public class User {
  private String userid;
  private String password;
  private String email;
 
  public User() {}
  public User(String userid, String password, String email) {
    this.userid = userid;
    this.password = password;
    this.email = email;
  }
  
  public String getUserid() {
    return userid;
  }

  public void setUserid(String userid) {
    this.userid = userid;
  }

  public String getPassword() {
    return password;
  }

  public void setPassword(String password) {
    this.password = password;
  }

  public String getEmail() {
    return email;
  }

  public void setEmail(String email) {
    this.email = email;
  }
  
  @Override
  public String toString() {
    return ToStringBuilder.reflectionToString(this, ToStringStyle.NO_CLASS_NAME_STYLE);
  }
  
  @Override
  public int hashCode() {
    return HashCodeBuilder.reflectionHashCode(this, "userid", "password");
  }
  
  @Override
  public boolean equals(Object obj) {
    return EqualsBuilder.reflectionEquals(this, obj, "userid", "password");
  }
}
```

위의 코드를 아래의 각 클래스와 메소드에 대한 소개를 통하여 하나씩 설명한다.  


## `ToStringBuilder` Class

- [ToStringBuilder api doc](https://commons.apache.org/proper/commons-lang/apidocs/org/apache/commons/lang3/builder/ToStringBuilder.html)


### `refelectionToString()` method

| Modifier and Type | Method |
| :-- | :-- |
| `static String` | `reflectionToString(Object object)` |
| `static String` | `reflectionToString(Object object, ToStringStyle style)` |
| `static String` | `reflectionToString(Object object, ToStringStyle boolean outputTransients)` |

위 메소드들은 객체를 간편하고 보기좋게 출력하는데 사용된다. 아래의 `ToStringStyle`의 선택에 따라 어떤식으로 출력이 되는지 알아보자.

### `ToStringStyle` Class

| Modifier and Type | Method |
| :-- | :-- |
| `static ToStringStyle` | `DEFAULT_STYLE` |
| `static ToStringStyle` | `JSON_STYLE` |
| `static ToStringStyle` | `MULTI_LINE_STYLE` |
| `static ToStringStyle` | `NO_CLASS_NAME_STYLE` |
| `static ToStringStyle` | `NO_FIELD_NAMES_STYLE` |
| `static ToStringStyle` | `SHORT_PREFIX_STYLE` |
| `static ToStringStyle` | `SIMPLE_STYLE` |

`ToStringStyle`에서 제공하는 필드값은 위와 같다.

```java
public void testToStringStyle() {
  User user = new User("userid", "password", "chunsang.yu@gmail.com");

  System.out.println(ToStringBuilder.reflectionToString(user, ToStringStyle.DEFAULT_STYLE));
  // devchun.User@6debcae2[userid=userid,password=password,email=chunsang.yu@gmail.com]
  
  System.out.println(ToStringBuilder.reflectionToString(user, ToStringStyle.JSON_STYLE));
  // {"userid":"userid","password":"password","email":"chunsang.yu@gmail.com"}
  
  System.out.println(ToStringBuilder.reflectionToString(user, ToStringStyle.MULTI_LINE_STYLE));
  /*    
    debchun.User@6debcae2[
    userid=userid
    password=password
    email=chunsang.yu@gmail.com
  ]*/
  
  System.out.println(ToStringBuilder.reflectionToString(user, ToStringStyle.NO_CLASS_NAME_STYLE));
  // [userid=userid,password=password,email=chunsang.yu@gmail.com]
  
  System.out.println(ToStringBuilder.reflectionToString(user, ToStringStyle.NO_FIELD_NAMES_STYLE));
  // devchun.User@6debcae2[userid,password,chunsang.yu@gmail.com]
  
  System.out.println(ToStringBuilder.reflectionToString(user, ToStringStyle.SHORT_PREFIX_STYLE));
  // User[userid=userid,password=password,email=chunsang.yu@gmail.com]
  
  System.out.println(ToStringBuilder.reflectionToString(user, ToStringStyle.SIMPLE_STYLE));
  // userid,password,chunsang.yu@gmail.com
}
```

위와 같이 간단한 방법으로 `JSON` 타입으로, 프로퍼티 각각에 대한 다중 라인으로, 클래스 패스의 노출 제한,  클래스패스와 클래스명의 노출 제한, 프로퍼티 값만 노출 등 다양한 형태로 출력 가능하다.  
필요 기능에 따라 객채의 선언시 `toString()` 메소드를 오버라이딩하여 사용하면 된다.

```java
@Override
public String toString() {
  return ToStringBuilder.reflectionToString(this, ToStringStyle.NO_CLASS_NAME_STYLE);
}
```

## `HashCodeBuilder` Class

- [HashCodeBuilder api doc](https://commons.apache.org/proper/commons-lang/apidocs/org/apache/commons/lang3/builder/HashCodeBuilder.html)

### `reflectionHashCode()` method

`reflectionHashCode()`는 다양한 오버로딩을 통하여 구현 메소드가 존재한다. 하지만 그중에서 편하다고 생각하는 하나의 메소드에 대해서만 이 포스트에서는 소개하고자 한다.

| Modifier and Type | Method |
| :-- | :-- |
| `static int` | `reflectionHashCode(Object object, String... excludeFields) |

여기서 주요하게 보아야 할 프로퍼티는 `String... excludeFields`이다. 객체 내에 포함된 필드 중 제외하고자 하는 필드의 명칭을 나열하면, 해당 필드를 제외한 필드에 대하여 해시코드(hashcode)를 반환 받을 수 있다.

**사용 예제**

위의 **구현 결과물**에 나온 것처럼 `userid`와 `password`를 제외하여 `email` 필드만을 가지고 결과값을 도출하고자 할 경우 아래와 같다.

```java
@Override
public int hashCode() {
  return HashCodeBuilder.reflectionHashCode(this, "userid", "password");
}
```

## `EqualsBuilder` Class 

- [EqualsBuilder api doc](https://commons.apache.org/proper/commons-lang/apidocs/org/apache/commons/lang3/builder/EqualsBuilder.html)

### `reflectionEquals()` method

`reflectionEquals()`는 `reflectionHashCode()`와 마찬가지로 다양한 오버로딩을 통하여 구현 메소드가 존재한다. 하지만 그중에서 편하다고 생각하는 두개의 메소드에 대해서만 이 포스트에서는 소개하고자 한다.

| Modifier and Type | Method |
| :-- | :-- |
| `static boolean` | `reflectionEquals(Object lhs, Object rhs, String... excludeFields) |
| `static boolean` | `reflectionEquals(Object lhs, Object rhs, Collection<String> excludeFields) |

여기서 주요하게 보아야 할 프로퍼티는 `String... excludeFields`와 `Collection<String>`이다. 객체 내에 포함된 필드 중 제외하고자 하는 필드의 명칭을 나열하거나 대상이 담긴 콜랙션 객체를 사용할 경우, 해당 필드를 제외한 필드에 대하여 비교할 수 있다.

**사용 예제**

위의 **구현 결과물**에 나온 것처럼 `userid`와 `password`를 제외하여 `email` 필드만을 가지고 객체 간 비교를 하고자 할 경우 아래와 같이 작성 가능하다.

```java
@Override
public boolean equals(Object obj) {
  return EqualsBuilder.reflectionEquals(this, obj, "userid", "password");
}
```

사용자 객체(`User`)를 비교하는데 있어 아이디(`userid`), 비밀번호(`password`)가 같지 않아도 이메일(`email`)만 같을 경우 같은 객체인지 알아볼 수 있는 코드이다. 테스트 코드를 통하여 어떤 결과물이 나올지 예상해보고 실제 결과는 어떻게 나오는지 알아보자.

```java
@Test
public void testLang3() {
  User userOne = new User("user1", "user1password", "chunsang.yu@gmail.com");
  User userTwo = new User("user2", "user2password", "chunsang.yu@gmail.com");

  logger.info(user1.equals(user2) ? "true" : "false"); // true
}
```

위의 예제는 사용자의 아이디, 비밀번호는 다르지만 이메일 객체는 동일하다. 따라서, `reflectionHashCode()`, `reflectionEquals()`를 사용하는데 있어 제외하고자 하는 필드에 아이디, 비밀번호를 등록하였기 때문에 이메일만을 가지고 객체간 비교를 수행하며, 동일한 메일을 가지기 때문에 위의 객체는 동일하다는 결과를 얻을 수 있다.

## 결론

사용 목적에따라 객체를 `toString()`, `hashCode()`, `equals()` 메소드를 오버라이딩하여 사용할 경우 위와같이 간편하게 적용해서 사용할 수 있는 것을 알아봤다. 코드 자체가 말이 안될수도 있지만, 현재 구현하고 있는 코드에 대하여 적용을 어떻게 하면 좋을지에 대하여 생각해보면 좋을 것 같다.

