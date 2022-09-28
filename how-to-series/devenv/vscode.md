## Maven 개발환경

> 문제 정의
>
> - Pure Java 개발을 위해서는 대부분의 통합환경에서도 지원하고 명령어 행에서도 사용가능한 maven 또는 Gradle이 가장 일반적이다.
> - VS Code 확장  마켓플레이스에서 `Maven for Java Plugin` 을 설치한다.



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
>
> 
