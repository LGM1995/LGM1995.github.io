---
title: OBJECT DAO, DTO, VO, ENTITY의 차이
categories: [Learn, Spring, Object]
tags: [vo, dto, dao, entity]		# TAG는 반드시 소문자로 이루어져야함!
lastmod : 2023-03-26 10:48:00 # 페이지의 마지막 수정일
sitemap :
changefreq : daily # 페이지의 변경 빈도 always/hourly/daily/weekly/monthly/yearly/never
priority : 1.0 # 페이지의 검색순위로 검색엔진에게 우선순위를 알려줌 0.0-1.0 (defult 0.5)
---

# 데이터를 다루기 위한 객체 VO, DTO, DAO, ENTITY의 차이

## DAO(Data Accecss Object)

모든 객채는 이름을 자세히 보면 설명이 나와있다. DAO는 말 그대로 실제 DB에 접근하여 데이터의 삽입, 삭제, 조회 기능을 수행하기 위한 객체이다. 보통 DB와 연결된 커넥션풀이 설정되어 있으며
Mybatis의 경우 root-context.xml / JPA의 경우 apllication.properties에 DB 커넥션 정보가 설정되어 있다.

데이터 베이스에 접근한다는 측면에서 Repository와 혼동이 될 수 있으나 repostiry에서는 여러 DAO를 다룰 수 있다는 점에서 조금 더 상위 레벨로 볼 수 있다.

```
public class MemberDAO {
    private Connection connection;
    private PreparedStatement preparedStatement;
    private ResultSet resultSet;

    public MemberDAO() {
        try {
            String URL = "jdbc:mysql://localhost:8080/~~~~;
            String ID = "root";
            String Password = "root";
            Class.forName("com.mysql.cj.jdbc.Driver");
            connection = DriverManager.getConnection(URL, ID =, Password);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    // 로그인 과정
    public int login(String email, String userPassword) {
        String SQL = "select userPassword from member where email= ?";
        try {
            preparedStatement = connection.prepareStatement(SQL);
            preparedStatement.setString(1, email);
            resultSet = preparedStatement.executeQuery();
            if (resultSet.next()) {
                if (resultSet.getString(1).equals(userPassword))
                    return 1; // 로그인 성공
                else
                    return 0; // 비밀번호 오류
            }
            return -1; // 아이디 오류
        } catch (Exception e) {
            e.printStackTrace();
        }
        return -2; // 데이터베이스 오류
    }
}
```

위에는 email과 passowrd를 토대로 원하는 결과값을 얻기위해 DB에 접근하는 MemberDAO 객체이다.

## DTO(Data Transfer Object)

DTO는 데이터 전송 객채로 주로 비동기 처리를 위해 사용되는 가변 객체이다. 레이어간의 데이터 교환을 위해 사용되며 필요한 데이터를 하나씩 만드는 것이 아니라 특정 객체 형태로 보낼 수 있다는 장점이 있다.
getter와 setter의 메소드만 가지며 어떠한 비즈니스 로직을 가져선 안된다.

```
public class LoginDTO {

  private String email;
  private String password;

  public String getEmail() {
    return email;
  }

  public String getPassword() {
    return password;
  }

  public void setEmail(String email) {
    this.email = email;
  }

  public void setPassword(String password) {
    this.password = passowrd;
  }
}
```

클라이언트의 로그인 요청에 대한 아이디와 비밀번호를 전송할 객체이다. 현재는 커넥션풀이 제공되어 실제로 DAO를 만들어 사용하는 경우는 극히 드물다.

## VO(Value Object)

Read-Only의 속성을 가진 객체로 getter의 기능만을 가지고 있다. 다른 객체들과 다르게 equals()와 hashcode()를 오버라이딩하여 객체에 선언된 필드의 값이 같으면 같은 객체라고 판단한다.

```
public class ProductVO {

  private String name;
  private long price;

  public String getName() { return name; }

  public long getPrice() { return price; }

  public boolean discountable() {
    return this.price > 30000;
  }

}
```

상품이라는 ProductVO 객체는 이름과 가격이라는 값을 가지고 있으며 3만원이 넘는가에 대한 true, false를 리턴하는 로직도 가지고 있다. 또한 equals와 hashcode를 오버라이딩 하여
만약 같은 값의 상품이름과 가격을 가진 product1, prouduct2가 있다고 가정하면 두 객체는 같은 것으로 판단한다.

## Entity

주로 JPA 사용시 DB와 1대1 매핑된 클래스로 DB에 존재하는 속성(필드) 값만 가질 수 있으며 가장 Core한 클래스로 상속이나 구현체의 사용이 불가능 하다. DB에 직접적인 영향을 미칠 수 있는 객체로 외부의 접근을 최소하 하고 데이터 일관성을 지키기 위해
setter를 사용을 지양하고 생성자를 통해 값들을 설정한다. 일반적으로 @Entity를 사용한 클래스의 카멜케이스를 적용하면 DB 테이블의 언더스코어 네이밍과 매핑된다.

