

T academy

김영한 우아한 형제들

### JPA 실습준비

* STS IDE 설치, Spring Boot 로 데모
* H2 데이터베이스 설치



###### dependancy

- maven
- jpa
- h2



### JPA와 모던 자바 데이터 저장 기술

1. SQL 중심적인 개발의 문제점

   - CRUD; 자바 객체를 SQL로 SQL을 자바객체로 ...
   - 엔티티 신뢰 문제, 컬럼 변경 그리고 상호 참조 등등
   - 계층형 아키텍처, 진정한 의미의 계층 분할이 어렵다.
   - SQL 의존적이고, 패러다임의 불일치 (객체 vs 관계형데이터베이스)
     - 상속
     - 연관관계
     - 데이터타입
     - 데이터 식별 방법
   - 개발자는 SQL 매퍼의 일을 많이 한다.

2. JPA 소개

   - 자바 진영의 ORM 기술 표준 (Java Persistence API)

     JPA는 어플리케이션과 JDBC 사이에서 동작

     ![image-20220927171112358](JPA프로그램기본다지기-3강.assets/image-20220927171112358.png)

     조회

     ![image-20220927171151651](JPA프로그램기본다지기-3강.assets/image-20220927171151651.png)

   - JPA는 인터페이스의 모음이고 JPA2.1 표준명세를 구현한 3가지 구현체

   - 하이버네이트, EclipseLink, DataNucleus

   - 생산성 

     - 저장: jpa.persist(member)
     - 조회: Member m = jpa.find(memberId)
     - 수정: m.setName("변경할 이름")
     - 삭제: jpa.remove(member);

3. Spring Data JPA

4. QueryDSL

5. 실무경험 공유



### JPA 기초와 매핑

#### 실습

- persistence.xml
  - JPA  설정
  - /MEMA-INF/persistence.xml 위치
  - `javax.persistence` 로 시작하는 것은 JPA 표준 속성을
  - `hibernate`로 시작하는 것은 Hibernate 전용 속성이다.

>
>
>* 방언(Dialect)는 JPA가 특정 데이터베이스에 종속적이지 않도록 특정 데이터베이스 고유 기능을 구현해 놓은 것 (대략 45가지 방언 지원)
>  * H2: org.hibernate.dialect.H2Dialect
>  * MySql: org.hibernate.dialect.MySQL5InnoDBDialect
>  * Oracle 10g: org.hivernate.dialect.Oracle10gDialect



##### 어플리케이션 개발

1. 엔티티 매니저 팩토리 설정
2. 엔티티 매니저 설정
3. 트랜잭션
4. 비즈니스 로직 (CRUD)
5. 리소스 정리 

##### DB  테이블 생성

```sql
create table Member (
    id bigint not null,
    name varchar(255),
    primary key(id) 
);

drop table Member;
```



##### maven 단순 프로젝트로 생성

- Group Id: hellojpa
- Artifact Id: hellojpa
- dependancy 추가 (POM.xml)
  - 위에서 했던 
  - jpa, h2 추가
    - org.hibernate.hibernate-entitymanager@5.3.7.Final
    - com.h2database.h2@1.4.197

> 소스 - hellojpa.Main.java
>
> ```java
> package hellojpa; 
> public class Main {
> public static void Main(String args[]) {
>   System.out.println("hello");
> }
> }
> ```
>
> 소스 - hellojpa.entity.Memgber
>
> ```java
> package hellojpa.entity
> @Entity
> public class Member {
> @Id
> private Long id;
> private String name;
> 
> // getter/setter
> }
> ```
>
> 리소스 - META-INF/persistence.xml
>
> ```xml
> <?xml version="1.0" encoding="UTF-8" ?>
> <persistence xmlns="http://xmlns.jcp.org/xml/ns/persistence" version="2.2">
> <persistence-unit name="hello">
>   <properties>
>       <!-- 필수 속성 -->
>       <property name="javax.persistence.jdbc.driver" value="org.h2.Driver" />
>       <property name="javax.persistence.jdbc.user" value="sa" />
>       <property name="javax.persistence.jdbc.password" value="" />
>       <property name="javax.persistence.jdbc.url" value="jdbc:h2:tcp://localhost/~/test" />
>       <property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect" />
> 
>       <!-- 선택 속성 -->
>       <property name="hibernate.show_sql" value="true" />
>       <property name="hibernate.format_sql" value="true" />
>       <property name="hibernate.use_sql_comments" value="true" />
>       <property name="hibernate.id.new_generator_mappings" value="true" />
> 
>       <property name="hibernate.hbm2ddl.auto" value="create" />
> 
>   </properties>
> </persistence-unit>
> </persistence>
> ```
>
> 소스 - Main
>
> ```java
> public class Main {
> 	public static void main(String args[]) {
> 		EntityManagerFactory emf = Persistence.createEntityManagerFactory (persistenceUnitName: "hello");
> 		EntityManager em = emf.createEntityManager();
> 		EntityTransaction tx = em.getTransaction();
> 		tx.begin();
> 		
> 		try {
>       //팀 저장
>       Team team = new Team();
>       team.setName("TeamA");
>       em.persist(team);
> 
>       //회원 저장
>       Member member = new Member();
>       member.setName("member1");
>       member.setTeamId(team.getId());
>       em.persist(member);
>       
>       tx.commit();
>       // 조회
> 		} catch(){
> 			tx.rollback();
> 		} finally {
> 			em.close();
> 		}
> 		emf.close();
> 	}
> }
> ```
>
> 



