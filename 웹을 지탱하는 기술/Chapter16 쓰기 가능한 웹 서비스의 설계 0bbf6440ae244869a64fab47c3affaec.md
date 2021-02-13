# Chapter16 쓰기 가능한 웹 서비스의 설계

# 01 쓰기 가능한 웹 서비스의 어려운 점

- 예시
    - 복수의 사용자가 동시에 쓰기를 하는 경우엔 어떻게 되는지
    - 백업한 데이터를 복원할 때처럼 동시에 복수의 리소스를 갱신하기 위해선 어떻게 해야하는지
    - 복수의 처리과정을 반드시 실행하기 위해서 어떻게 해야하는 지 등

# 02 쓰기 가능한 우편번호 서비스의 설계

- 15장의 기능 + a
    - 우편번호 리소스의 작성
    - 우편번호 리소스의 갱신
    - 우편번호 리소스의 삭제
    - 일괄처리
    - 트랜잭션
    - 배타제어

# 03 리소스의 작성

### 방법 1. 팩토리 리소스에 POST

- 팩토리 리소스란 리소스를 작성하기 위한 특별한 리소스
- 추가할 새로운 리소스를 JSON형식으로 POST
- 예

    ```java
    //톱 레벨 리소스(http://zip.ricollab.jp)를 팩토리 리소스로 함
    //요청
    POST /HTTP/1.1
    Host: zip.ricollab.jp
    Content-Type: application/json

    {
    	"zipcode":"9999999",
    	"address":{
    		"prefecture":"XX현",
    		"city":"YY시",
    		"town":"ZZ정"
    	},
    	"yomi":{
    		"prefecture":"엑스엑스켄",
    		"city":"와이와이시",
    		"town":"제트제트나비"
    	}
    }

    //응답
    HTTP/1.1 201 Created
    Content-Type: application/json
    Location: http://zip.ricollab.jp/9999999

    {
    	"zipcode":"9999999",
    	"address":{
    		"prefecture":"XX현",
    		"city":"YY시",
    		"town":"ZZ정"
    	},
    	"yomi":{
    		"prefecture":"엑스엑스켄",
    		"city":"와이와이시",
    		"town":"제트제트나비"
    	}
    }
    ```

### 방법 2. PUT으로 직접 작성

- 이 예제에서는 우편번호에 의해 리소스의 URI가 미리 정해져 있기 때문에 PUT로도 작성 가능
- 이점
    - POST를 지원할 필요가 없어지기 때문에 서버측의 구현이 간단해진다.
    - 클라이언트가 작성과 변경을 구별할 필요도 없어도 되므로 클라이언트측 구현이 간단해진다
- 결점
    - 클라이언트가 URI 구조를 미리 알아야 한다.
    - 요청의 외관상으로는 그 조작이 신규작성인지 갱신인지 구별할 수 없다.
- 예

    ```java
    //PUT의 경우 새로 작성하고 싶은 리소스의 URI에 직접 요청을 보냄.
    //또한 작성할 리소스의 URI를 클라이언트가 모두 알고 있기 때문에 
    //응답메시지에 Location 헤더는 포함하지 않음

    //요청
    PUT /9999999 HTTP/1.1
    Host: zip.ricollab.jp
    Content-Type: application/json

    {
    	"zipcode":"9999999",
    	"address":{
    		"prefecture":"XX현",
    		"city":"YY시",
    		"town":"ZZ정"
    	},
    	"yomi":{
    		"prefecture":"엑스엑스켄",
    		"city":"와이와이시",
    		"town":"제트제트나비"
    	}
    }

    //응답
    HTTP/1.1 201 Created
    Content-Type: application/json

    {
    	"zipcode":"9999999",
    	"address":{
    		"prefecture":"XX현",
    		"city":"YY시",
    		"town":"ZZ정"
    	},
    	"yomi":{
    		"prefecture":"엑스엑스켄",
    		"city":"와이와이시",
    		"town":"제트제트나비"
    	}
    }
    ```

# 04 리소스의 갱신

기본적으로 PUT으로 수행 (일괄갱신은 POST)

### 벌크 업데이트 Bulk Update