```
ex) BusinessMember(클래스) => business_member(테이블)
```

```
@Entity
@Getter
@Setter
@Builder
@Table(name = "\"user\"")
@AllArgsConstructor
@NoArgsConstructor
public class User {

    @Id
    @Column(name = "user_id") // 회원 고유번호 INDEX
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long userId;

    @Column(name = "username", length = 50, unique = true) // 실제 회원 아이디
    private String username;

    @Column(name = "password", length = 100) // 비밀번호
    private String password;

    @Column(name = "name", length = 50) // 회원 실명
    private String name;

    @Column(name = "activated")
    private boolean activated;

    @Column(name = "email", length = 100, unique = true)
    private String email;

    @ManyToMany
    @JoinTable(
            name = "user_authority",
            joinColumns = {@JoinColumn(name = "user_id", referencedColumnName = "user_id")},
            inverseJoinColumns = {@JoinColumn(name = "authority_name", referencedColumnName = "authority_name")})
    private Set<Authority> authorities;
}
```

실제 유저에 대한 user라는 테이블과 매핑된 클래스로 모든 필드값은 테이블과 1대1로 대응되는 컬럼들이다. DB테이블에 명시되지 않은 필드값을 가지고 있을 수 없으며 다른 구현체의 상속을 받을 수 없고 핵심 업무인 domain 로직을 제외한 부가적인 로직을 가져선 안된다.

### DTO와 VO의 차이

두 객체는 모두 값을 가지고 있다는 점에서 공통점이 있지만 그 사용 목적에 있어서 차이를 가진다. DTO는 오로지 데이터를 송수신 하기 위해서만 사용되고 그 이외의 개념으로는 사용되지 않는다.
그러나 위에서 보았던 상품이라는 vo는 객체 자체로서 의미를 가지고 그 값을 비교하거나 다른 목적으로도 사용될 수 있다.

만약 위에서 DTO를 값을 비교하기 위해 사용했다면 같은 이메일과 password로 생성된 DTO user1과 user2는 명목상 같은 객체로 봐야하지만 다른 객체로 판별된다.


### DTO와 Entity의 분리

보통 JPA를 사용하는 경우에 DTO를 전반적으로 모든 레이어의 이동에 사용하고 마지막에 Entity를 활용하여 DB 조작이 이루어 진다.
이를 분리하는 이유는 DB Layer와 View Layer를 분리하기 위함인데 만약 이를 분리하지 않고 Entity만을 사용한다면 어떤 문제점이 발생할까?

 * 불필요한 데이터의 전송이 이뤄진다.
   * 회원의 이름과 생년월일만 필요로 하는 서비스에서 회원 데이터 테이블에 있는 모든 컬럼을 다 전송해야하며 이는 결국 트래픽을 늘리고 데이터 전송시간을 늘리는 불필요한 데이터 송신이 된다.
   * 또한 개인 정보와 같이 민감한 내역의 정보도 송신하게 되어 정보 유출을 야기 시킨다.
 * 데이터의 관리가 어렵다.
   * Entity는 데이터와 직접 연결된 객체로 아무 레이어에서의 무분별한 접근은 데이터를 통제하기 어려워진다.
 * 비즈니스 로직과 데이터베이스 로직의 분리가 어렵다.
   * Entity는 값을 조작하는 핵심 도메인 로직만 있어야 하는데 DB 컬럼내에 존재하지 않는 회원 별 할인율을 반환한다거나 상태값을 반환하는 등의 다양한 비즈니스 로직을 분리할 수가 없다.

### 마무리

각자의 특성을 표로 분리 하자면 다음과 같다.

| 분류       | DTO                             | VO                                         | ENTITY                                               |
|----------|---------------------------------|--------------------------------------------|------------------------------------------------------|
| 사용목적     | 레이어 사이의 값 전달                    | 값 표현                                       | DB와 매핑                                               |
| 변경 용이성   | 일반적으로는 가변객체로 사용하나 불변 객체로도 사용 가능 | 값 자체로서 의미를 가지는 불변 객체이며 필요하면 새로 만들어 재할당 해야함 | 보통은 setter의 사용을 지양하여 불변 객체로 사용하나 필요에 따라 가변 객체로 사용 가능 |
| 로직 포함 여부 | 어떠한 로직도 사용할 수 없음                | 다양한 로직을 포함할 수 있으며 어느 레이어에서도 사용이 가능         | 오로지 도메인 로직만 가져야하며 Presentaion 로직(UI/UX적 요소) 를 가져선 안됨 |

하지만 실제 현업에서는 이들이 혼용되어 사용되거나 다르게 사용되는 경우가 다반사다. 그렇기 때문에 팀과의 미리 약속된 코딩 가이드에 따라 유동적으로 조절할 수 있어야한다.

>#### 개인적인 이해를 바탕으로 작성한 글 입니다. 피드백 언제든 환영합니다!
