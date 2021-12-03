---
title: JPA 설정
date: "2021. 11. 17"
summary: JPA를 실제 사용하기 위해서 필요한 설정 정보 정리
---

### 필수 속성

#### DB 연결 정보

- javax.persistence.jdbc.driver
- javax.persistence.jdbc.user
- javax.persistence.jdbc.password
- javax.persistence.jdbc.url

<details><summary>Spring Boot</summary>
<ul>
	<li>spring.datasource.driver-class-name</li>
	<li>spring.datasource.username</li>
	<li>spring.datasource.password</li>
	<li>spring.datasource.url</li>
</ul>
</details>

#### 방언

- hibernate.dialect

<details><summary>Spring Boot</summary>
<ul>
	<li>spring.jpa.database-platform</li>
</ul>
</details>

<br>

### 옵션

- hibernate.show_sql
  - JPA가 작성한 SQL을 콘솔에 출력
- hibernate.format_sql
  - show_sql로 출력되는 SQL을 보기 편하도록 포맷팅
- hibernate.use_sql_comments
  - show_sql로 출력되는 SQL이 왜 작성 되었는지 확인할 수 있는 주석 출력
- hibernate.jdbc.batch_size
  - batch의 크기를 지정 (JDBC batch)
- hibernate.hbm2ddl.auto
  - create
    - 기존테이블 삭제 후 다시 생성 (DROP 후 CREATE)
    - 개발 초기 단계
  - create-drop
    - create와 같으나 종료시점에 테이블 DROP
    - 개발 초기 단계
  - update
    - 변경된 DDL만 반영
    - 개발 초기 단계 or 테스트 서버 (운영DB에서는 절대 사용 X)
  - validate
    - 엔티티와 테이블이 정상 매핑되었는지만 확인
    - 스테이징과 운영 서버
  - none
    - 스테이징과 운영 서버

<details><summary>Spring Boot</summary>
<ul>
	<li>spring.jpa.properties.hibernate.show_sql</li>
	<li>spring.jpa.properties.hibernate.format_sql</li>
	<li>spring.jpa.properties.hibernate.use_sql_comments</li>
	<li>spring.jpa.properties.hibernate.jdbc.batch_size</li>
	<li>spring.jpa.hibernate.ddl-auto</li>
</ul>
</details>