### 필드와 컬럼 매핑

#### JPA 기본 기능 - 데이터베이스 스키마 자동 생성하기

- DDL을 애플리케이션 실행 시점에 자동 생성
- 이렇게 생성된 DDL은 `개발 장비에서만` 사용하거나 적절히 다듬은 후에 운영서버에서 사용
- 데이터베이스 Dialect을 활용해서 데이터베이스에 맞는 적절한 DDL 생성
- hibernate.hbm2ddl.auto
  - create: 기존 테이블 삭제 후 다시 생성 (DROP+ CREATE)
  - create-drop: create와 같은나 종료시점에 테이블을 DROP
  - update: 변경분만 반영 (운영DB에는 사용하면 안됨)
  - validate: 엔티티와 테이블이 정상 매핑되었는지만 확인
  - none: 사용하지 않음
- 주의
  - 운영장비에서는 절대 create, create-drop, update를 사용하면 안된다
  - 개발 초기 단계는 create 또는 update
  - 테스트 서버는 update 또는 validate
  - 스테이징과 운영 서버는 validate 또는 none



#### 실습2

> 목표
>
> - 스키마 자동 생성하기 설정
> - 스키마 자동생성하기 실행, 옵션별 확인



##### 스키마 자동 생성하기 설정 

> 소스 - persistence.xml
>
> ```xml
> ...
> <persistence-unit name="hello">
>  <properties>
>      <!-- 필수 속성 -->
>      <property name="javax.persistence.jdbc.driver" value="org.h2.Driver" />
>      <property name="javax.persistence.jdbc.user" value="sa" />
>      <property name="javax.persistence.jdbc.password" value="" />
>      <property name="javax.persistence.jdbc.url" value="jdbc:h2:tcp://localhost/~/test" />
>      <property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect" />
> 
>      <!-- 선택 속성 -->
>      <property name="hibernate.show_sql" value="true" />
>      <property name="hibernate.format_sql" value="true" />
>      <property name="hibernate.use_sql_comments" value="true" />
>      <property name="hibernate.id.new_generator_mappings" value="true" />
>      
>      <property name="hibernate.hbm2ddl.auto" value="create" />
>  
>  </properties>
> </persistence-unit>
> ```
>
> 



> 소스 - Member.java
>
> ```java
> 
> @Entity
> public class Member {
>  @Id
>  private Long id;
>  
>  private String name:
>  private int age;
>  
>  public Long getId() { return id; }
>  public void setId(Long id) { this.id = id; }
>  public String getName() { return name; }
>  public void setName(String name) { this.name = name; }
>  public int getAge() { return age; }
>  public void setAge(int age) { this.age = age; }
> }
> ```
>
> * age 필드가 추가되면, log에서 확인해 보면 테이블을 DROP 후 CREATE 하는 것을 확인할 수 있다.



##### 데이터베이스 매핑 어노테이션

* Column: 객체 멤버변수와 테이블의 컬럼명이 다른 경우. ex @Column (name = "username")
* Temporal: 
* Enumerated
* Lob
* Transient: 이 필드는 매핑하지 않는다. 즉 DB에 저장하지 않는 필드이다.

> 꼭 하나씩 해보기



> 소스 (MemberType.java)
>
> ```java
> package hellojpa.entity;
> 
> public enum MemberType {
>  USER, ADMIN
> }
> ```
>
> 
>
> 소스 - Member.java 변경
>
> ```java
> @Entity
> public class Member {
>  ...
>  @Enumerated(EnumType.STRING)
>  private MemberType memberType;
>  ...
> }
> 
> ```
>
> * 만일, EnumType.ORDINAL로 실행하면 숫자로 들어간다.



###### @Column

- name: 필드와 매핑할 테이블 컬럼 이름
- insertable, updatable: 읽기 전용
- length: 필드의 크기
- nullable: null 허용여부 결정, DDL  생성시 사용
- nuique: 유니크 제약 조건, DDL 생성시 사용
- columnDefinition, length, precision, scale 등, DDL 생성시 사용



