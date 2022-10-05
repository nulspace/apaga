> 문제 정의
>
> - Pure Java 개발을 위해서는 대부분의 통합환경에서도 지원하고 명령어 행에서도 사용가능한 maven 또는 Gradle이 가장 일반적이다.
> - VS Code 확장  마켓플레이스에서 `Maven for Java Plugin` 을 설치한다.



## Maven

### Maven Project 생성

위의 확장 플러그인을 설치하고 나면, 메이븐 프로젝트를 생성할 수 있는데, `명령어 팔레트(Ctrl/Cmd + Shift + p)`에서 순서는 다음과 같다.

1. `create maven project`를 입력한다.
2. `maven-archetype-quickstart`를 선택한다.
3. 메이븐 버전 `1.4`를 선택한다.
4. Group id `howto.jpa`를 기입한다.
5. Artifact Id `sample-jpa`를 기입한다.
6. 생성될 폴더를 선택한다.
7. 생성된 Workspace로 VS Code를 오픈한다.



> 메이븐이 설치되지 않아서, 관리자 모드 명령어 쉘에서 메이븐을 설치하고 환경변수를 리로딩...
>
> ```powershell
> cmd > choco install maven
> ...
> C:\ProgramData\chocolatey\lib\maven\apache-maven-3.8.6\bin
> ...
> 
> cmd> refreshenv
> ```





### Maven 명령어

#### 기본 명령어

##### mvn compile

- 컴파일 수행

- 컴파일 된 결과는 target/classes에 생성된다.

##### mvn test

- 테스트 클래스 실행

- 테스트 코드를 컴파일한 뒤 테스트 코드를 실행한다.

- 테스트 클래스들은 target/test-classes 디렉터리에 생성된다.

- 테스트 결과 리포트는 target/surefire-reports에 생성된다.

##### mvn package

- 컴파일된 결과물을 패키지 파일로 생성

- 컴파일, 테스트, 빌드를 수행하여 패키지 파일을 생성한다.

- 프로텍트 이름, 버전, 패키징 옵션에 맞게 파일이 생성된다.

- pom에서 아래와 같이 설정하면 결과 파일은 donggov-1.0-SNAPSHOT.war 로 생성된다.

  ```xml
      ...
  	<artifactId>donggov</artifactId> 
      <version>1.0-SNAPSHOT</version> 
      <packaging>war</packaging>
  	...
  ```

##### mvn install

- 패키징한 파일을 로컬 저장소에 배포

##### mvn deploy

- 패키징한 파일을 원격 저장소에 배포 (nexus 혹은 maven central 저장소)

##### mvn clean

- 메이븐 빌드를 통하여 생성된 모든 산출물을 삭제한다.



#### 자주사용하는 옵션

##### -Dmaven.test.skip=true

> - 테스트를 건너뛴다.
> - 예) mvn package -Dmaven.test.skip=true : 테스트를 건너뛰고 패키징 수행



##### -P[profile명] [goals]

> - 환경에 따라 다르게 설정을 관리할 수 있는 기능아다. profile(프로파일)이라고 부름.
> - pom에서 설정할수 있다.
> - 예) mvn -Pdevelopment -Dmaven.test.skip=true package
>   development라고 정의한 profile 설정으로 테스트를 건너뛰고 패키징 수행



