

# 토크온세미나



## JPA 프로그램 기본 다지기 - T아카데미

출처: https://www.youtube.com/watch?v=WfrSN9Z7MiA

김영한, 자바 ORM 표준 JPA 프로그래밍 저자, 우아한 형제들

## 1강 - JPA 소개



>  JPA 실습 준비
>
> - IDE 툴 - STS 또는  IntelliJ
> - H2 데이터베이스
>
> 
>
> JPA 디폴트 프로젝트 생성
>
> * Spring boot 보일러 플레이트
> * maven
> * starter
>   * h2
>   * jpa
>
> 
>
> Run spring Application



### 왜 JPA를 써야 하는가?



#### JPA와 모던 자바 데이터 저장 기술

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
   - JPA는 어플리케이션과 JDBC 사이에서 동작
     - Java 어플리케이션 >> JPA >> JDBC API :     (SQL)    : DB
   - JPA는 인터페이스의 모음이고 JPA2.1 표준명세를 구현한 3가지 구현체
   - **하이버네이트**, EclipseLink, DataNucleus
   - 생산성 

     - 저장: jpa.persist(member)
     - 조회: Member m = jpa.find(memberId)
     - 수정: m.setName("변경할 이름")
     - 삭제: jpa.remove(member);
   - `Spring  Data JPA`는 `Hibernate`를 좀 더 편하게 사용할 수 있도록
   - `QueryDSL`은 문자 리터럴로 작성된 정적인 SQL 문장을 Java 동적인 자바 객체로 접근해 IDE 및 컴파일 타임에 문제를 캐치하고 해결할 수 있도록 하는 프레임워크



#### JPA 개념들...



## 2강 - JPA 실습



#### 객체 매핑

1. 



#### 밸류 매핑

1. 





## 3강 - 필드와 컬럼 매핑



#### Column



#### Temporal



#### Enumerated



#### Lob



#### Transient



## 4강 - 연관관계 매핑



#### 단방향 연관 매핑 - OneToMany, ManyToOne

1. 



## 5강 - 양방향 매핑



#### 양방향 연관 매핑

1. 





## 6강 - JPA 내부구조



#### 영속성 컨텍스트

`EntityManagerFactory`는 `EntityManager`를 생성하는 팩토리이고 `EntityManager`는 내부적으로 커넥션 풀에서 하나의 `Connection`을 선택해 사용하게 된다.

> 영속성 컨텍스트란?
>
> * "엔티티를 영구 저장하기 위한 환경"
> * EntityManager.persist(entity)라는 메소드를 통해 `PersistenceContext`에 접근해서 저장할 엔티티들을 데이터베이스에 저장함.
> * `J2SE 환경`에서는 EnityManager와 PersistenceContext는 1:1 관계
> * `J2EE 또는 Spring framework 환경`에서는 EnityManager와 PersistenceContext는 N:1 관계 (같은 Transaction으로 묶으면)

#### 엔티티의 생명주기

- 비영속 (new/transient) - 영속성 컨텍스트와 전혀 관계가 없는 상태

  > 객체를 생성했지만 영속성 컨텍스트에 포함되지 않은 상태
  >
  > ```java
  > Member member = new Member();
  > member.setId("1");
  > member.setName("홍길동");
  > ```

- 영속 (managed) -  영속성 컨텍스트에 저장된 상태

  > 영속성 컨텍스트에 객체를 저장한 상태
  >
  > ```java
  > EntityManager em = emf.createEntityManager();
  > em.getTransaction().begin();
  > 
  > em.persist(member);
  > ```

- 준영속 (detached) - 영속성 컨텍스트에 저장되었다가 분리된 상태

  > 영속성 컨텍스트에서 객체를 분리한 상태
  >
  > ```java
  > em.detach(member);
  > // 또는
  > em.clear();
  > // 또는
  > em.close();
  > ```

- 삭제 (removed) - 삭제된 상태

  > 영속성 컨텍스트에서 객체를 삭제한 상태
  >
  > ```java
  > em.remove(member);
  > ```

#### 영속성 컨텍스트의 이점