###### @Lob

- CLOB: String, char[], java.sql.CLOB
- BLOB: byte[], java.sql.BLOB



##### 식별자 매핑

- @Id(직접 매핑)

  - IDENTITY: 데이터베이스에 위침, MYSQL
  - SEQUENCE: 데이터베이스 시퀀스 오프젝트 사용, ORACLE
    - @SequenceGenerator 필요
  - TABLE: 키 생성용 테이블 사용, 모든 DB에서 사용
    - @TableGenerator 필요
  - AUTO: 방언(Dialect)에 따라 자동 지정, 기본 값

  > @Id @GeneratedValue (strategy = GenerationType.AUTO|TABLE|SEQUENCE|IDENTITY)
  >
  > private Long id;

- 권장하는 식별자 전략

  - 기본 키 제약 조건: Null 아님, 유일해야 하고, 변하면 안된다.
  - 미래까지 이 조건을 만족하는 자연키는 찾기 어렵다. **대리키(대체키)를 사용하자**.
    ex. 주민번호를 사용했는데, 개인정보보호법에 의해 사용하지 마라!!!
  - 권장: **Long + 대체키 + 키 생성전략** 사용, 비즈니스 키를 사용하지 않는다



### 연관관계 매핑

#### exam3

JPA의 포인터 !!!

`객체지향 설계의 목표는 자율적인 객체들의 협력공동체를 만드는 것이다.`

- 연관관계가 없는 객체
- 객체를 테이블에 맞추어 모델링 (참조 객체 대신에 외래키를 값으로 그대로 사용함)

> 소스 - Member
>
> ```java
> @Entity
> public class Member {
> @Id @GeneratedValue
> private Long id;
> 
> @Column (name="USERNAME")
> private String name;
> private int age;
> 
> @Column (name="TEAM_ID")
> private Long teamId;
> ...
> }
> 
> ```
>
> 
>
> 소스 - Team
>
> ```java
> @Entity
> public class Team {
> @Id @GeneratedValue
> private Long id;
> 
> private String name;
> ...
> }
> 
> ```
>
> 
>
> 소스 - Main
>
> ```
> public class Main {
> 	public static void main(String args[]) {
> 		EntityManagerFactory emf = Persistence.createEntityManagerFactory (persistenceUnitName: "hello");
> 		EntityManager em = emf.createEntityManager();
> 		EntityTransaction tx = em.getTransaction();
> 		tx.begin();
> 		
> 		try {
>       //팀 저장
>       Team team = new Team();
>       team.setName("TeamA");
>       em.persist(team);
> 
>       //회원 저장
>       Member member = new Member();
>       member.setName("member1");
>       member.setTeamId(team.getId());
>       em.persist(member);
>       
>       tx.commit();
>       // 조회
> 		} catch(){
> 			tx.rollback();
> 		} finally {
> 			em.close();
> 		}
> 		emf.close();
> 	}
> }
> 
> ```
>
> * 그러면 조회할 때,  **식별자로 다시 조회하는 것은 객체 지향적인 방법이 아니다.** 즉, 데이터지향적이다.
>
> ```java
> // 조회
> Member findMember = em.find(Member.class, member.getId());
> 
> // 연관관계가 없음
> Team findTeam = em.find(Team.class, team.getId());
> 
> 
> ```
>
> <span style='color:blue;'>객체를 테이블에 맞추어 데이터 중심으로 프로그램하면 협력관계를 만들 수 없다.</span>
>
> * 테이블은 외래 키로 조인을 사용해서 연관 테이블을 찾을 수 있는데
> * 객체는 참조를 사용해서 연관된 객체를 찾는다.
> * 이 문제를 해결하는 것이 핵심!!!



#### 단방향 매핑 (exam4)

###### Member >>>>>> [0..1]Team 에서의 객체 연관관계의 경우, 연관관계 매핑을 통해

- Member는 Team에 접근 가능하지만
- Team은 Member에 접근할 수 없다.

> 소스 Member
>
> * @ManyToOne(fetch = FetchType.LAZY)로 옵션을 선택하면 한방 Query가 아닌, 필요한 시점에서 Qeury함 (한방은 FetchType.EAGER )
>
> ```java
> @Entity
> public class Member {
>  @Id @GeneratedValue
>  private Long id;
>  
>  @Column (name="USERNAME")
>  private String name;
>  private int age;
>  
> //    @Column (name="TEAM_ID")
> //    private Long teamId;
>  
>  @ManyToOne
>  @JoinColumn (name = "TEAM_ID")
>  private Team team;
>  ...
> }
> 
> ```
>
> 
>
> 소스 - Main
>
> * 단방향 연관관계 설정 후에는 참조 객체를 SET하면 DB에 해당 ID가 저장
>
> ```java
> public class Main {
> 	public static void main(String args[]) {
>      ...
> 			//회원 저장
>          Member member = new Member();
>          member.setName("member1");
>          //member.setTeamId(team.getId());
>          member.setTeam(team);
>          em.persist(member);
>      ...
>  }
> }
> 
> ```
>
> * 이제는 조회할 때,
>
> ```java
> // 조회
> Member findMember = em.find(Member.class, member.getId());
> 
> // 연관관계가 없음
> // Team findTeam = em.find(Team.class, team.getId());
> // 참조를 사용해서 연관관계 조회
> Team findTeam = em.getTeam();
> 
> 
> ```
>
> 



