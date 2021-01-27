# Chapter12 Atom

Atom은 블로그 등의 갱신정보를 보내기 위한 피드로 알려져 있지만,
실제로는 폭넓은 분야에서 응용이 가능한 범용 XML 포맷입니다.

# 01 Atom이란 무엇인가?

Atom Syndication Format은 XML 포맷입니다.

- 목적

    RSS의 스펙이 난립하여 혼란 → 확정성 있는 피드의 표준 포맷을 책정하기 위해 Atom 탄생

    RSS는 주로 블로그의 신착정보를 전달하는 피드의 목적으로 이용되었지만

    Atom은 블로그뿐만 아니라 검색엔진이나 사진관리 등 다양한 웹 서비스의 웹 API롤 이용할 수 있습니다.

- RSS란? Rich Site Summary or Really Simple Syndication

    뉴스나 블로그 사이트에서 주로 사용하는 콘텐츠 표현 방식

# 02 Atom의 리소스 모델

![Chapter12%20Atom%20a2797ff7c66c47f2b638bfca48b3a84c/Untitled.png](Chapter12%20Atom%20a2797ff7c66c47f2b638bfca48b3a84c/Untitled.png)

### 멤버 리소스

Atom에서 최소 리소스 단위, 예를 들어 블로그 기사 하나하나가 멤버 리소스가 됨.

멤버 리소스의 구분(엔트리로 표현, 엔트리 이외로 표현)

- 엔트리 리소스 : 텍스트와 XML로 표현, <entry> 요소로 나타냄.
- 미디어 리소스 : 텍스트 이외의 데이터, 미디어 링크 엔트리로 표현

→ 위 그림 12.1 오류?

### 컬렉션 리소스

복수의 멤버 리소스를 포함하는 리소스, 계층화할 수 없다.

<feed> 요소로 표현하고, 이 표현을 피드(Feed)라고 한다.

### 미디어 타입

Atom의 미디어 타입은 'application/atom+xml'입니다. 엔트리와 피드를 명시하고 싶을 때는 type 파라미터로 'entry', 'feed'를 지정합니다.

### 확장자

Atom에는 '.atom'이라는 확장자를 이용하는 것이 권장되고 있습니다.

### 이름공간

