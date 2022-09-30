## H2 Database

윈도우용 설치 패키지 [다운](https://github.com/h2database/h2database/releases/download/version-2.1.214/h2-setup-2022-06-13.exe)받아서 실행한다.

설치된 HOME  폴더(D:\devbin\H2)에는 

```html
[HOME] +
       +-- bin
       +-- dbins // 사용자 정의 database instance
       +-- docs
       +-- service
```



> ##### 서비스 등록
>
> `service`폴더에는 윈도우 서비스로 등록/삭제할 수 있는 배치 파일이 있다.
>
> 또는 
>
> ##### 프로세스 실행
>
> ```powershell
> cmd > java -jar h2*.jar 
> ```
>
> 이때, 주의해야 할 것은 `~`은 user home 폴더를 `./`은 현재 작업 폴더를 `/`은 프로세스가 실행한 최상위 폴더를 의미한다.



이제, 웹 브라우저를 이용해 `http://localhost:8082/`에 접속하면 데이터베이스에 접근할 수 있는 `로그인`화면이 보인다.

* `Driver class`:  org.h2.Driver
* `JDBC URL`:  다음 [장](#concept)에서 모드에 따라서
* `사용자명`: sa (디폴트)
* `비밀번호`: 없음 (디폴트)



#### Concept

아직까지 정확한 컨셉을 이해하지 못했지만, 다음 3가지 모드로 존재하며 memory 모드를 제외한 두 모드는 URL은 파일 경로로 상대 또는 절대 경로로 표현할 수 있다. 자세한 것은 [여기](http://www.h2database.com/html/cheatSheet.html)를 참고하라.

데이터베이스 명은 파일 시스템에서 접미사 `.mv.db`을 붙여서 존재한다.

> JDBC URL 형식 `jdbc:h2:[모드][파일 경로][데이터베이스 명]`

* Server 모드 - `tcp://localhost/`
  * 해당 URL로 여러 사용자가 접근 할 수 있다.
  * JDBC URL: `jdbc:h2:tcp://localhost//devbin/H2/dbins/deficial`
* Embedded 모드
  * 해당 URL로 단일 사용자만이 접근할 수 있다. 
  * <span style='color:blue;'>데이터베이스 명을 처음 사용하고 한번만 연결해 주면 데이터베이스가 생성된다.</span>
  * 이후에 사용하려면 `Server`모드로 변경하여 사용하는 것을 추천한다. 그렇지 않으면 `락` 문제를 ... 
  * JDBC URL: `jdbc:h2:/devbin/H2/dbins/deficial`, `jdbc:h2:~/deficial` , `jdbc:h2:./deficial` 
* In-Memory 모드
  * 프로세스가 동작하는 동안의 메모리 상에서만 데이터베이스가 존재한다.
  * 주로, 개발할 때 테스트용으로 사용한다.
  * JDBC URL: `jdbc:mem:defical`

