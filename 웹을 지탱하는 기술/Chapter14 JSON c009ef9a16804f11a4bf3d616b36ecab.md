# Chapter14 JSON

지금까지는 HTML, microformats, Atom이라는 XML계의 리소스 표현을 설명하였다.
마지막으로 소개할 포맷은 더욱 가벼운 데이터 표현형식인 JSON입니다. 
JSON은 XML처럼 문서를 마크업하기에는 적합하지 않지만, 해시나 배열같이 프로그램 언어가 다루기 용이한 데이터 구조를 기술할 수 있다는 것을 그 특징으로 합니다.

# 01 JSON이란 무엇인가

JSON(JavaScript Object Notation), 데이터를 기술하기 위한 언어

JavaScript 기법으로 데이터를 기술할 수 있는 점이 가장 큰 특징이며 심플함으로 인해 많은 언어에서 라이브러리를 제공하고 있어 프로그래밍 언어 간에 데이터를 주고 받을 수 있습니다.

웹 서비스에서는 브라우저가 JavaScript를 실행할 수 있어 호환성이 좋고, XML과 비교하면 데이터 표현이 간결하다는 등의 이점이 있어 Ajax 통신에서 데이터 포맷으로 활용되고 있습니다.

# 02 미디어 타입

JSON의 미디어 타입 : 'application/json'

JSON은 스펙 상 UTF-8, UTF-16, UTF-32 중 하나로 인코딩하도록 되어 있기 때문에 최초의 4바이트를 체크하면 자동적으로 문자 인코딩을 특정할 수 있지만, HTTP, 헤더 등의 미디어 타입에서 파라미터로 지정할 수도 있습니다.

```java
Content-Type : application/json; charset=utf-8
```

# 03 확장자

JSON 파일에는 '.json'이라는 확장자를 이용하는 것을 추천합니다. 명시적으로 JSON표현을 얻고자 할 경우에는 URI에 '.json'을 붙이도록 리소스를 설계하면 좋습니다.

- 'json' vs djson' 차이

# 04 자료형

JSON에 마련되어 있는 자료형

- object

- number

- array

- boolean

- string

- null

### Object(오브젝트)

이름과 값의 집합. 이름과 값의 세트를 오브젝트의 '멤버'라고 부릅니다.
JavaScript에서는 멤버의 이름에 식별자와 수치도 가능하지만, JSON에서는 멤버의 이름은 항상 문자열입니다. 멤버의 값은 문자열과 수치는 물론 오브젝트나 배열 등 JSON의 자료형이라면 무엇이든 들어갈 수 있습니다.

### array(배열)

배열은 순서를 가진 값의 집합입니다. 0개 이상의 값을 가질 수 있습니다.

### string(문자열)

