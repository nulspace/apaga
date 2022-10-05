[TOC]

### CASE #1 @OneToMay 관계를 매핑하는 최상의 방법

출처: [Vlad mihaclea](https://vladmihalcea.com/the-best-way-to-map-a-onetomany-association-with-jpa-and-hibernate/)


#### 개요

기본적으로 JPA는 RDBMS를 쉽게 접근하기 위한 해법으로 무엇이 필요했을지를 생각해 보면 JPA를 어떻게 활용할지를 쉽게 접근할 수 있다. 

> (접근 방법)
>
> - RDBMS는 부모테이블과 자식테이블 관계에서 자식테이블이 FK를 가지고 있고, 이 연관관계를 가지고 양방향으로 데이터를 탐색할 수 있다.
> - 하지만 Object Mapping에서는 단방향으로 (1) 부모 엔터티 객체에서 자식 엔티티 객체를 속성으로 갖는다. 물론 (2) 자식엔티티 객체에서 부모 엔터티 객체를 단방향으로 갖을 수도 있다.
> - (3) 부모, 자식 객체에서 양방향 상호 참조에 따른 편리성은 있지만, 많은 자식데이터가 존재할 경우 어떻게 관리하는 것이 좋을 것인지 등의 이슈는 

이러한 세가지의 접근 방법에 따른 JPA 사용법을 확인해 보자.



#### 단방향 연관

##### 일대다(OneToMany) - 예시 `POST`:`COMMENT` 

* 기본적으로 단방향 `@OneToMany`<span style="color:blue">연관**만**</span>을 기술해 놓으면 데이터베이스 관점에서 보면 `다대다 관계`로 풀어 놓는다. 아마도 Many에는 어떠한 연관과 관련한 어노테이션을 사용하지 않기 때문이지 않을까 싶다.

* 엔터티 객체들

  * Post 

    * 이를 데이터베이스 관점에서 `일대다`로 구현하려면 `@JoinColumn`을 정의해야 한다.

    * 이  `@JoinColumn`어노테이션은 COMMENT 테이블의 FK를 정의한다.

      ```java
      public class Post {
          @Id
          @GeneratedValue
          private Long id;
      
          @NonNull
          private String title;
      
          @OneToMany(cascade = CascadeType.ALL, orphanRemoval = true)
          @JoinColumn(name = "post_id")  
          private List<Comment> comments = new ArrayList<Comment>();
          
          // Constructors, getters and setters removed for brevity
      }
      ```

  * Comment

    * 여기에 사용된 `@NonNull`은 lombok가 생성자로 사용하도록 `@RequiredArgsConstructor`와 함께 사용한다.

      ```java
      public class Comment {
          @Id
          @GeneratedValue
          private Long id;
      
          @NonNull
          private String review;
          // Constructors, getters and setters removed for brevity    
      }    
      ```

* 데이터베이스 테이블들

  * POST
  * COMMENT

* App - Hibernate 로그들

  * insert 하고나서 update
  * update 하고나서 delete



##### 다대일(ManyToOne) - 예시 `Contact`:`Person` 

* 기본적으로 단방향 `@ManyToOne`<span style="color:red">연관**만**</span> 기술해 놓아도<u>`다대다`로 풀지 않는다</u>. 

* 이 `@ManyToOne` 어노테이션 만으로도 이미 유일한 테이블 정보 Person과 Person의 ID를 알고 있게 된다.

* 엔터티 객체들

  * Contact

    * 속도 향상을 위해 ManyToOne 속성으로 (fetch = FetchType.LAZY)을 정의한다.

    * (1)은 생략 가능하고 name속성으로 다른 FK 컬럼 이름로 정의할 수 있다.

      ```java
      @Entity
      public class Contact {
          @Id @GeneratedValue
          private Long id;
      
          @NonNull
          private String description;
      
          @NonNull
          @ManyToOne
          @JoinColumn (name="person_id")   //----- (1)
          private Person person;
      
      }
      ```

  * Person

    * 부모 테이블에는 `@Entity` 어노테이션만을 명기하면 끝.

      ```java	
      @Entity
      public class Person {
      
          @Id
          @GeneratedValue
          private Long id;
      
          @NonNull
          private String name;
      
      }
      
      ```

  * App

    * 이를 활용한 어플리케이션을 
      * 데이터 생성
      * 데이터 수정
      * 데이터 삭제

#### 양방향 연관

##### 일대다(OneToMany) - 예시 `Team`:`Member`

- 일대다 관계에서 FK를 포함한 엔티티 상태의 변화를 어떻게 매핑하는 것이 가장 좋을까?

- 양방향 일대다에서 mappedBy를 생략하면

- 엔터티 객체들

  * Team

    * 어노테이션 `@OneToMany`에서 사용된 연관 속성 mappedBy를 사용할 경우  `@JoinTable` 또는 `@JoinColumn`은 정의해서는 안된다. 하지만 mappedBy를 생략하면 TABLE이 `다대다`매핑된다.

    * 또한 데이터베이스와 엔터티 객체간의 동기화를 위해서 별도로 Member 객체 관리를 위한 add/remove 메소드를 구현하라.

      ```java
      @Entity
      public class Team {
          @Id
          @GeneratedValue
          private Long id;
          private String name;
      
          @OneToMany(
              mappedBy = "team", 
              cascade = CascadeType.ALL, // ---> (1)
              orphanRemoval = true) 
          private List<Member> members = new ArrayList<Member>();
      
          public void addMember(Member member) {
              members.add(member);
              member.setTeam(this);
          }
      
          public void removeMember(Member member) {
              members.remove(member);
              member.setTeam(null);
      }
      }
      ```

    ```
      
    
    ```

* Member

  * 부모 엔티티 객체에서 구현된 add/remove 메소드를 위해 equals메소드와 hashCode메소드를 재정의 하라.

    ```java
    @Entity
    public class Member {
        @Id
        @GeneratedValue
        private Long id;
        private String name;
        private int age;
    
        // 양방향 연관에서 사용하도록
        @ManyToOne(fetch = FetchType.LAZY)
        private Team team;
    
        // 컬렉션을 포함할 부모 엔티티 객체에서 구분할 수 있는 식별자로
        @Override
        public boolean equals(Object o) {
            if( this == o) return true;
            if( !(o instanceof Member)) return false;
    
            return id != null && id.equals(((Member)o).getId());
        }
    
        @Override
        public int hashCode() {
            return getClass().hashCode();
      }
    }
    ```

  * App

    * 2~3일동안 EntityManager.find() 메소드를 호출한 뒤, 콘솔에 출력하면 다음과 같은 에러가 발생했다.

    * [원인](https://www.javafixing.com/2022/05/fixed-jpa-bidirectional-relationship.html)은 Lombok의 `@ToString` 어노테이션이 순환 참조를 해서 발생하는 것이었다.

      ```powershell
      Exception in thread "main" java.lang.StackOverflowError
              at java.base/java.lang.AbstractStringBuilder.ensureCapacityInternal(AbstractStringBuilder.java:173)
              at java.base/java.lang.AbstractStringBuilder.append(AbstractStringBuilder.java:538)
              at java.base/java.lang.StringBuilder.append(StringBuilder.java:174)
              at howto.jpa.repository.structure.Member.toString(Member.java:18)
      ```

    * 따라서 해당 어노테이션을 제거해주면 된다. 

      > 좀더 깊이
      >
      > 1. 실제로 `toString()`을 오버라이드했지만 여전히 동일한 에러가 발생한다.
      >
      > 2. 다만, 오버라이드했든 어노테이션을 사용하든 한쪽(`Member`)에만 있는 경우에는 문제가 없었다.
      >
      > 3. Jackson을 이용해 출력값을 확인하려고 사용했지만 여전히 동일한 에러가 발생한다.
      >
      > ```Java
      > if(findTeam != null) {
      > 	ObjectMapper mapper = new ObjectMapper();
      >     System.out.println(mapper.writeValueAsString(findTeam));    
      > }
      > ```

      

    * <span style='color:orange;font-weight:bold;'>양방향 참조 자체가 상호 참조를 하기때문에 발생하는 문제인 것으로 추정한다.</span> 따라서 상호 참조하는 Reference 변수를 toString()에서 하지 않도록 만들어 주는 것이 유효하다. 즉, 일대다-`Team`:`Member`에서 <u>Member에서 Team을 참고하고 있는 속성은 toString()에서 제외</u>하는 것이다. 

    * <span style='color:blue;font-weight:bold;'>양방향 객체 참조 Gooooood</span> - findTeam.getMembers().get(0).getTeam 했을 경우 findTeam과 동일하다.

      ```java
      if(findTeam != null) {
          System.out.println("Team: " + findTeam);
          System.out.println("Team.member: " + findTeam.getMembers().get(0));
          System.out.println("Team.member[0].team: " + findTeam.getMembers().get(0).getTeam());
      }
      ```



#### Value Mapping(@Embeddable)

##### 일대다(OneToOne) - 예시 `Team`:`Member`





### CASE #2 
