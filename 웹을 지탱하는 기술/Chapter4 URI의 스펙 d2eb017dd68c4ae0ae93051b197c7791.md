# Chapter4 URI의 스펙

# 01 URI의 중요성

### URI : Uniform Resource Identifier

'리소스를 통일적으로 식별하는 ID'

URI를 사용하여 웹상에 존재하는 모든 리소스를 한결같은 방식으로 보여줄 수 있다. → 모든 리소스에 간단하게 접속할 수 있다.

# 02 URI의 구문

URI의 스펙은 RFC 3986?

### 간단한 URI의 예

**http://blog.example.com/entries/1**

- URI Scheme : http
- 호스트명 : blog.examples.com
- 패스 : /entries/1

URI는 **URI 스키마**로 시작합니다.

- URI가 이용하는 프로토콜을 나타내는 것이 일반적
→ URN 등 프로토콜을 나타내지 않는 URI 스키마도 존재
- "://"로 구분됩니다.

다음으로 **호스트명**이 나타납니다.

DNS(Domain Name System)에서 이름을 해석할 수 있는 도메인명인나 IP어드레스로 인터넷에서 반드시 일의성을 가지게 됩니다.

호스명 뒤에는 **계층화를 나타내는 경로**가 이어집니다.

경로는 호스트 안에서 오로지 하나의 리소스를 가리킵니다.

이처럼 인터넷 상에서 반드시 유일한 호스트명의 구조와 호스트 내에서 유일한 계층적인 경로를 결합함으로써, 어떤 리소스의 URI가 전세계 다른 리소스와 절대로 중복되지 않도록 되어있다.

**예시용 도메인명**
example.com : IANA(Internet Assigned numbers Authority)에서 예시용으로 예약해 둔 도메인
example.com외에도 example.net, example.org, example.edu도 예시용으로 예약. 이들은 테스트용 도메인 등과 함께 RFC 2606에서 예시용으로 정의해 둠

### 복잡한 URI의 예

http://yohei:pass@blog.example.com:8000/search?q=test&debug=true#n10

- URI Scheme : http
- 사용자 정보 : yohei:pass
- 호스트명 : blog.example.com
- 포트번호 : 8000
- 패스 : /search
- 쿼리 파라미터 : q=test&debug=true
- URI 프래그먼트 : #n10

사용자 정보 : 이 리소스에 접근할 때 이용할 사용자 이름과 패스워드로 구성됨. ':'로 구분

호스트 정보 : 호스트명과 포트번호로 구성.  ':'로 구분

포트번호 : 이 호스트에 액세스 할 때, 프로토콜이 사용할 TCP의 포트번호를 나타냄. 생략될 때는 각 프로토콜의 디폴트 값이 사용됨. HTTP의 디폴트 값은 80번

쿼리파라미터 : 패스 뒤에는 구분 문자인 '?'가 오고 이름=값의 형식으로 된 쿼리가 이어짐. 하나 이상의 쿼리의 집합을 '쿼리 파라미터' 또는 '쿼리 문자열'이라고 부릅니다. 쿼리 파라미터는 검색 서비스에 검색 키워드를 전달할 때 등 클라이언트에서 동적으로 URI를 생성할 때 사용

URI 프래그먼트 : #앞에 문자열로 표현한 URI가 가리키는 리소스 내부에서 HTML 문서인 경우는 id 속성 값이 n10인 요소를 나타내게 됨?

# 03 절대 URI와 상대 URI

- 절대경로 Absolute Path, 절대 URI : http://example.com/foo/bar
- 상대경로 Relative Path, 상대 URI : /foo/bar

### Base URI

상대 URI의 기준을 지정. 기본 URI

### 대표적인 2가지 URI 부여 방식

- **리소스의 URI를 Base URI로 하는 방법**

    상대 URI가 출현하는 리소스의 URI를 Base URI로 부여

    장점

    직관적이고 이해하기 쉬움

    단점

    Base URI가 되는 리소스의 URI를 클라이언트 측에서 가지고 있어야 됨

- **Base URI를 명시적으로 지정하는 방법**

    HTML과 XML 안에서 명시적으로 Base URI를 지정하는 방법

    ```html
    <html xmlns="http://www.w3.org/1999/xhtml:>
    	<head>
    		<title>test web page</title>
    		<!--이 HTML 문서 내의 Base URI는 http://example.com이 된다. -->
    		<base href = "http://example.com/"/>
    		...
    	</head>
    	...
    </html>
    ```

    ```html
    <foo xml:base="http://example.com/foo">
    	<!-- 이 곳의 Base URI는 http://example.com/foo가 된다 -->
    	<bar xml:base="http://example.com/foo/bar">
    		<!-- 이 곳의 Base URI는 http://example.com/foo/bar가 된다 -->
    	</bar>
    </foo>
    ```

# 04 URI와 문자

### URI에서 사용할 수 있는 문자

**ASCII American Standard Code for Information Interchange**

- 알파벳 : A-Za-z
- 숫자 : 0-9
- 기호 : -.~:@!$&'()

한글이나 일어 등 ASCII 이외의 문자를 URI에 넣을 때는 %인코딩 방식을 이용합니다.

### %인코딩

가 → %EA%B0%80, UTF-8 : 0xEA 0xB0 0x80

### %인코딩의 문자 인코딩

UTF-8, EUC-KR : 절대 경로가 다르다. 인코딩 결과가 다르다.

# 05 URI의 길이 제한

스펙상으로는 길이제한 X

구현상으로는 제한존재

특히 Internet Explorer 2038바이트 제한

# 06 다양한 스키마

URI 스키마의 공식적인 목록은 IANA(Internet Assigned Numbers Authority)에 있고 2010년 70개 등록. (http://j.mp/uri_schemes)

HTTP에 대응한 http 스키마가 먼저 탄생. 그 뒤 다양한 프로토콜에 대응한 스키마가 등록되어 감

URI 스키마?

# 07 URI 구현에서 주의할 점들

웹 서비스와 웹 API를 구현함에 있어서 URI의 스펙상 주의해야 할 것은 상대 URI의 해석과 %인코딩을 다루는 2가지 사항입니다.

- 가능한 절대 URI를 사용하는 것이 클라이언트에게 도움이 됨
- URI에 ASCII 이외의 문자를 넣을 경우에는 %인코딩의 문자 인코딩으로 될 수 있는 한 UTF-8을 이용

# URI, URL, URN

### URI는 URL(Uniform Resource Locator)와 URN(Uniform Resource Name)을 총칭하는 이름

- URL은 도메인을 갱신하지 않거나 서버가 장해로 인해 변경되면 액세스 할 수 없게 되는 문제가 있습니다.
- → 그 결과가 URN이며 리소스에 도메인명과는 독립된 이름을 붙일 수 있음.(ex 서적은 ISBN이라는 세계적으로 통일된 ID를 사용한다. urn:isbn:9784774142043)
- URI는 URL과 URN의 2개의 ID 체계를 합한 총칭

### URN은 리소스를 취득할 수 없다.

URL과 같이 서버명과 프로토콜명이 들어있지 않기 때문에 URI로서 리소스를 가져올 수 없습니다.

### URL은 충분히 영속적이 되었다.

웹의 가치가 향상되고, 리소스와 URL은 가능한 한 영속적으로 엑세스 할 수 있도록 해야한다는 사고과 확산됨에 따라 URN을 사용할 것까지도 없는 경우가 많아졌다.

URL은 Locator 리소스의 위치를 나타내는 것

URN은 Name 리소스의 이름을 나타내는 것

URI는 Identifier 리소스를 식별하는 것