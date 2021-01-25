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
- XML의 내용
- 텍스트의 내용
- 텍스트 이외의 내용

# 04 피드 - 엔트리의 집합

# 05 Atom의 확장

# 06 Atom을 활용한다