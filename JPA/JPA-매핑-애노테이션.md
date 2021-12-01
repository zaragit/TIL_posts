## 객체와 테이블

### @Entity

- JPA가 관리하는 엔티티 객체가 된다.
- 기본 생성자를 필수로 가져야 한다.

```java
@Entity // name = "Member" (기본값으로 클래스명을 사용)
public class Member {
	...
}
```

```java
@Entity(name="shop_member") // 직접 엔티티 이름을 변경할 수 있음 (잘 사용 X)
public class Member {
	...
}
```

<br>

### @Table

- 엔티티와 매핑할 테이블을 지정하는 경우에 사용

```java
@Entity
@Table // Member 테이블과 매핑 (생략 가능), (기본값으로 엔티티 이름을 사용)
public class Member {
	...
}
```

```java
@Entity
@Table(name="MBR") // MBR 테이블과 매핑
public class Member {
	...
}
```

<br><br>

## 필드와 컬럼

### @Id

- 기본키 매핑

```java
@Id
private Long id;
```

#### @GeneratedValue

- 기본키를 자동 생성 (@Id만 사용하는 경우 기본키를 직접 할당)
- GenerationType

  1.  IDENTITY

      - 기본 키 생성을 DB에게 위임
      - MySQL, PostgreSQL, SQL Server, DB2 에서 사용
        ex) Mysql은 AUTO_INCREMENT를 사용
      - em.persist()를 호출하자 마자 INSERT SQL 실행
        엔티티를 영속 상태로 만들기 위해서 기본키 값이 필요한데 IDENTITY 전략은 DB에 INSERT 되어야 기본키 값을 알 수 있기 때문

      ```java
      @Id
      @GeneratedValue(strategy = GenerationType.IDENTITY)
      ```

  2.  SEQUENCE

      - 시퀀스를 사용
      - ORACLE, PostgreSQL, DB2, H2 에서 사용

      ```java
      @Id
      @GeneratedValue(strategy = GenerationType.SEQUENCE)
      ```

      - **@SequenceGenerator**

        - SequenceGenerator를 사용하지 않으면 하이버네이트가 생성하는 기본 시퀀스를 사용

        ```java
        @Entity
        @SequenceGenerator(name = "MEMBER_SEQ_GENERATOR",	// Generator 이름
        					sequenceName = "MEMBER_SEQ",  	// 시퀀스 이름
        					initialValue = 1, 				// start with
        					allocationSize = 1) 			// increment by
        public class Member {
        	@Id
        	@GeneratedValue(strategy = GenerationType.SEQUENCE,
        					generator = "MEMBER_SEQ_GENERATOR")
        	private Long id;

        	...
        }
        ```

  3.  TABLE

      - 기본키 전용 테이블을 만들어서 관리
      - 테이블을 통해서 시퀀스를 흉내
      - 모든 DB에서 사용 가능하지만 성능이 느림
      - 잘 사용하지 않음
      - **@TableGenerator**

        ```java
        @Entity
        @TableGenerator(name = "MEMBER_SEQ_GENERATOR",  // Generator 이름
        				table = "MY_SEQUENCES",  		// 관리 테이블 이름
        				pkColumnName = "PK_TABLE",		// 시퀀스 컬럼명 (기본값 : 'sequence_name')
        				valueColumnName = "now",		// 시퀀스 값 컬럼명 (기본값 : 'next_val')
        				pkColumnValue = "MEMBER_SEQ",  	// 키로 사용할 값 이름 (기본값 : 엔티티 이름)
        				initialValue = 1,				// 시퀀스 start with
        				allocationSize = 1)  			// 시퀀스 increment by
        public class Member {
        	@Id
        	@GeneratedValue(strategy = GenerationType.TABLE,
        					generator = "MEMBER_SEQ_GENERATOR")
        	private Long id;
        }
        ```

        ![image](https://user-images.githubusercontent.com/74804564/143614549-cc5ac6c6-152d-45fd-9890-eb687a6ad4e6.png)

  4.  AUTO

      - 방언에 따라서 자동으로 지정

      ```java
      @Id
      @GeneratedValue(strategy = GenerationType.AUTO)
      private Long id;
      ```

<br>

### @Column

- 컬럼명이 변수명과 컬럼명이 다른 경우에 사용

```java
@Column(name = "name")
private String username;
```

```java
@Column(updatable = false) // 엔티티 값이 변경돼도 DB에 update 하지 않는다.
private String username;
```

```java
@Column(insertable = false) // 엔티티를 새로 추가해도 DB에 insert 하지 않는다.
private String username;
```

```java
@Column(nullable = false) // NOT NULL
private String email;
```

```java
/**
	*	잘 사용하지 않는다.
	*	'UK_mbmcqelty0fbrvxp1q58dn57t' 와 같이 랜덤하게 제약조건 이름이 생성기 때문
	*
	*	alter table member
	*		add constraint UK_mbmcqelty0fbrvxp1q58dn57t unique (email)
	*/
@Column(unique = true) // UNIQUE
private String email;
```

```java
@Column(length = 11) // String 타입의 경우 length를 지정할 수 있다. (기본값: 255)
private String phone;
```

```java
@Column(columnDefinition = "varchar(100) default 'NONE'") // 직접 작성
private String email;
```

```java
/**
	*	아주 큰 숫자를 저장할 때 사용
	*	자바의 BigDecimal 타입
	*
	*	- precision : 소수점을 포함한 전체 자릿수
	*	- scale : 소수의 자릿수
	*/
@Column(precision = 6, scale = 3) // 000.000 ~ 999.999
private BigDecimal visit;
```

<br>

### @Enumerated

- 자바의 Enum 타입을 컬럼에 매핑
- EnumType

  1.  String

      ```java
      public enum RoleType {
      	USER, EMPLOYEE, ADMIN
      }

      @Entity
      public class Member {
      	...

      	@Enumerated(EnumType.STRING)
      	private RoleType role; // 'USER' or 'EMPLOYEE' or 'ADMIN'으로 저장

      	...
      }
      ```

  2.  Integer
      - 사용 X
      - enum 값의 순서번호를 DB에 저장하기 때문에 나중에 문제가 생길 여지가 있음

<br>

### @Temporal

- Date를 매핑
- TemporalType

  1.  DATE
      - 날짜
      - java.sql.Date
  2.  TIME
      - 시간
      - java.sql.Time
  3.  TIMESTAMP

      - 날짜 + 시간
      - java.sql.Timestamp

      ```java
      import java.util.Date;
      public class Member {
      	...

      	@Temporal(TemporalType.TIMESTAMP)
      	private Date cratedDate;

      	...
      }
      ```

- 생략 (최신 하이버네이트 지원)

  - LocalDate : 날짜
  - LocalDateTime : 날짜 + 시간

  ```java
  import java.time.LocalDate;
  import java.time.LocalDateTime;

  @Entity
  public class Member {
  	...

  	private LocalDate cratedDate; // date 매핑

  	private LocalDateTime lastModifiedDate; // timestamp 매핑

  	...
  }
  ```

<br>

### @Lob

- CLOB, BLOB 타입 컬럼과 매핑

```java
@Lob
private String description; // CLOB으로 매핑
```

```java
@Lob
private byte[] description; // BLOB으로 매핑
```

<br>

### @Transient

- DB 컬럼과 매핑되지 않는 필드

```java
@Transient
private int temp;
```

<br> <br>

## 연관관계

### 고려사항

- **방향**
  - 단방향
  - 양방향
- **다중성**
  - 다대일 (N:1)
  - 일대다 (1:N)
  - 일대일 (1:1)
  - 다대다 (N:M)
- **연관관계의 주인 (외래키를 관리할 엔티티)**

<br>

### @JoinColumn

- 외래키를 관리할 필드를 매핑

```java

 @ManyToOne
 @JoinColumn(name = "TEAM_ID") // Team 엔티티에 기본키를 'team_id'로 매핑 (foreign key)
 private Team team;

```

<br>

### 다대일 [N:1]

외래키가 있는 쪽이 연관관계의 주인

#### 단방향

- 다 쪽에 @ManyToOne 사용

```java
// N
@ManyToOne
@JoinColumn(name = "TEAM_ID")
private Team team;

```

#### 양방향

- 일 쪽에 @OneToMany(mappedBy = "{주인 변수명}") 사용
- 보통 RDB의 외래키는 다 쪽에 있기 때문에 @OneToMany에만 mappedBy 속성이 있음

```java

// N
@ManyToOne
@JoinColumn(name = "TEAM_ID")
private Team team;

---------------------------------

// 1
@OneToMany(mappedBy = "team") // 연관관계의 주인이 N 쪽에 team 이라는 표시
private List<Member> members = new ArrayList<>();

```

<br>

### 일대다 [1:N]

실무에서는 잘 사용하지 않는 매핑  
객체의 입장에서는 일 쪽에서 연관관계의 주인이 되는 경우가 생길 수 있지만 RDB의 경우 항상 다 쪽에 외래키가 있다.  
가능한 일대다 보다는 다대일 양방향을 사용하는게 좋다.

#### 단방향

- 일 쪽에 @JoinColumn을 매핑
- 실제 DB에 외래키는 다 쪽에 있어서 반대편 외래키를 관리해야 한다.

```java
// 1
@OneToMany
@JoinColumn(name = "team_id")
private List<Member> members = new ArrayList<>();
```

#### 양방향

- 다 쪽에 insertable, updatable이 false인 @JoinColumn을 매핑
- 억지 매핑으로 사용하지 말자

```java
// 1
@OneToMany
@JoinColumn(name = "team_id")
private List<Member> members = new ArrayList<>();

---------------------------------------------------

// N
@ManyToOne
@JoinColumn(name = "team_id", insertable = false, updatable = false)
private Team team;
```

<br>

### 일대일 [1:1]

#### 단방향

- 외래키를 관리할 테이블에 @JoinColumn을 매핑

```java
@OneToOne
@JoinColumn(name = "delivery_id")
private Delivery delivery;
```

#### 양방향

- 외래키를 관리하지 않는 테이블에 mappedBy 사용
- 외래키가 없는 쪽은 지연 로딩으로 설정해도 항상 즉시 로딩 된다.

```java
@OneToOne
@JoinColumn(name = "delivery_id")
private Delivery delivery;

-------------------------------

@OneToOne(mappedBy = "delivery")
private Order order;
```

<br>

### 다대다 [N:M]

- @ManyToMany, @ManyToMany(mappedBy)를 사용
- 실무에서 쓰면 안된다.
- 다대다 보다는 일대다, 다대일로 풀어서 사용해야 한다.
- 관련해서 작성한 [블로그 게시글](https://zara9006.tistory.com/2)

<br> <br>

## 고급 매핑

### 상속관계 매핑

- 객체의 상속과 DB의 슈퍼타입 서브타입 관계를 매핑
- InheritanceType

  1.  JOINED

      - 장점
        - 테이블 정규화
        - 외래키 참조 무결성
        - 저장공간을 효율적으로 사용
      - 단점
        - 전체 조회 시 조인을 많이 사용한다. (성능이 저하될 수 있음)
        - INSERT SQL 2번 호출

      ```java
      // 부모
      @Inheritance(strategy = InheritanceType.JOINED)
      @DiscriminatorColumn // DTYPE 컬럼 (name 속성으로 변경 가능)
      public class Item {
      	...
      }

      -------------------------------------------------

      // 자식
      @Entity
      @DiscriminatorValue("B") // DTYPE 으로 'B' 저장 (기본값 : 엔티티 이름)
      public class Book extends Item { ... }

      @Entity
      public class Album extends Item { ... }

      @Entity
      public class Movie extends Item { ... }
      ```

  1.  SINGLE_TABLE
      - 장점
        - 단일 테이블이기 때문에 조회 성능이 빠름
      - 단점
        - 연관없는 컬럼들은 모두 null이 들어간다.
        - 테이블이 커질 수 있다. (조회 성능이 오히려 느려질 수 있음)
  1.  TABLE_PER_CLASS
      - 장점
        - not null 제약조건을 사용할 수 있음
      - 단점
        - 모든 자식 엔티티를 조회하면 성능이 느림 (UNION SQL 난발)

<br>

### MappedSuperclass

- 공통 매핑 정보
- 엔티티가 아니고, 단순히 공통되는 매핑 정보만 상속을 통해서 제공하는 역할

```java
@MappedSuperclass
public class BaseEntity {
	private String createdBy;
	private String lastModifiedBy;
	private LocalDateTime cratedDate;
	private LocalDateTime lastModifiedDate;

	...
}

------------------------------------------------

@Entity
public class Member extends BaseEntity { ... }

@Entity
public class Product extends BaseEntity { ... }
```

<br> <br>

## 값 타입

### 임베디드 타입

```java
@Embeddable
public class Address {
    private String address1;
	private String address2;
	private int zipcode;

	...
}

---------------------------------

@Embedded
private Address address;
```