#### 양방향 매핑 (exam5)

Member [*]<<<>>> [0..1]Team 에서의 객체 연관관계의 경우, 연관관계 매핑을 통해

- Member는 Team에 접근 가능하고
- Team도 Member에 접근할 수 있다. 즉, Team 엔티티에  Member를 담는 컬렉션을 추가한다.

> 소스 Member
>
> (그대로 유지)
>
> 
>
> 소스 Team
>
> ```java
> @Entity
> public class Team {
>  @Id @GeneratedValue
>  private Long id;
>  
>  private String name;
>  
>  @OneToMany (mappedBy = "team")
>  List<Member> mebers = new ArrayList<Member>();
>  ...
> }
> 
> ```
>
> * 이제는 조회할 때,
>
> ```java
> // 조회
> Member findMember = em.find(Member.class, member.getId());
> 
> // 연관관계가 없음
> // Team findTeam = em.find(Team.class, team.getId());
> // 참조를 사용해서 연관관계 조회
> Team findTeam = em.getTeam();
> 
> List<Member> members = findTeam.getMembers();
> for(Member m : members) {
>  System.out.println("member = " + m );
> }
> 
> 
> ```



<span style="color:blue;font-size:1.2em;">반대 방향으로 객체 그래프 탐색</span>

// 역방향 조회

##### 연관관계의 주인과 mappedBy

- mappedBy = JPA의 맨붕 클래스
- mappedBy는 처음에는 이해하기 어렵다.
- 객체와 테이블 간의 연관관계를 맺는 차이를 이해한다.
  - 객체 연관관계
    - 회원 -> 팀; 연관관계 1개, 단방향
    - 팀 -> 회원; 연관관계 1개, 단방향
  - 테이블 연관관계
    - 회원 <--> 팀; 연관관계 1개, 양방향
- 객체의 연관관계는 서로 다른 객체 인스턴스가 2개 존재하는 것이다.
- 따라서 연관관계의 주인(Owner) 개념이 등장



##### 양방향 매핑 규칙 (mappedBy로 관리해 줄 )

- 객체의 두 관계 중 하나를 연관관계의 주인으로 지정
- 연관관계의 주인만이 외래 키를 관리 (등록, 수정)
- 주인이 아닌쪽은 읽기만 가능
- 주인은 mappedBy 속성 사용 불가
- 주인이 아니면 <span style="color:blue;">`mappedBy ="team" `</span>속성으로 주인 지정
- <span style="color:red;">쉬운 말로 변경 ...</span> 인지 부조화 ...
  - DB의 외래키가 있는 곳을 주인(Owner)으로 하라.
  - <span style="color:blue;">진짜 매핑</span> 여기에서는 Member.team이 연관관계의 주인
  - <span style="color:blue;">가짜 매핑</span> 주인의 반대편 (Team.members)

> 소스 - Main
>
> 1. ㅇㅇ
> 2. 객체 입장에서는 양쪽에 모두 set해야 맞다.
>
> ```java
> public class Main {
> 	public static void main(String args[]) {
>      ...
> 			Team team = new Team();
>          team.setName("Team A");
>      	em.persist(team);
>      
>      	Member member = new Member();
>      	member.setName("member1");
>      
>      	team.getMembers().add(member); // 무시됨. >> 꼭 확인해야 할 것
>      	member.setTeam(team);
>      
>      	em.persist(member);
>      ...
>  }
> }
> 
> 
> ```
>
> 



#### 양방향 매핑 (exam5)

###### 연관관계 매핑 어노테이션

- 다대일(@ManyToOne)
- 일대다(@OneToMany)
- 일대일(@OneToOne)
- 다대다(@ManyToMany)
- @JoinColumn, @JoinTable



###### 상속관계 매핑 어노테이션

- @Inheritance
- @DiscriminatorColumn
- @DiscriminatorValue
- @MappedSuperclass (매핑 속성만 상속)



###### 복합키 어노테이션

- @IdClass
- @EmbeddedId
- @Embeddable
- @MapsId
- 
