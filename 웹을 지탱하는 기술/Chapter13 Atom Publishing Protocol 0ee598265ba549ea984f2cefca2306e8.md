# Chapter13 Atom Publishing Protocol

# 01 Atom Publishing Protocol이란 무엇인가

### Atom(Atom Syndication Format)과 AtomPub(Atom Publishing Protocol)

Atom : 데이터 포맷의 규칙(피드, 엔트리)

AtomPub : Atom을 이용한 리소스 편집 프로토콜의 규정, Atom이 규정한 피드와 엔트리로 표현하는 리소스의 편집, 소위 말하는 CRUD 조작을 실현하기 위한 프로토콜입니다.

### AtomPub의 의의

이전의 프로토콜 : XML-RPC 기반의 MetaWeblog API나 Blogger API 등 → 국제화, 확장성의 문제

But AtomPub은 범용적인 웹 API의 기초를 이룬 프로토콜

### AtomPub와 REST

AtomPub의 설계는 REST 스타일에 기초한 프로토콜 스펙

AtomPub 대응 프레임워크와 라이브러리

- Java : Apache Abdera(http://incubator.apache.org/abdera/)
- Python : amplee(http://j.mp/ampleep)
- Perl : XML::Atom::Server(http://j.mp/perl_atom)

# 02 AtomPub의 리소스 모델

![Chapter13%20Atom%20Publishing%20Protocol%200ee598265ba549ea984f2cefca2306e8/Untitled.png](Chapter13%20Atom%20Publishing%20Protocol%200ee598265ba549ea984f2cefca2306e8/Untitled.png)

- 서비스 문서 : 컬렉션의 메타 데이터를 표현
- 카테고리 문서 : 엔트리 카테고리에 지정할 수 있는 값을 열거

# 03 블로그 서비스

블로그 서비스를 예로, AtomPub를 어떻게 적용할 수 있는지 알아보자.

[http://blog.example.com](http://blog.example.com) 접속

기사 목록 리소스 [http://blog.example.com/entry/1234](http://blog.example.com/entry/1234) 같이 기사 ID가 들어간 URI로 특정

이 블로그 사이트의 엔트리를 포함하는 것이 컬렉션 리소스, 이 사이트의 톱페이지는 그 컬렉션 리소스에 있는 기사의 최신 목록의 HTML 표현.

하나의 리소스는 복수의 표현을 가질 수 있다. 톱 페이지는 HTML표현이었지만
AtomPub에 기초하면 컬렉션 리소스는 Atom 피드 형식으로도 표현할 수 있다.

```html
//요청
GET /feed HTTP/1.1
Host: blog.example.com

//응답
HTTP/1.1 200 OK
Content-Type: application/atom+xml:type=feed

<feed xmlns="http://www.w3.org/2005/Atom">
	<id>...</id>
	...
	<entry>
		<id>...</id>
		...
	</entry>
	<entry> ... </entry>
	<entry> ... </entry>
	<entry> ... </entry>
	<entry> ... </entry>
	...
</feed>
```

# 04 멤버 리소스의 조작

### 엔트리 단위에서의 조작

AtomPub에서는 Atom의 <link>요소에서 편집용 URI로 링크를 확장

```html
//예시 : rel 속성이 edit라는 값을 가진 <link> 요소를 편집링크라고 부릅니다.
<link href="http://blog.example.com/entry/1234.atom" rel="edit"/>
```

- GET - 엔트리 취득

```html
//요청
GET /entry/1234.atom HTTP/1.1
Host: blog.example.com

//응답
HTTP/1.1 200 OK
Content-Type: application/atom+xml:type=**entry**

<entry xmlns="http://www.w3.org/2005/Atom">
	<id>tag :blog.example.com,2010-08-24:entry:1234</id>
	<author><name>yohei</name></author>
	<updated>2010-08-24T13:11:54Z</updated>
	<link href="0http://blog.example.com/entry/1234" rel ="alternate"/>
	<link href=" http’//blog.example.com/entry/1234.atom" rel="edit"/>
	<content>테스트입니다.</content>
</entry>
```

- PUT - 엔트리 갱신

PUT할 때 변경한 부분 이외에는 GET한 리소스를 그대로 전송
AtomPub의 스펙에서는 모르는 요소, 속성도 포함해 모두 재전송할 것이 요구됨

```html
//요청
PUT /entry/1234.atom HTTP/1.1
Host: blog.example.com
Authorization: Basic dXNlcjpwYXNz
Content-Type: application/atom+xml; type=entry

<entry xmlns="http:www.w3.org/2005/Atom">
	<id>tag:blog.example.com.2010-08-24:entry:1234</id>
	<title>테스트 일기</title>
	<author><name>yohei</name></author>
	<updated>2010-08-24T13:11:54Z</updated>
	<link href="http://blog.example.com/entry/1234" rel="alternate"/>
	<link href="http://blog.example.com/entry/1234.atom" rel="edit"/>
	<content>수정했습니다.</content>
</entry>

//응답
HTTP/1.1 200 OK
```

- DELETE - 엔트리의 삭제

```html
//요청
DELETE/entry/1234.atom HTTP/1.1
Host:blog.example.com
Authorization: Basic dXNlcjpwYXNz

//응답
HTTP/1.1 200 OK
```

- POST - 엔트리의 작성

엔트리의 생성이 성공한 경우 201 Created 반환, 응답의 Location 헤더에는 새롭게 작성한 엔트리의 URI가 들어값니다. AtomPub의 서버는 id요소와 updated 요소를 갱신하고, POST한 엔트리에는 들어있지 않은 link요소를 추가하는 경우가 있습니다.

```html
//요청
POST/feed HTTP/1.1
Host:blog.example.com
Authorization: Basic dXNlcjpwYXNz
Content-Type:application/atom+xml; type=entry

<entry xmlns="http:www.w3.org/2005/Atom">
	<id>urn:uuid:1225c695.....</id>
	<title>테스트 일기</title>
	<author><name>yohei</name></author>
	<updated>2010-08-24T13:11:54Z</updated>
	<content>새로운 엔트리입니다.</content>
</entry>

//응답
HTTP/1.1 201 Created
Location: http:blog.example.com/entry/1235.atom
Content-Type: application/atom+xml; type=entry

<entry xmlns="http:www.w3.org/2005/Atom">
	<id>tag:blog.example.com,2010-08-24:entry:1235</id>
	<title>테스트 일기</title>
	<author><name>yohei</name></author>
	<updated>2010-08-24T13:11:55Z</updated>
	<link href="http://blog.example.com/entry/1235" rel="alternate"/>
	<link href="http://blog.example.com/entry/1235.atom" rel="edit"/>
	<content>새로운 엔트리입니다.</content>
</entry><
```

### 미디어 리소스의 조작

- 미디어 리소스의 작성

    <entry>요소의 XML문서는 이미지 본체를 보낼 수 없음. 이미지 본체를 POST함

    ```html
    //요청
    POST/media HTTP/1.1
    Host: blog.example.com
    Content-Type: image/jpeg
    Authorization: Basic dXNIcjpwYXNz
    Slug: %EC%9E%A5%EB%AF%B8

    binary data...

    //응답
    HTTP/1.1 201 Created
    Content-Type: application/atom+xml
    Location: http://blog.example.com/media/%EC%9E%A5%EB%AF%B8.atom

    <entry xmlns="http://www.w3.org/2005/Atom">
    	<id>tag:blog.example.com,2010-10-04:blog:media:%EC%9E%A5%EB%AF%B8</id>
    	<title>일본어...</title>
    	<author><name>test</name></author>
    	<content type="image/jpeg" src="http://blog.example.com/media/%EC%9E%A5%EB%AF%B8.jpg"/>
    	<link rel="edit-media" href="http://blog.example.com/media/%EC%9E%A5%EB%AF%B8.jpg"/>
    	<link rel="edit" href="http://blog.example.com/media/%EC%9E%A5%EB%AF%B8.atom"/>
    </entry>
    ```

- 미디어 리소스의 갱신

    이미지 데이터를 갱신하고 싶을 때 edit-media 링크로 참조할 수 있는 URI에 PUT을 보냅니다.

    ```html
    //요청
    PUT/media/%EC%9E%A5%EB%AF%B8.jpg HTTP/1.1
    Host: blog.example.com
    Authorization: Basic dGVzdDpwYXNz
    Content-Type: imaage/jpeg

    binary data...

    //응답
    HTTP/1.1 200 OK
    ```

# 05 서비스 문서

### 미디어 타입

Content-Type 헤더에서의 application/atomsvc+xml

### <service>요소

서비스 문서에 등장하는 Atom의 이름공간에 소속된 요소는 'atom:'이라는 접두어를 붙혀서 표현

### <workspace>요소

<service>요소는 자식요소로 반드시 하나 이상의 <workspace>요소를 가집니다.

### <collection>요소

<workspace>요소는 0개 이상의 <collection>요소를 가집니다.

### <accept>요소

<accept>요소는 이 컬렉션 리소스가 받아들일 수 있는 미디어 타입을 보여줍니다.

### 카테고리

- 카테고리 문서 : <categories href="......">, href 속성이 있다면 자식요소를 가질 수 없음
- 카테고리의 추가

# 06 AtomPub에 적합한 웹 API

AtomPub은 타이틀과 갱신일자 같은 기본적인 메타 데이터를 가진 리소스인 엔트리를 CRUD하는 웹 API를 위한 프로토콜입니다.

실제로 Google은 AtomPub을 베이스로한 GData를 사용해 다양한 웹 서비스의 API를 제공하고 있습니다. AtomPub라는 공통 프로토콜을 사용하여 블로그, 캘린더, 스프레드시트, 앨범 등을 편집할 수 있도록 되어있는 것입니다.

### AtomPub에 적합한 웹 API

- 블로그 서비스의 API
- 검색 기능을 가진 데이터베이스의 API
- 멀티미디어 파일의 리포지터리의 API
- 태그를 사용한 소셜 서비스의 API

### AtomPub에 적합하지 않은 웹 API

- Comet을 이용하는 실시간성이 중요한 API
- 영상의 스트림 전송 등 HTTP 이외의 프로토콜을 필요로 하는 API
- 데이터의 계층구조가 중요한 API
- '타이틀', '작성자', '갱신일시' 같은 Atom 포맷이 제공하는 메타 데이터가 불필요한 API