JSON의 문자열은 반드시 이중인용부호(")로 감싸줍니다. 어떤 문자도 \uXXXX라는 형식(4자리의 16진수로 표현한 Unicode번호)으로 에스케이프?할수 있습니다. 또한 백슬래시(\)와 줄바꿈 같은 제어문자는 특수한 에스케이프 표기법을 가지고 있습니다.

```java
//단순 문자열
"가나다"
//에스케이프 표기한 "가나다"라는 문자열
"\uAC00\uB098\uB2E4"
//백 슬래시(\\)와 줄바꿈(\n)을 포함하는 문자열
"foo\\bar\n"
```

### number(수치)

JSON의 수치에는 정수와 부동소수점이 모두 포함됩니다. 수치의 표기는 10진 표기로 한정합니다

```java
//정수값
10
//음수값
-100
//소수점이 붙은 수치
30.1
//지수
1.0e-10
```

### boolean(부울린)

값이 참이냐 거짓인가를 취하는 부울린형은 **리터럴**로 준비되어 있습니다.

'true'와 'false'와 같이 반드시 모두 소문자로 기술해야 합니다.

### null

null값도 리터럴로 준비되어 있습니다. 반드시 'null'이라고 소문자로 써야합니다.

### 일시

JSON에서 기본적으로 제공하는 자료형에는 일시는 없습니다.

```java
//UNIX 시간 : 1970년 1월 1일 0시 0분 0초 부터 경과한 시간을 초단위로 나타냅니다.
123456789 -> 2009년 2월 14일 8시 31분 30초

//JavaScript의 Date 클래스의 toString()메서드로 출력한 문자열
"Mon Nov 01 2010  05:43:35 GMT+0900"

//But... Internet Explorer 8에서는
"Mon Nov 01 05:43:35 UTC+0900 2010"

//그러므로 더 표준적인 일시로 저장하는 것이 바람직하며 ISO 8601 포맷 일시의 예
"2010-11-01T05:43:35+09:00"
```

### 링크

JSON에서 링크를 구현하기 위해서는 단순히 URI를 문자열 값으로 가지는 것이 가장 간편합니다.

URI는 절대 URI로 해두는 편이 무난합니다.

```java
{
	"href" : "http://example.com/foo/bar"
}
```

# 05 JSON에 의한 크로스 도메인 통신

JSONP(JSON with Padding)을 이용하여 JSON으로 리소스 표현을 제공할 수 있습니다.

### 크로스 도메인 통신의 제한

- 배경
Ajax에서 이용하는 XMLHttpRequest라는 JavaScript의 모듈은 보안상의 제한으로 인해 JavaScript 파일을 가져왔던 동일한 서버하고만 통신할 수 있습니다.
JavaScript가 있는 서버와 다른 서버가 통신할 수 있다면, 브라우저에서 입력한 정보를 사용자가 모르는 사이에 다른 서버(부정한 서버)로 전송할 수 있게 되기 때문입니다.
이렇게 불특정 다수의 도메인에 속하는 서버에 액세스하는 것을 '크로스 도메인 통신'이라고 부립니다. 복수 도메인의 서버와 통신할 수 없고, 단일 도메인과 통신해야 한다는 것은 커다란 제약입니다.

### <script> 요소에 의한 해결

```java
//XMLHttpRequest에서는 크로스 도메인 통신을 할 수 없지만,
//HTML의 <script>요소를 이용하면, 복수의 사이트에서 JavaScript 파일을 읽을 수 있음
//<script> 요소는 역사적인 이유에서 일반적으로 브라우저의 보안제한을 받지 않습니다.
<html xmlns="http://www.w3.org/1999/xhtml">
	<head>
		<script src="http://example.jp/map.js"></script>
		<script src="http://example.com/zip.js"></script>
		...
	</head>
	...
</html>
```

- 역사적인 이유?

### 콜백 함수를 활용하는 JSONP

JSONP는 브라우저의 이런 성질을 이용해 크로스 도메인 통신을 구현하는 방법입니다.

JSONP에서는 오리지널 JSON을 클라이언트가 지정한 콜백 함수명으로 랩핑하여 도메인이 다른 서버로부터 데이터를 취득합니다.

![Chapter14%20JSON%20c009ef9a16804f11a4bf3d616b36ecab/Untitled.png](Chapter14%20JSON%20c009ef9a16804f11a4bf3d616b36ecab/Untitled.png)

```java
<html xmlns="http://www.w3.org/1999/xhtml">
	<head>
		<title>크로스 도메인 통신의 예</title>
	</head>
	<body>
		<script type="text/javascript">
			function foo(zip){
				alert(zip("zipcode"));
			}
		</script>
		<script src="http://zip.ricollab.jp/1120002.json?callback=foo"></script>
	</body>
</html>

//요청
GET /11220002.json?callback=foo HTTP/1.1
Host: zip.ricollab.jp

//응답
HTTP/1.1 200 OK
Content-Type: application/javascript

foo({
	"zipcode":"1120002",
	"address":{
		"prefecture":"도쿄도",
		"city":"분쿄구",
		"town":"코이시카와"
	},
	"yomi":{
		"prefecture":"토우쿄우토",
		"city":"분쿄우쿠",
		"town":"코이시카와"
	}
});
```

- 다른 예 찾기

# 06 하이퍼미디어 포맷으로서의 JSON

JSON은 JavaScript를 기반으로 한 심플한 데이터 포맷입니다.

XML과 비교하면 기술의 중복도가 낮다는 이점이 있기 때문에, 주로 데이터 표현의 포맷으로 이용되고 있습니다. 또한 JSONP를 사용하면 크로스 도메인 통신이 가능하기 때문에 Ajax에서는 필수 기술이 되어 있습니다.

JSON은 데이터 기술에 적합한 포맷이지만, 리소스를 표현하는 포맷으로서 생각한 경우는 하이퍼미디어 포맷으로서의 측면을 잊어서는 안됩니다. JSON을 하이퍼미디어 포맷으로 사용하기 위해서는 링크를 표현하는 멤버를 정확히 넣을 필요가 있습니다. 다른 리소스와의 관계를 고려하여 링크를 확실히 넣는 설계를 하는 것이 중요합니다.