- PUT의 가장 기본적인 사용법은 갱신하고자 하는 **리소스 전체**를 그대로 메시지 바디에 넣는 방법
- 구현은 간단, 전송할 데이터는 커짐
- 예

    ```java
    PUT /9999999 HTTP/1.1
    Host: zip.ricollab.jp
    Content-Type: application/json

    {
    	"zipcode":"9999999",
    	"address":{
    		"prefecture":"XX현",
    		"city":"YY시",
    		"town":"ZZ정"
    	},
    	"yomi":{
    		**"prefecture":"바트바트켄",**
    		"city":"와이와이시",
    		"town":"제트제트나비"
    	}
    }
    ```

### 파셜 업데이트 Partial Update

- 부분적인 데이터를 송신, 리소스의 일부분만을 송신하는 갱신방법
- 송수신할 데이터가 적은 반면, GET한 리소스의 일부를 수정해서 그대로 PUT하는 식의 사용방법은 불가능하게 된다.
- 예

    ```java
    PUT /9999999 HTTP/1.1
    Host: zip.ricollab.jp
    Content-Type: application/json

    {
    	"yomi":{
    		**"prefecture":"바트바트켄",**
    	}
    }
    ```

### 갱신할 수 없는 프로퍼티를 갱신하고자 하는 경우

우편번호는 정해지면 대응하는 주소가 바뀌는 경우는 있어도 번호 자체는 변하지 않습니다.

따라서 우편번호를 갱신하고자 하는 경우는 400 Bad Request를 반환해, 그 프로퍼티는 갱신할 수 없다는 것을 클라이언트에게 알려주는 것이 좋다.

반면에 작성일시, 갱신일시 등 서버가 자동적으로 값을 갱신하는 프로퍼티인 경우, 클라이언트가 갱신을 요청해 왔다가 해도, 무시하고 200 OK를 반환하는 편이 좋다.

# 05 리소스의 삭제

삭제하고 싶은 리소스의 URI에 DELETE를 보내서 리소스를 삭제합니다.

주의사항

- 삭제 대상 리소스 아래에 자식 리소스가 존재하는 경우
    - 예시에서는 지역 리소스
    - 블로그 엔트리에서는 답글들도 같이 삭제하는 것이 일반적이다.

# 06 일괄처리

### 일괄처리 요청

```java
//예 : 일괄갱신, 2개의 우편번호 리소스를 한번에 갱신하는 요청
//PUT의 경우 갱신대상 리소스를 URI로 지정해야만 하기 때문에 POST 사용
POST /HTTP/1.1
Host: zip.ricollab.jp
Content-Type: application/json

{
	{
		"zipcode":"9999998",
		"address":{
			"prefecture":"XX현",
			"city":"YY시",
			"town":"ZZ정"
		},
		"yomi":{
			"prefecture":"바트바트켄",
			"city":"와이와이시",
			"town":"와이와이나비"
		}
	},
	{
		"zipcode":"9999999",
		"address":{
			"prefecture":"XX현",
			"city":"YY시",
			"town":"ZZ정"
		},
		"yomi":{
			"prefecture":"바트바트켄",
			"city":"와이와이시",
			"town":"제트제트나비"
		}
	}
}
```

### 일괄처리의 응답

요청을 받은 서버는 JSON 배열을 분해하고 각각의 갱신처리를 수행합니다.
에러가 발생한 경우의 대처방법이 문제가 됩니다.

1. 일괄처리를 트랜잭션화 하여 도중에 실패한 경우 
    아무 처리도 하지 않음을 웹서비스 내부에서 보증하는 것

2. 어느 리소스에 대한 처리가 성공했고 어느 리소스에 대한 처리가 실패했는지 
    클라이언트에게 전달한다.

