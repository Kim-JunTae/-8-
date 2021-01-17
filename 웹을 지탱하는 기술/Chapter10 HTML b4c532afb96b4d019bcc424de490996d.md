# Chapter10 HTML

하이퍼미디어 포맷으로서의 HTML

### 복습

하이퍼미디어란?

텍스트와 이미지, 음성, 영상 등 다양한 미디어를 하이퍼링크(HyperLink)로 연결해 구성한 시스템
이전의 미디어 : 책, 영화 → 선형적으로 처음부터 순서대로 읽거나 시청
하이퍼 미디어 : 비선형적으로 사용자가 스스로 링크를 선택하여 정보를 얻습니다.
링크란 하이퍼미디어에 있어서 정보끼리 연결하는 구조
웹은 하이퍼미디어의 한 예

# 01 HTML이란 무엇인가

- HTML = Hypertext Markup Language(태그(Tag)로 문서의 구조를 표현하는 컴퓨터 언어)
- 구조화 문서(Structured Document) : 마크업 언어를 이용한 마크업 구조를 가진 문서
- 구조화  문서를 위한 마크업 언어에는 원래 SGML(HTML 4.01까지의 버전은 SGML 기반)
그러나 복잡한 문법으로 처리 프로그램으로 생성하기 힘듬
→ XML, 스펙이 심플
- XHTML 1.0 (SGML 베이스에서 XML 베이스로 바꾼 스펙)

**HTML 5**
- <section> 요소와 내비게이션 블록용 <nav> 요소 등을 추가
- JavaScript 애플리케이션을 위한 API가 신규로 추가되고 로컬 스터리지의 이용과 드래그 앤 드롭 등이 가능
**HTML5에 대한 요약 설명 조사**

**Internet Explorer와 XHTML**
XHTML 1.0의 미디어 타입으로 text/html을 이용하는 경우도 있다.
Internet Explorer가 application/xhtml+xml을 제대로 다루지 못해 파일저장을 확인하는 다이얼 로그를 내보내기 때문
**현재는? 조사하자**

# 02 미디어 타입

HTML의 미디어 타입에는 'text/html'과 'application/xhtml+xml' 2 종류가 있습니다.

- 'text/html'은 SGML 베이스의 HTML
- 'application/xhtml+xml'은 XML 베이스의 XHTML

어느 쪽을 사용하더라도 charset 파라미터를 통해 문자 인코딩을 지정할 수 있습니다.

# 03 확장자

HTML에서는 '.html' 또는 '.htm' 이라는 확장자를 이용

- htm은 구형 OS의 제약에 의한 것으로 현재는 '.html'을 사용하는 편이 일반적

# 04 XML의 기초지식

HTML/XHTML의 서식을 배우기 위해서는 메타언어인 XML의 스펙을 알아야 합니다.

### XML의 트리구조

![Chapter10%20HTML%20b4c532afb96b4d019bcc424de490996d/Untitled.png](Chapter10%20HTML%20b4c532afb96b4d019bcc424de490996d/Untitled.png)

- 요소
    - 요소의 트리구조
    - 빈 요소
- 속성
- 실제 참조와 문자 참조
    - 실제 참조

    ![Chapter10%20HTML%20b4c532afb96b4d019bcc424de490996d/Untitled%201.png](Chapter10%20HTML%20b4c532afb96b4d019bcc424de490996d/Untitled%201.png)

    - 문자 참조 : Unicode 번호로 문자를 지정함
- 코멘트 : <! - - 코멘트 내용 - ->
- XML 선언 : XML의 버전과 문자 인코딩 방식을 지정

    ```xml
    **<?xml version="1.0" encoding="utf-8"?>**
    ......
    ```

- 이름공간 : 복수의 XML 포맷을 조합할 때, 이름의 충돌을 방지할 목적
    - 요소의 이름공간

        xmlns:접두어="이름공간명"

    ```xml
    <html **xmlns="http://www.w3.org/1999/xhtml"
    			xmlns:atom="http://www.w3.org/2005/Atom"**>
    <head>
    	<**link** rel="stylesheet" href="base.css"/>
    	<**atom:link** rel="enclosure" href="attachment.mp3"/>
    </head>
    ...
    </html>
    ```

    - 속성의 이름공간

        로컬 속성 : 접두 X

        글로벌 속성 : 접두어 O

    ```xml
    <entry **xmlns="http://www.w3.org/2005/Atom"
    			 xmlns:thr="http://purl.org/sydication/thread/1.0"**>
    	<**link** href="blog.example.com/entries/1/commentsfeed"
    				thr:count="10">
    </entry>
    ```

    Atom Threading Extensions???

# 05 HTML의 구성요소

### 헤더

![Chapter10%20HTML%20b4c532afb96b4d019bcc424de490996d/Untitled%202.png](Chapter10%20HTML%20b4c532afb96b4d019bcc424de490996d/Untitled%202.png)

### 바디

- 블록 레벨 요소 Block Level Element

    문서의 단락과 표제 등 어느 정도 큰 덩어리를 표현

    ![Chapter10%20HTML%20b4c532afb96b4d019bcc424de490996d/Untitled%203.png](Chapter10%20HTML%20b4c532afb96b4d019bcc424de490996d/Untitled%203.png)

- 인라인 요소 Inline Element

    블록 레벨 요소 안에 들어가는 요소로  강조나 줄 바꿈, 이미지 삽입 등을 표현

    ![Chapter10%20HTML%20b4c532afb96b4d019bcc424de490996d/Untitled%204.png](Chapter10%20HTML%20b4c532afb96b4d019bcc424de490996d/Untitled%204.png)

- 공통 속성

    HTML의 모든 요소는 id 속성과 class 속성을 가질 수 있습니다.

# 06 링크

# 07 링크 관련 - 링크의 의미를 지정한다.

# 08 하이퍼미디어 포맷으로서의 HTML