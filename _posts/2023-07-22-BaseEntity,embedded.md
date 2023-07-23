---
title: JPA 데이터 값타입 (BaseEntity와 Embedded Type 활용)
categories: [Learn, Java]
tags: [java, jpa ]		# TAG는 반드시 소문자로 이루어져야함!
lastmod : 2023-01-09 23:00:00 # 페이지의 마지막 수정일
sitemap :
changefreq : daily # 페이지의 변경 빈도 always/hourly/daily/weekly/monthly/yearly/never
priority : 1.0 # 페이지의 검색순위로 검색엔진에게 우선순위를 알려줌 0.0-1.0 (defult 0.5)
---

<img width="1426" alt="JPA" src="https://lgm1995.github.io/assets/img/learn/java/jpa.png">

# BaseEntity와 Embedded Type

---

오늘 공유할 내용은 JPA에서 사용되는 BaseEntity와 Embedded Type에 대한 내용이다.

## JPA 데이터 타입

---

JPA의 데이터 타입은 크게 Entity 타입과 값타입 두 분류로 나눌수 있다.

* Entity

  클래스 객체로 식별자(ID)를 지니고 있으며 DB의 영속성 데이터를 가져와 객체지향적으로 프로그래밍을 할 수 있는 클래스이다.

* 값 타입

  자바의 기본 타입이나 객체를 말하며 식별자가 없고 임베디드 타입을 통해 복합 값(객체)를 사용할 수 있다.
  엔티티와 같은 생명주기를 공유한다는 특성이 있으며 값 타입은 서로 공유되어선 안된다.(값을 복사하여야 함)


## 임베디드 타입(Embadded Tpye)

---

복합 값 타입으로 불리우는 임베디드 타입은 엔티티의 값으로 쓰이는 객체이다.


```java
@Entity
@Table(name = "member")
public class MemberEntity {

    @Id
    @Column(name = "id")
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "name") // 이름
    private String name;

    // 주소 정보
    @Column(name = "zipCode")
    private String zipCode; // 우편번호

    @Column(name = "city")
    private String city; // 시

    @Column(name = "district")
    private String district; // 구

    @Column(name = "address_detail")
    private String detail; // 상세주소


}
```

member 테이블과 매핑될 Entity클래스 MemberEntity를 만들었다.

회원은 이름과 주소를 가지고 있는데 주소는 우편번호, 시, 구, 상세주소를 가지고 있다.

```java
@Embeddable
@Getter
public class Address {

    // 주소 정보
    @Column(name = "zipCode")
    private String zipCode; // 우편번호
    @Column(name = "city")
    private String city; // 시
    @Column(name = "district")
    private String district; // 구
    @Column(name = "address_detail")
    private String detail; // 상세주소

    // 기본 생성자를 필수로 만들어야 한다.
    public Address () {}

    // 값 객체는 값을 복사해서 사용해야 참조 오류를 막을 수 있다 따라사 변경이 생기면 새로 다시 객체를 만들어 사용해야 한다.
    public Address(String zipCode, String city, String district, String detail) {
        this.zipCode = zipCode;
        this.city = city;
        this.district = district;
        this.detail = detail;
    }
}
```

주소는 단순히 값으로 사용될 필드들이고 이는 바뀌는 일이 거의 없지만 회사나 명소 등 다른 엔티티에서도 사용해야 할 확률이 높다.

그래서 이를 Address 라는 복합 값 객체 클래스로 만들어 사용할 수 있다.

```java
@Entity
@Table(name = "member")
public class MemberEntity {

    @Id
    @Column(name = "id")
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "name") // 이름
    private String name;

    // 주소 정보
    @Embedded
    private Address address;

}
```

임베디드 타입으로 사용할 객체에 @Embeddable을 달고 Entity에서 @Embedded 타입을 명시하여 훨씬 간결한 코드의 사용이 가능하다.

### Embedded 타입의 주의 사항

---
<em>
Setter의 사용을 지양해야 한다.

만약 Address 주소 객체 하나의 주소값을 여러 MemberEntity가 참조하고 있다면 하나의 회원 주소값 변경시 연관된 다른 회원들의 주소도 바뀌어 버린다.

Setter를 삭제하고 주소 수정 이벤트가 발생시 새로운 Address 객체로 변경하는게 좋다.
</em>

## BaseEntity

---

DB의 데이터를 관리하기 위해서는 일반적으로 생성일과 마지막 업데이트 날짜를 기록하는게 통상적이다.

```java

@Entity
@Table(name = "member")
@Setter
@Getter
public class MemberEntity {

    @Id
    @Column(name = "id")
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "name") // 이름
    private String name;


    @Embedded // 주소 정보
    private Address address;

    @Column(name = "creator") // 생성자
    private Long creator;

    @Column(name = "created_at") // 생성 날짜
    private LocalDateTime createdAt;

    @Column(name = "updater") // 업데이트자
    private Long updater;

    @Column(name = "updated_at") // 업데이트 날짜
    private LocalDateTime updatedAt;

}
```

