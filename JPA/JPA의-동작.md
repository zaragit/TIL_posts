---
title: JPA의 구성과 동작 원리
date: "2021. 11. 23"
summary: JPA의 구성과 동작에 대해서 학습한 내용
---

## Persistence

![Persistence](/images/JPA/persistence_cp.png)

JPA를 사용하게 되면 Persistence라는 단어를 많이 접하게 된다.  
Persistence는 없어지지 않고 지속된다는 뜻이다.  
JPA는 엔티티 객체의 인스턴스를 없어지지 않고 지속되게 해준다.  
보통 지속이라는 표현보다 영구적으로 엔티티를 저장한다는 의미에서 영속성이라고 한다.

### Persistence Unit

![Persistence](/images/JPA/persistence_unit.png)

JPA를 사용하려면 /resources/META-INF/persistence.xml 설정 파일을 작성해야 한다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<persistence version="2.2"
 			xmlns="http://xmlns.jcp.org/xml/ns/persistence"
			xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 			xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence http://xmlns.jcp.org/xml/ns/persistence/persistence_2_2.xsd">

	<persistence-unit name="DB1">
		<properties>
			<property name="javax.persistence.jdbc.driver" value="org.h2.Driver"/>
			<property name="javax.persistence.jdbc.user" value="sa"/>
			<property name="javax.persistence.jdbc.password" value=""/>
			<property name="javax.persistence.jdbc.url" value="jdbc:h2:tcp://localhost/~/test"/>
			<property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect"/>

			<!-- 기타 JPA 옵션(생략) -->
		</properties>
	</persistence-unit>

	<persistence-unit name="DB2">
		<!-- 생략 -->
	</persistence-unit>
</persistence>
```

persistence 안에 persistence-unit을 작성하는데 보통 persistence-unit은 DB마다 하나씩 정의한다.  
때문에 DB 접속정보(DataSource) property는 각각의 unit마다 필수로 작성해 줘야 한다.

<br>

## EntityManagerFactory

![EntityManagerFactory](/images/JPA/entitymanagerfactory.png)

실제로 코드에서 JPA를 사용하기 위해서는 EntityManager가 필요한데 EntityManager는 EntityManagerFactory를 통해서 제공받을 수 있다.

EntityManagerFactory를 만들 때 Persistence Unit이 필요하다.

```java
EntityManagerFactory emf = Persistence.createEntityManagerFactory("DB1");
EntityManager em = emf.createEntityManager();
```

Persistence 객체로 부터 EntityManagerFactory를 만드는데 파라미터로 persistence-unit의 name 값을 받는다.

> Spring Boot를 사용하는 경우 'spring-boot-starter', 'spring-boot-starter-data-jpa' 의존성만 추가해 주면 EntityManagerFactory를 빈으로 등록해 준다.

<br>

## EntityManager

JPA가 RDB 테이블에 저장된 데이터를 자바의 객체로 매핑하고 관리할 객체를 Entity라고 하고, 이런 Entity 객체의 인스턴스를 관리해 주는 관리자 역할을 하는 게 EntityManager이다.

#### Persistence Context

영속성 컨텍스트라고 부른며 논리적인 개념이다.  
JPA를 사용하면서 직접적으로 접근해서 다룰 수 있는 대상이 아니다. EntityManager를 통해서만 접근할 수 있다.

영속성 컨텍스트는 JPA가 엔티티 인스턴스를 영속(저장)하고 관리하는 환경이다.
실제로 JPA를 통해서 엔티티를 저장할 때 'save'와 같은 메소드가 아닌 'persist'라는 메소드를 호출한다. 엔티티를 DB에 바로 저장하지 않고 영속성 컨텍스트에 저장하는 것이기 때문이다.

##### 1. 1차 캐싱

엔티티 인스턴스를 영속하고 있기 때문에 캐싱의 효과가 있다.  
이미 조회한 엔티티는 컨텍스트에 영속되며, 동일한 엔티티를 조회하면 영속된 엔티티를 반환하기 때문에 DB에 SQL을 보내지 않아도 된다.

##### 2. 동일성 보장

동일한 엔티티를 조회했을 때 영속되어 있는 인스턴스를 반환하기 때문에 자바의 == 비교도 True값이 나온다. 동일한 데이터를 조회했을 때 동일한 인스턴스가 반환되기 때문에 객체지향적으로 데이터를 다룰 수 있다는 장점이 있다.

```Java
Member findMember1 = em.find(Member.class, 1L);
Member findMember2 = em.find(Member.class, 1L);

System.out.println(findMember1 == findMember2); // true
```

##### 3. 쓰기 지연

EntityManager를 통한 작업은 모두 트랜잭션 안에서 실행되도록 설계되어 있다.  
따라서 JPA의 모든 데이터 변경을 트랜잭션 안에서 실행되게 된다.

엔티티의 변경사항은 1차적으로 영속상태로 영속성 컨텍스트에 저장되고, 트랜잭션이 commit 되는 순간 DB에 반영한다.

###### 실제 DB에 값이 반영되는 시점

1. 트랜잭션 커밋 (내부적으로 flush를 호출한다.)
2. entityManager.flush()
3. JPQL 쿼리 실행

##### 4. 변경 감지

영속성 컨텍스트에 영속된 엔티티는 스냅샷을 가진다.  
처음 영속상태가 됐을 때 엔티티가 DB에 저장되어 있는 상태와 같다고 보고 동일한 상태의 복사본을 저장해 둔다.

DB에 값을 반영하기 전에 현재 엔티티와 복사본을 비교해서 변경된 부분도 적절한 INSERT와 UPDATE 쿼리를 통해서 변경해 준다.