- 1차 캐시

  > 캐시 구조로 TX 가 유지되는 시간에만 존재함
  >
  > * key/value(Entity, Snapshot) = @Id/member
  > * member1/memberEntity1
  >
  > member2를 find하면 없기때문에 DB에서 가져와서 member2/memberEntity2를 캐시함

- 동일성(identity) 보장 - 1차 캐시 구조로 인해 `equals()`가 가능함

- 트랜잭션을 지원하는 쓰기 지연 (Transactional write-behind)

  > 트랜잭션이 시작해서 커밋하는 시점에 DB에 반영 (옵션 설정이 필요함)
  >
  > ```java
  > transaction.begin();
  > 
  > em.persist(member1);	// --(1) 내부적으로 1차 캐시와 SQL저장소에 생성해 가지고 있다.
  > em.persist(member2);	// --(2) flush() 메소드를 만나거나 commit() 메소드를 만나면 반영
  > 						// --(_) 필요에 따라 컨텍스트를 clear()로 비운다
  > transaction.commit();	// --(3) 옵션을 통해 batch 작업이 가능하다
  > ```

- 변경 감지 (Dirty Checking)

  > 변경된 Entity 객체에 대한 update메소드와 같은게 있어야 되지 않을까? 없어도 OK!
  >
  > ```java
  > tx.begin();
  > 
  > Member member1 = em.find(Member.class, "member1");	// --(1) Entity를 1차 캐시에
  > 
  > member1.setName("최길동");	
  > member1.setAge(500);								// --(2) 1차 캐시에 snapshot을
  > 
  > tx.commit();										// --(3) 자동으로 Update를 수행함
  > ```
  >
  > **영속성 컨텍스트를 플러시 하는 방법**
  >
  > * 쓰기 지연 SQL 저장소의 쿼리를 데이터베이스에 전송 (CRUD)
  >
  > * 수동 호출 - em.flush() 
  >
  > * 자동 호출 
  >
  >   * 트랜잭션 커밋
  >   * JPQL 쿼리 실행
  >
  > * 자동 호출 사례
  >
  >   ```java
  >   em.persist(member1);
  >   em.persist(member2);
  >   em.persist(member3);
  >   
  >   // 중간에 JPQL을 실행하면 ... 실수를 막고자 자동으로 플러시 ...???? 확인해봐야
  >   query = em.createQuery("select m from Member m", Member.class);
  >   List<Member> mebers = query.getResultList();
  >   ```
  >
  > * 

- 지연 로딩 (Lazy loading)

  > **다대일 관계 (Member:Team)에서** 
  >
  > - Member를 find하는데 Team 쿼리를 할 필요가 있을까?
  >
  > - 이때, `FetchType.LAZY` 를 사용해서 `프록시 객체(가짜 클래스)`로 조회
  >
  >   ```java
  >   @Entity
  >   public class Member {
  >       @Id
  >       private Long id;
  >       
  >       private String name;
  >       
  >       @ManyToOne (fetch = FetchType.LAZY)
  >       @JoinColumn (name = "TEAM_ID")
  >       private Team team;
  >   }
  >   ```
  >
  >   team 객체를 접근할 때, Query를 한다. 하지만 프록시 객체는 반드시 `managed`상태에서 동작되어야 함.
  >
  > **주의 사항**
  >
  > - 가급적 지연 로딩을 사용하고
  > - 즉시 로딩을 적용할 경우, Chainning 된 객체 참조가 있을 경우 SQL 문제가 발생할 수도 있어
  > - 즉시 로딩은 JPQL에서 N+1 문제를 발생
  > - @ManyToOne, @OneToOne은 기본이 즉시 로딩으로 설정되어 있으니 LAZY로 설정을 변경해야
  > - @OneToMany, @ManyToMany은 기본이 지연 로딩으로 설정되어 있음



## 7강 - JPA와 개체지향쿼리

여기부터는 실용편 활용편이다.

#### 양방향 연관 매핑

1. 



## 마치며 Spring Data JPA와 QueryDSL 이해



#### 양방향 연관 매핑

1. 