디비와 매핑될 Entity에 생성자, 생성날짜, 업데이트자, 업데이트 날짜 컬럼을 추가 하였다.

하지만 이 코드는 관리될 대상의 데이터와 연결된 모든 Entity에 추가되야하고 공통적으로 상태값이라던가 새로운 관리 대상이 생기는 경우 모든 Entity에 추가해줘야하는 불편함이 발생한다.

### 상속받을 Entity 만들기

---

```java
@MappedSuperclass // Entity에서 일반클래스지만 상속받을 수 있도록 하는 어노테이션
@Getter
@Setter
public class BaseEntity {

    @Column(name = "creator", updatable = false) // 생성자
    private Long creator;

    @Column(name = "created_at", updatable = false) // 생성 날짜
    private LocalDateTime createdAt;

    @Column(name = "updater") // 업데이트자
    private Long updater;

    @Column(name = "updated_at") // 업데이트 날짜
    private LocalDateTime updatedAt;

    @PrePersist // JPA가 EntityManager를 통해 persist() 메소드가 실행되어 영속화 될 때 작동하는 어노테이션
    public void prePersist() {
        Long userId = SecurityUtil.getCurrentMember(); // 스프링 시큐리티 유틸을 구현하여 로그인된 사용자의 아이디를 가져오는 함수를 구현했다.
        LocalDateTime now = LocalDateTime.now();
        createdAt = now;
        creator = username;
        updatedAt = now;
        updater = username;
    }

    @PreUpdate // update가 감지 될떄 작동하는 어노테이션
    private void preUpdate() {
        Long userId = SecurityUtil.getCurrentMember();
        updatedAt = LocalDateTime.now();
        updater = username;
    }

}


```

위처럼 순수 JPA를 사용하여 BaseEntity 객체를 만들어 상속하면 코드가 Clean 해진다.

```java

@Entity
@Table(name = "member")
@Setter
@Getter
public class MemberEntity extends BaseEntity{

    @Id
    @Column(name = "id")
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "name") // 이름
    private String name;


    @Embedded // 주소 정보
    private Address address;

}
```

### 좀 더 활용하기

---

```java
@Getter
@MappedSuperclass
@EntityListeners(AuditingEntityListener.class) // 스프링 시큐리티와 메인 클래스에서 @EnableJpaAuditing를 선언하는 경우 트랜잭션 커밋 시점에서 다양한 기능을 적용 시킬수 있다.
public class BaseEntity {

  @CreatedBy // Spring Security를 활용하여 로그인된 insert시 자동으로 로그인 된 회원의 정보를 DB에 저장한다.
  @Column(name = "creator", updatable = false) // 생성자
  private Long creator;

  @CreatedDate // inset 시점의 시간을 자동으로 넣어준다.
  @Column(name = "created_at", updatable = false) // 생성 날짜
  private LocalDateTime createdAt;

  @LastModifiedBy // @CreatedBy와 마찬가지로 업데이트 시점에 회원의 정보를 DB에 저장한다.
  @Column(name = "updater") // 업데이트자
  private Long updater;

  @LastModifiedDate
  @Column(name = "updated_at") // 업데이트 날짜
  private LocalDateTime updatedAt;

}
```

@EntityListeners(AuditingEntityListener.class) 어노테이션을 활용하여 날짜와 회원정보를 자동으로 주입하도록 하였다.
이 어노테이션을 사용하기 위해서는 메인 클래스에 꼭 @EnableJpaAuditing를 사용해야 한다.

또한 @CreatedBy 와 @LastModifiedBy를 사용하기 위해서는 Spring Security를 사용하며 AuditorAware를 상속받은 클래스를 만들어 getCurrentAuditor 기능을 오버라이드하여 구현하여야 한다.

```java

@Configuration
public class AuditConfig implements AuditorAware<Long> {

    @Override
    public Optional<Long> getCurrentAuditor() {
        String userId = SecurityUtil.getCurrentMember();
        return Optional.ofNullable(userId);
    }

    // 이미 SecurityUtil 클래스에 구현해놓은 유저 아이디를 구하는 로직을 활용했다.
}
```

---

### 마무리

이번 시간엔 JPA 데이터 값 타입과 조금더 객체 지향적으로 Entity를 설계 하는 방법을 알아보았다.

다음 시간에는 도메인 주도 설계 DDD (Domain-Driven Design) 대한 이론적인 내용을 알아보려 한다.

>#### 개인적인 이해를 바탕으로 작성한 글 입니다. 피드백 언제든 환영합니다!