Atom의 이름공간은 [https://www.w3.org/2005/atom](https://www.w3.org/2005/atom)입니다.?

# 03 엔트리 - Atom의 최소단위

```html
//엔트리 예
<entry xmlns="http://www.w3.org/2005/Atom">
	<id>tag:example.com,2021-01-25:entry:1234</id>
	<title>테스트 일기</title>
	<updated>2021-01-25T22:19:55Z</updated>
	<link href="http://example.com/1234"/>
	<content>일기.. 오늘 영상이었는데! 추웠다.</content>
</entry>
```

### 메타 데이터

- ID

    <id> 요소의 내용은 이 엔트리를 유일하게 나타내는 URI 형식의 ID
    http 스키마의 URI도 이용할 수 있지만 
    Atom에서는 HTTP와는 독립된 tag스키마의 URI가 자주 이용되고 있음

    ```html
    <id>tag:example.com, 2021-01-25:entry:1234</id>
    //tag 스키마 구조
    tag:{DNS명 또는 메일주소},{일자}:{임의의 문자열}
    ```

- 타이틀과 개요

    ```html
    <entry xmlns="http://www.w3.org/2005/Atom">
    <title>Atom에 대해서</title>
    	<summary type="xhtml">
    		<div xmlns="http://www.w3.org/1999/xhtml">
    			<p>이 엔트리에서는 <a href="http://ko.wikipedia.org/wiki/Atom">
    				Atom</a>에 대해서 설명합니다.
    			</p>
    		</div>
    	</summary>
    ...
    </entry>
    ```

- 저자와 공헌자

    ```html
    <author>
    	<name>야마모토 요헤이</name>
    	<uri>http:yohei-y.blogspot.com</uri>
    	<email>yoyoyo@gmail.com</email>
    </author>
    <contributor>
    	<name>이나오 쇼토쿠</name>
    </contributor>
    ```

    <name> 요소 : 자연언어로 기술한 이름, 필수

    <uri>요소 : 사람과 관련된 URI, 임의

    <email> 요소 : 사람의 이메일 주소, 임의

- 공개일시와 갱신일시
- 카테고리
- 링크

![Chapter12%20Atom%20a2797ff7c66c47f2b638bfca48b3a84c/Untitled%201.png](Chapter12%20Atom%20a2797ff7c66c47f2b638bfca48b3a84c/Untitled%201.png)

### 엔트리의 내용

- 플레인 텍스트, 이스케이프된 HTML, XHTML

    ```html
    //플레인 텍스트의 예
    <entry xmlns="http://www.w3.org/2005/Atom">
    	...
    	<content type="text">단순한 텍스트가 들어감</content>
    </entry>

    //HTML의 예
    <entry xmlns="http://www.w3.org/2005/Atom">
    	...
    	<content type="html">&lt;p>에스케이프된 HTML &lt;br>이 들어갑니다.&lt;/p></content>
    </entry>

    //XHTML의 예
    <entry xmlns="http://www.w3.org/2005/Atom">
    	...
    	<content type="xhtml">
    		<div xmlns="http://www.w3.org/1999/xhtml">
    			<p>XHTML이 그대로 </br>들어갑니다</p>		
    		</div>
    	</content>
    </entry>
    ```

    <content> 의 type 속성에는 이 3가지 이외의 미디어 타입도 지정 가능

- XML의 내용 : 미디어 타입이 application/xml, text/xml 또는 서브 타입이 '+xml'로 끝날 경우

    SVG(Scalable Vector Graphics) 문서 : 벡터 이미지를 XML로 표현하는 포맷

    ```html
    <entry xmlns="http://www.w3.org/2005/Atom">
    	...
    	<content type="image/svg+xml">
    		<svg xmlns="http://www.w3.org/2000/svg" 
    				 xml:space="preserve" width="5.5in" height=".5in">
    			<text style="fill:red;" y="15">This is SVG</text>
    		</svg>
    	</content>
    </entry>
    ```

- 텍스트의 내용

    엔트리 리소스는 XML 이외의 텍스트도 가질 수 있습니다.

    ```html
    //CSV를 내용으로 가지는 엔트리 리소스
    <entry xmlns="http://www.w3.org/2005/Atom">
    	...
    	<content type="text/csv">
    		상품명, 가격, 개수
    		사과, 150, 1
    		귤, 300, 5
    	</content>
    </entry>
    ```

- 텍스트 이외의 내용

    이미지와 같은 바이너리 데이터를 <content>에 넣을 때 Base64로 인코딩합니다.

    ```html
    <entry xmlns="http://www.w3.org/2005/Atom">
    	...
    	<content type="image/jpeg">
    		Base64로 인코딩한 JPEG 이미지
    	</content>
    </entry>
    ```

    Base64로 인코딩한 데이터는 인코딩/디코딩 처리에 오버헤드가 걸리고 XML문서가 거대해지기 때문에 지나치게 큰 파일에는 적합하지 않습니다.

    용량이 큰 이미지나 음성, 동영상 등의 바이너리를 <content> 요소에 넣을 때는 src 속성을 사용해 외부 리소스를 참조합니다.

    ```html
    <entry xmlns="http://www.w3.org/2005/Atom">
    	...
    	<content type="image/jpeg" src="http://example.com/image/foo_bar.jpg"/>
    </entry>
    ```

    src 속성으로 외부 리소스를 참조하고 있는 엔트리 리소스를 '미디어 링크 엔트리'라고 부릅니다.
    또한 미디어 링크 엔트리가 참조하고 있는 이미지 리소스를 '미디어 리소스'라고 부릅니다.

# 04 피드 - 엔트리의 집합

멤버 리소스를 여러 개 가지는 컬렉션 리소스의 표현이 피드(feed)

```html
<feed xmlns="http://www.w3.org/2005/Atom">
	<id>tag:example.com,2010:feed</id>
	<title>일기</title>
	<author>
		<name>야근맨</name>
	</author>
	<updated>2015-08-31T13:11:54Z</updated>
	<link rel="alternate" href="http://example.com"/>
	<link rel="self" href="http://example.com/feed"/>
	<entry>
		<id>tag:example.com,2021-01-26:entry:2345</id>
		<title>일기2</title>
		<updated>2021-01-26T23:04:55Z</updated>
		<link href="http://example.com/2345"/>
		<content>야근 너무 싫어</content>
	</entry>
	<entry>
		<id>tag:example.com,2021-01-26:entry:2345</id>
		<title>일기3</title>
		<updated>2021-01-26T23:04:55Z</updated>
		<link href="http://example.com/2345"/>
		<content>칼퇴하고 싶어</content>
	</entry>
</feed>
```

### 엔트리와 공통 메타 데이터

<feed> 요소는 엔트리와 같은 메타 데이터를 가질 수 있습니다.

<id>,<title>,<author>,<updated>

### 피드의 독자적인 메타 데이터

- 서브타이틀 : 타이틀에서 다 설명하지 못한 설명을 기술

    ```html
    <subtitle type="text">피드의 개요</subtitle>
    ```

- 생성 프로그램 : 피드를 생성한 프로그램의 정보를 나타냅니다.

    ```html
    <generator uri="http://wordpress.org/" version="2.8.6">
    	WordPress
    </generator>
    ```

- 아이콘 : favicon(웹 사이트와 웹 페이지에 관련된 아이콘)

    ```html
    <icon>http://blog.example.com/image/favicon.ico</icon>
    ```

- 로고 : 이 피드를 상징하는 이미지를 지정

    ```html
    <logo>http://blog.example.com/image/logo.png</logo>
    ```

# 05 Atom의 확장

```html
//예
<feed xmlns="http://www.w3.org/2005/Atom"
			xmlns:os="http://a9.com/-/spec/opensearch/1.1">
	<title>'REST'의 검색결과</title>
	<id>http://example.com/search?q=REST</id>
	<author><name><Example CO.Ltd</name></author>
	<os:totalResults>3392</os:totalResults>
	<os:startIndex>21</os:startIndex>
	<os:itemsPerPage>10</os:itemsPerPage>
	<entry>
		<title>REST 입문</title>
		<id>http://yohei-y.blogspot.com/2005/04/rest_23.html</id>
		<link href="http://yohei-y.blogspot.com/2005/04/rest_23.html"/>
	</entry>
	...
</feed>
```

3개의 확장요소를 포함하고 있음. 'os'라는 접두어가 나타내는 OpenSearch의 이름공간에 속함

OpenSearch는 검색결과의 표준 포맷으로서 Atom과는 완전히 별도로 표준화되어 있는 스펙입니다.

Atom의 이름공간 이외의 확장요소는 '외부 마크업'이라고 부릅니다.

### Atom Threading Extenstions - 스레드를 표현한다.

- 이름공간

    ```html
    //이름공간
    http://purl.org/syndication/thread/1.0

    //<thr:in-reply-to> 요소
    	//ref 속성 : <thr:in-reply-to> 요소가 있는 엔트리가 참조하는 엔트리의 ID. 필수
    	//href 속성 : 이 엔트리의 표현을 얻기 위한 URI. 임의
    	//type 속성 : href 속성에서 참조하는 리소스의 미디어 타입. 임의
    	//source 속성 : ref 속성에서 참조하는 엔트리를 포함하는 피드의 URI. 임의
    <feed xmlns="http://www.w3.org/2005/Atom"
    			xmlns:thr="http://purl.org/syndication/thread/1.0">
    	<id>http://bbs.example.com/feed</id>
    	<title>게시판</title>
    	<updated>2010-07-28T12:00:OOZ</updated>
    	<link rel="alternate" href="http//bbs.exmaple.com/"/>
    	<link rel="self" href="http://bbs.example.com/feed"/>
    	<entry>
    		<id>tag:bbs.example.com.2010:1</id>
    		<title>XML과 REST에 대해서</title>
    		<updated>2010-07-27T12:12:12Z</updated>
    		<link href="http://bbs.example.com/entries/1"/>
    		<summary>XML과 REST의 관계란?</summary>
    	</entry>
    	<entry>
    		<id>tag:bbs.example.com.2010:1.1</id>
    		<title>Re:XML과 REST에 대해서</title>
    		<updated>2010-07-28T12:00:00Z</updated>
    		<link href="http://bbs.example.com/entries/1/1"/>
    		<link rel="related" href="http://bbs.example.com/entries/1"/>
    		<thr:in-reply-to
    			ref="tag:example.org.2010:1"
    			type="application/xhtml+xml"
    			href="http://bbs.example.com/entries/1"/>
    		<summary>직접적인 관계는 없습니다.</summary>
    		</entry>
    </feed>

    //replies 링크 관계와 thr:count 속성/thr:update 속성
    	//rel 속성 : replies라는 값. 필수
    	//href 속성 : 이 엔트리에 대한 답글이 있는 리소스의 URI. 필수
    	//type 속성 : href에서 참조하는 f소스의 미디어 타입. 생략시 디폴트는 application/atom_xml이 됨. 임의
    	//thr:count 속성 : href로 참조하는 리소스의 총 답글수(양수값). 임의
    	//thr:updated 속성 : 이 엔트리에 대한 답글이 마지막으로 갱신된 시각. 임의
    <entry xmlns="http:://www.w3.org/2005/Atom"
    			 xmlns:thr="http://purl.org/syndication/thread/1.0">
    	<id>tag:blog.example.com,2010:1</id>
    	<title>최초의 블로그 포스팅</title>
    	<author><name>야마모토 요헤이</name></author>
    	<updated>2010-08-24T10:00:00Z</updated>
    	<link href="http://blog.exmaple.jp/entries/1">
    	<link rel ="replies"
    				href="blog.example.com/entries/1/commentsfeed"
    				type="application/atom+xml"
    				thr:count="10"
    				thr:updated="2010-08-24T10:20:00Z"/>
    	<thr:total>15</thr:total>
    	<summary>최초의 기사입니다.</summary>
    </entry>

    //<thr:total> 요소 : 이 엔트리에 대한 답글의 총 수
    ```

### Atom License Extension - 라이선스 정보를 표현한다.

- 이름공간, <link rel="license" href="http://creativecommons.org/Licenses/by-nc/3.0/"/>
- 복수 라이선스 : <entry>요소와 <feed> 요소는 라이선스 링크를 여러개 가질 수 있음
- 라이선스를 지정하지 않는 경우 : 특정 라이선스를 지정하지 않는다는 것을 명시, 
unspecified 링크 지정
- Atom의 <rights>요소와의 관계 : <rights>요소는 사람이 읽고 이해하는 것을 목적

### Feed Paging and Archiving - feed를 분할한다.

- 이름공간, http://purl.org/syndication/history/1.0, fh라는 접두어는 이 이름공간에 연결
- 피드의 종류 : 완전 피드, 페이지화 피드, 아카이브된 피드
    - 완전 피드
    - 페이지화 피드

        ![Chapter12%20Atom%20a2797ff7c66c47f2b638bfca48b3a84c/Untitled%202.png](Chapter12%20Atom%20a2797ff7c66c47f2b638bfca48b3a84c/Untitled%202.png)

    - 아카이브된 피드

        ![Chapter12%20Atom%20a2797ff7c66c47f2b638bfca48b3a84c/Untitled%203.png](Chapter12%20Atom%20a2797ff7c66c47f2b638bfca48b3a84c/Untitled%203.png)

### OpenSearch - 검색결과를 표현한다.

- 4가지 파트 구분
    - Description Document : 검색엔진이 제공하는 검색기능을 프로그램이 이해할 수 있는 형식으로 기술하는 XML형식
    - URL Template Syntax : 검색결과 리소스를 표현하는 URL의 검색 쿼리 부분을 파라미터화 하는 스펙
    - Query Element : URL Template Syntax에서 사용하는 검색 파라미터를 기술하는 XML 요소. Description Document와 검색결과 모두 이용한다.
    - Response Element : 검색결과를 Atom과 RSS 2.0 등의 피드 형식으로 표현하기 위한 확장 요소

- 이름공간, [http://a9.com/-/opensearch/1.1/](http://a9.com/-/opensearch/1.1/), os라는 접두어는 이 이름공간에 연결
- <os:totalResults> 요소 : 검색결과 총 수를 표현하는 양의 정수
- <os:startIndex> 요소 : 이 피드에 들어있는 검색결과의 최초 엔트리의 인덱스
- <os:itemsPerPage> 요소 : 한 피드에 들어가는 최대 검색결과 엔트리 수
- <os:Query> 요소 : 검색 쿼리를 나타냄
- 링크 관계 : 표 12.2의 링크 관계를 사용해 검색결과를 페이지화 할 수 있음

# 06 Atom을 활용한다

Atom은 타이틀, 저자, 갱신일시라는 기본적인 메타 데이터를 갖춘 리소스 표현을 위한 포맷

다양한 애플리케이션용 확장이 준비된 포맷

블로그 이외에도 팟 캐스트, 사진관리, 검색엔진등에서 사용

XML의 이름공간 구조를 사용하여 Atom에 독자적인 요소를 추가하고 있다.

다음장의 AtomPub와 함께 사용하면 리소스의 표현뿐만 아니라 HTTP를 활용한 조작도 가능해 진다.