- 207 Multi-Status와 WebDAV의 <D:multistatus>요소를 조합하는 방법
- 200 OK와 독자 포맷을 조합하는 방법
- WebDAV란?

    Web Distributed Authoring and Versioning, 웹 분산 저작 및 버전 관리

    HTTP의 확장으로 WWW 서버에 저장된 문서와 파일을 편집하고 관리하는 사용자들 사이에 협업을 손쉽게 만들어준다.

    분산 저작에 초점을 맞춘 프로토콜로 이해하자

    - 확장된 메소드들
        - PROPFIND
        리소스를 위한 속성을 검색한다. 즉 웹의 파일 목록과 속성을 검색한다.
        - PROPPATCH
        하나의 리소스에 대한 속성을 변경한다.
        - MKCOL
        새로운 컬렉션(디렉토리, 폴더에 해당)을 만든다.
        - COPY
        리소스와 컬렉션을 복사한다. 파일 복사
        - MOVE
        리소스와 컬렉션을 이동한다. 파일 이동
        - LOCK, UNLOCK
        리소스에 overwrite 방지를 위한 락을 걸고 푼다

    참고 : [https://m.blog.naver.com/PostView.nhn?blogId=rainysun7&logNo=50021644302&proxyReferer=https:%2F%2Fwww.google.com%2F](https://m.blog.naver.com/PostView.nhn?blogId=rainysun7&logNo=50021644302&proxyReferer=https:%2F%2Fwww.google.com%2F)

- 207 Multi-Status : 복수의 결과를 표현한다.
WebDAV가 정의하고 있는 스테이터스 코드
    - 예

        ```java
        HTTP/1.1 207 **Multi-Status**
        Content-Type: application/xml; charset="utf-8"

        <D:multistatus xmlns:D="DAV:">
        	<D:response>
        		<D:href>http://zip.ricollab.jp/</D:href>
        		<D:propstat>
        			**<D:status>HTTP/1.1 200 OK</D:status>**
        		</D:propstat>
        		<D:propstat>
        			**<D:status>HTTP/1.1 500 Internal Server Error</D:status>**
        			<D:responsedescription>
        				데이터베이스 접속 에러입니다.
        			</D:responsedescription>
        		</D:propstat>
        	</D:response>
        	<D:responsedescription>
        		모두 2건의 요청 중 1건이 성공했습니다.
        	</D:responsedescription>
        </D:multistatus>
        ```

- 독자적인 복수 스테이터스 포맷
심플한 응답인 경우 독자적인 XML과 JSON으로 반환하는 것이 더 좋다.
    - 예

        ```java
        HTTP/1.1 200 OK
        Content-Type: application/json

        [
        	{
        		"status":"200 OK",
        		"description":"성공했습니다"
        	},
        	{
        		"status":"500 Internal Server Error",
        		"description":"데이터베이스 접속 에러입니다."
        	}
        ]
        ```

# 07 트랜잭션

### 해결해야만 하는 문제

- 예 : 리소스의 일괄삭제
    - http://zip.collab.jp/1
    - http://zip.collab.jp/2
    - http://zip.collab.jp/3

    도중에 실패하게 된다면 오른쪽과 같이 하나만 삭제되는 경우가 생길 수 있다.

    모아서 삭제하거나, 아무것도 삭제하지 않는다는것을 보증하기 위해 어떻게 해야할까?

![Chapter16%20%E1%84%8A%E1%85%B3%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A1%E1%84%82%E1%85%B3%E1%86%BC%E1%84%92%E1%85%A1%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B5%E1%84%89%E1%85%B3%E1%84%8B%E1%85%B4%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%200bbf6440ae244869a64fab47c3affaec/Untitled.png](Chapter16%20%E1%84%8A%E1%85%B3%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A1%E1%84%82%E1%85%B3%E1%86%BC%E1%84%92%E1%85%A1%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B5%E1%84%89%E1%85%B3%E1%84%8B%E1%85%B4%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%200bbf6440ae244869a64fab47c3affaec/Untitled.png)

### 트랜잭션 리소스

트랜잭션 정보를 표현하는 리소스

예로 트랜잭션 리소스를 [http://zip.ricollab.jp/transactions](http://zip.ricollab.jp/transactions) 라는 팩토리 리소스를 이용해 작성해보자

- 트랜잭션의 시작

```java
//요청
POST /transactions HTTP/1.1
Host: zip.ricollab.jp

//트랜잭션 리소스의 작성이 성공
//응답
HTTP/1.1 201 Created
Location: http://zip.ricollab.jp/transactions/308
```

- 트랜잭션 리소스에 처리 대상 추가

```java
//생성된 트랜잭션 리소스에 삭제하고 싶은 리소스의 URI를 추가해간다.
//예시에서는 트랜잭션리소스 아래에 삭제하고 싶은 URI 경로를 직접 추가하여 PUT으로 리소스 생성
//요청(/1)
PUT /transaction/308/1 HTTP/1.1
Host: zip.ricollab.jp

//응답(/1)
HTTP/1.1 201 Created
Location: http://zip.ricollab.jp/transactions/308/1

...
```

- 트랜잭션의 실행

```java
//트랜잭션 리소스에 처리를 실행하고 싶다는 뜻을 전달하기 위해 
//PUT으로 커밋을 전달하는 데이터를 기록하여 커밋을 표현
//요청
PUT /transactions/308 HTTP/1.1
Host: zip.ricollab.jp
Content-Type: application/x-www-form-urlencoded

commit=true

//응답(성공)
HTTP/1.1 200 OK

//응답(에러 발생시)
HTTP/1.1 500 Internal Server Error
```

- 트랜잭션 리소스의 삭제

```java
//모두 성공한 경우 트랜잭션 리소스를 삭제한다.
//요청
DELETE /transaction/308 HTTP/1.1
Host: zip.ricollab.jp

//응답
HTTP/1.1 200 OK
```

트랜잭션 리소스를 삭제하면 트랜잭션에 관련된 다른 리소스(http://zip.ricollab.jp/transactions/308/*)도 동시에 삭제하도록 서버를 구현합니다.

커밋하기 전에 트랜잭션 리소스에 DELETE를 발행하면 트랜잭션은 실행되지 않기 때문에 모든 리소스는 트랜잭션 시작 전의 상태와 바뀌지 않습니다.

### 트랜잭션 리소스 이외의 해결방법

- 일괄처리의 트랜잭션화

```java
//복수 리소스에 대한 삭제 요청을 POST로 일괄적으로 송신하고
//서버 내에서 이들을 반드시 모두 삭제하거나 아니면 하나도 삭제하지 않는다는 것을 보증하도록 구현

POST /batch HTTP/1.1
Host: zip.ricollab.jp
Content-Type: text/plain; charset=utf-8

DELETE /1
DELETE /2
DELETE /3
```

- 상위 리소스에 대한 조작

상위 리소스를 삭제하면(entry/1)

하위 리소스도 동시에 삭제할 수 있도록 서버를 구현(entry/1/comment/1, entry/1/comment/2, ...)

이 경우에도 서버 내부적으로 반드시 모두 삭제하거나 하나도 삭제하지 않는다는 것을 보증해야 함

# 08 배타제어

복수의 클라이언트가 동시에 하나의 리소스를 편집해 경합(Conflict)이 일어나지 않도록 하나의 클라이언트만 편집하도록 제어하는 처리

비관적 잠금(Pessimistic Lock)과 낙관적 잠금(Optimistic Lock)이 있다.

### 해결해야하는 문제

![Chapter16%20%E1%84%8A%E1%85%B3%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A1%E1%84%82%E1%85%B3%E1%86%BC%E1%84%92%E1%85%A1%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B5%E1%84%89%E1%85%B3%E1%84%8B%E1%85%B4%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%200bbf6440ae244869a64fab47c3affaec/Untitled%201.png](Chapter16%20%E1%84%8A%E1%85%B3%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A1%E1%84%82%E1%85%B3%E1%86%BC%E1%84%92%E1%85%A1%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B5%E1%84%89%E1%85%B3%E1%84%8B%E1%85%B4%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%200bbf6440ae244869a64fab47c3affaec/Untitled%201.png)

### 비관적 잠금

사용자를 신용하지 못하고, 경합이 발생하지 않도록 한다.

HTTP로 구현하기 위해서는 WebDAV의 LOCK/UNLOCK 메서드를 사용하는 방법과 독자적인 잠금 리소스를 사용하는 방법이 있습니다.

- LOCK/UNLOCK

    ```java
    //요청
    LOCK /1120002 HTTP/1.1
    Host: zip.ricollab.jp
    Timeout: Second-3600
    Content-Type: application.xml; charset=utf-8
    Authorization: Basic...

    <D:lockinfo xmlns:D="DAV:">
    	<D:lockscope><D:exclusive/></D:lockscope>
    	<D:locktype><D:write/></D:locktype>
    </D:lockinfo>
    ```

    ![Chapter16%20%E1%84%8A%E1%85%B3%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A1%E1%84%82%E1%85%B3%E1%86%BC%E1%84%92%E1%85%A1%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B5%E1%84%89%E1%85%B3%E1%84%8B%E1%85%B4%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%200bbf6440ae244869a64fab47c3affaec/Untitled%202.png](Chapter16%20%E1%84%8A%E1%85%B3%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A1%E1%84%82%E1%85%B3%E1%86%BC%E1%84%92%E1%85%A1%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B5%E1%84%89%E1%85%B3%E1%84%8B%E1%85%B4%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%200bbf6440ae244869a64fab47c3affaec/Untitled%202.png)

    - 태그 설명

        Timeout 헤더 : 잠금이 타임아웃하는 시간(클라이언트측의 요구)을 지정한다. 여기선 1시간(3600초)

        Authorization 헤더 : 잠금을 할 사용자의 정보를 지정한다.

        <D:lockscope>요소 : 이 잠금이 배타 잠금(Exclusive Lock)인지 공유잠금(Shared Lock)인지 지정한다.

        - 배타잠금<D:exclusive>요소를 지정하여 이 리소스를 한 사람의 사용자만 편집 가능하게 한다
        - 공유잠금<D:shared>요소를 지정하여 자신이 이 리소스를 편집하려고 한다는 뜻을 표명하여 경합이 일어나기 어렵게 한다.

        <D:locktype>요소 : 잠금의 종류를 지정한다. WebDAV에서는 쓰기 잠금(Write Lock)을 지정하는 <D:write>요소만을 정의하고 있다.

    ```java
    //응답
    HTTP/1.1 200 OK
    Content-Type: application/xml; charset=utf-8

    <D:prop xmlns:D="DAV:">
    	<D:lockdiscovery>
    		<D:activelock>
    			<D:lockscope><D:exclusive/></D:lockscope>
    			<D:locktype><D:write/></D:locktype>
    			<D:depth>Infinity</D:depth>
    			<D:owner>
    				<D:href>mailto:yoheiy@gmail.com</D:href>
    			</D:owner>
    			<D:timeout>Second-3600</D:timeout>
    			<D:locktoken>
    				<D:href>opaquelocktoken:e71d4fae-5dec-22d6-fea5</D:href>
    			</D:locktoken>
    		</D:activelock>
    	</D:lockdiscovery>
    </D:prop>
    ```

    - 태그 설명

        <D:activelock>요소 : 현재 유효한 잠금의 정보

        <D:lockscope>요소, <D:locktype>요소 : 요청에서 지정한대로 잠금이 이루어져 있다는 것을 알 수 있다.

        <D:depth> 요소 : URI의 계층상 어디까지 잠금을 하고 있는지 보여준다. 'infinity'는 잠그고 있는 리소스의 패스 이하 모든 서브 리소스를 잠그고 있다는 것을 나타낸다

        <D:owner> 요소 : 이 잠금의 소유자 정보

        <D:timeout> 요소 : 이 잠금이 타임아웃하는 시간. Second-3600 3,600초, 즉 1시간을 의미한다.

        <D:locktoken> 요소 : 이후에 이 잠금을 이용해 리소스를 조작할 때는 If헤더에 이 잠금 토큰을 지정할 필요가 있다.

    - 잠금이 끝난 리소스를 편집하기 위해서는 If헤더로 잠금 토큰을 지정해야함.
    - 잠금 토큰을 지정하지 않을 채 리소스를 편집하려고 하면 아래와 같이 반환됨

    ```java
    HTTP/1.1 423 Locked
    ```

    - 잠근 토큰을 지정하고 PUT을 송신하면 리소스를 갱신할 수 있다.

    ```java
    //요청
    PUT /1120002 HTTP/1.1
    If:(<opaque Locktoken:e71d4fae-5dec-22d6-fea5>)
    Content-Type: application/json

    {
    	"zipcode":"1120002",
    	"address":{
    		"perfecture":"도쿄도",
    		....
    	},
    ...
    }
    //응답
    HTTP/1.1 200 OK
    ```

    - 갱신이 종료되면 클라이언트는 잠금을 해제합니다. 잠금 해제는 UNLOCK을 이용합니다.

    ```java
    //요청
    UNLOCK /1120034 HTTP/1.1
    Host: zip.ricollab.jp
    Content-Type: application/xml; charset=utf-8
    Authorization:Basic...
    Lock-Token: opaquelocktoken:e71d4fae-5dec-22d6-fea5

    //응답
    HTTP/1.1 200 OK
    ```

- 독자적인 잠금 리소스의 도입

    오버스펙같다면 LOCK에 해당하는 기능을 웹서비스에 내장

    ```java
    //1.잠금 리소스를 작성
    //요청
    POST /1120034 HTTP/1.1
    Host:zip.ricollab.jp
    Content-Type: application/x-www.form-urlencoded
    Authorization:Basic...

    scope=exclusive&timeout=300

    //2.잠그고 싶은 리소스의 URI에 대해 POST로 잠금정보를 보냄

    //3. 잠금 대상의 리소스의 자식 리소스로서 잠금 리소스가 생김
    // 잠금 리소스가 존재하는 동안 대상 리소스는 잠겨있고, 잠금을 한 사용자만 편집 가능
    //응답
    HTTP/1.1 201 Created
    Location: http://zip.ricollab.jp/1120034/lock
    Content-Type: application/json

    {
    	"locktype":"exclusive",
    	"timeout":"2010-09-07T10:00:30Z",
    	"owner":"yohei"
    }

    //4. 만약 다른 사용자가 이미 잠겨있는 리소스를 접근했을 때
    //4.1 423 Locked 이나 400 Bad Request 반환

    //5. 잠금의 해제 : 타임아웃 또는 잠금 리소스의 삭제
    //요청
    DELETE /1120034/lock HTTP/1.1
    Host: zip.ricollab.jp
    Authorization:Basic...
    //응답
    HTTP/1.1 200 OK
    ```

### 낙관적 잠금

- 항상 같은 문서를 여러 사람이 계속 편집하는 경우는 거의 없다는 전제하에서 통상적인 편집에서는 문서를 잠그지 않고 경합이 일어났을 때 대처하는 구조
- 비관적 잠금은 경합X, 잠금의 권한을 가진 사람 이외에는 편집 X → 시스템이 커질수록 문제발생
- 조건부 PUT과 조건부 DELETE 사용
- 조건부 PUT

    클라이언트가 갱신요청을 할 때 자신이 갱신하고자 하는 리소스의 변경여부를 확인하는 구조

    ```java
    //요청
    GET /1120002 HTTP/1.1
    Host: zip.ricollab.jp

    //응답
    HTTP/1.1 200 OK
    Content-Type: application/json
    ETag: sample-etag-value
    {
    	"zipcode":"1120002",
    	...
    }

    //얻어진 ETag값을 사용해 조건부 PUT을 실행합니다.
    //리소스가 변경되어 있지 않다면 ETag의 값은 바뀌지 않기 때문에 리소스의 갱신에 성공
    //요청
    PUT /1120002 HTTP/1.1
    Host: zip.ricollab.jp
    Content-Type: application/json
    ETag: sample-etag-value
    {
    	"zipcode":"11200002"
    	...
    }

    //응답
    HTTP/1.1 200 OK
    ```

- 조건부 DELETE

    다른 사람이 수정한 것을 모른 채 리소스를 삭제하는 실수를 방지할 수 있다.

    ```java
    //요청
    DELETE /1120002 HTTP/1.1
    Host: zip.ricollab.jp
    Content-Type: application/json
    If-Match: sample-etag-value

    //응답
    HTTP/1.1 200 OK
    ```

- 412 Precondition Failed - 조건이 맞지 않음
    1. 경합을 일으킨 사용자에게 확인한 후, 갱신 또는 삭제를 한다.
    2. 경합을 일으킨 데이터를 경합 리소스로서 별도의 리소스로 보전한다(PUT인 경우만)
    3. 경합을 일으킨 사용자에게 변경점을 전하고, 병합을 촉구한다(PUT인 경우만)

        ![Chapter16%20%E1%84%8A%E1%85%B3%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A1%E1%84%82%E1%85%B3%E1%86%BC%E1%84%92%E1%85%A1%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B5%E1%84%89%E1%85%B3%E1%84%8B%E1%85%B4%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%200bbf6440ae244869a64fab47c3affaec/Untitled%203.png](Chapter16%20%E1%84%8A%E1%85%B3%E1%84%80%E1%85%B5%20%E1%84%80%E1%85%A1%E1%84%82%E1%85%B3%E1%86%BC%E1%84%92%E1%85%A1%E1%86%AB%20%E1%84%8B%E1%85%B0%E1%86%B8%20%E1%84%89%E1%85%A5%E1%84%87%E1%85%B5%E1%84%89%E1%85%B3%E1%84%8B%E1%85%B4%20%E1%84%89%E1%85%A5%E1%86%AF%E1%84%80%E1%85%A8%200bbf6440ae244869a64fab47c3affaec/Untitled%203.png)

# 09 설계의 밸런스

품질, 기한, 모두 만족시키는 밸런스를 찾아라

- 될 수 있는 한 심플하게 유지한다
- 막히면 리소스로 돌아가 생각한다.
- 정말 필요하다면 POST로 무엇이든 할 수 있다.