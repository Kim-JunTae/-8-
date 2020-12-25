# Chapter2 동작 파라미터화 코드 전달하기

이 장의 내용
▪️변화하는 요구사항에 대응
▪️동작 파라미터화
▪️익명 클래스
▪️람다 표현식 미리보기
▪️실전 예제 : Comparator, Runnable, GUI

### 동작 파라미터화(behavior parameterization)

동작 파라미터화를 이용하면 자주 바뀌는 요구사항에 효과적으로 대응할 수 있다.

아직은 어떻게 실행할 것인지 결정하지 않은 코드 블록을 의미

---

## 2.1 변화하는 요구사항에 대응하기

예제 코드를 점점 개선하여 유연한 코드를 만드는 사례
ex) 농장 재고목록 애플리케이션에 리스트에서 **녹색 사과 필터링 기능** 추가

### 2.1.1 첫 번째 시도 : 녹색사과 필터링

```java
public static List<Apple> filterGreenApples(List<Apple> inventory) {
	List<Apple> result = new ArrayList<>(); // 사과 누적 리스트
	for(Apple apple : inventory) {
		**if("green".equals(apple.getColor()) { // 녹색 사과만 선택**
			result.add(apple);
		}
	}
	return result;
}
```

- 다양한 색을 필터링 하려면?
→ '비슷한 코드를 구현한 다음에 추상화하라'

### 2.1.2 두 번째 시도 : 색을 파라미터화

```java
public static List<Apple> filterApplesByColor(List<Apple> inventory, String color){
	List<Apple> result = new ArrayList<>();
	for(Apple apple : inventory) {
		if(apple.getColor().equals(color)){
			result.add(apple);
		}
	}
	return result;
}
```

- 색 이외에도 무게로도 구분하고 싶다면?

    → 다양한 요구사항의 반영을 위해서 메서드 전체 구현 고려

### 2.1.3 세 번째 시도 : 가능한 모든 속성으로 필터링

- 모든 속성을 메서드 인수로 추가하면 어떤 문제가 생길까

```java
public static List<Apple> filterApplesByColor(List<Apple> inventory
																							, String color, int weight, boolean flag){
	List<Apple> result = new ArrayList<>();
	for(Apple apple : inventory) {
		if((flag && apple.getColor().equals(color)) || 
				(!flag && apple.getWeight() > weight)){
			result.add(apple);
		}
	}
	return result;
}
```

- 유연하지 않다. 동작 파라미터화를 통해 유연성을 얻어보자!

## 2.2 동작 파라미터화

- 선택조건의 결정, 사과의 어떤 속성에 기초해서 불린값을 반환
→ 프리디케이트(predicate, 찬반형, 불린을 반환하는 함수)
- 선택조건을 결정하는 인터페이스를 정의하자

```java
public interface ApplePredicate {
	boolean test(Apple apple);
}

//무거운 사과만 선택
public class AppleHeavyWeightPreicate implements ApplePredicate {
	public boolean test(Apple apple) {
		return apple.getWeight() > 150;
	}
}

//녹색 사과만 선택
public class AppleGreenPredicate implements ApplePredicate {
	public boolean test(Apple apple) {
		return "green".equals(apple.getColor());
	}
}
```

- 사과 선택 전략을 캡슐화. 조건에 따라 filter 메서드가 다르게 동작
- 이를 전략 디자인 패턴(Strategy design pattern)이리 한다.
각 알고리즘(전략이라 불리는)을 캡슐화하는 알고리즘 패밀리를 정의해둔 다음에 
런타임에 알고리즘을 선택하는 기법이다.
ApplePredicate가  알고리즘 패밀리, 
AppleHeavyWeightPredicate와 AppleGreenPredicate가 전략이다.
- ApplePredicate가 다양한 동작을 할 수 있도록 filterApple에서 ApplePredicate 객체를 받아 애플의 조건을 검사하도록 고쳐야한다.
이렇게 **동작 파라미터화,** 즉 메서드가 다양한 동작(또는 전략)을 받아서 내부적으로 다양한 동작을 수행할 수 있다.

### 2.2.1 네 번째 시도 : 추상적 조건으로 필터링

```java
public static List<Apple> filterApples(List<Apple> inventory, ApplePredicate p){
	List<Apple> result = new ArrayList<>();
	for(Apple apple : inventory){
		if(p.test(apple)){  // 프레디케이트 객체로 사고가 검사 조건을 캡슐화 했다.
			result.add(apple);
		}
	}
	return result;
}

public class AppleRedAndHeavyPredicate implements ApplePredicate {
	public boolean test(Apple apple){
		return "red".equals(apple.getColor()) && apple.getWeight() > 150;
	}
}

List<Apple> redAndHeavyApples = filterApples(inventory, new AppleRedHeavyPredicate());
```

**코드/동작 전달하기**

- filterApples 메서드의 동작을 파라미터화함!

**한 개의 파라미터, 다양한 동작**

![Chapter2%20%E1%84%83%E1%85%A9%E1%86%BC%E1%84%8C%E1%85%A1%E1%86%A8%20%E1%84%91%E1%85%A1%E1%84%85%E1%85%A1%E1%84%86%E1%85%B5%E1%84%90%E1%85%A5%E1%84%92%E1%85%AA%20%E1%84%8F%E1%85%A9%E1%84%83%E1%85%B3%20%E1%84%8C%E1%85%A5%E1%86%AB%E1%84%83%E1%85%A1%E1%86%AF%E1%84%92%E1%85%A1%E1%84%80%E1%85%B5%204135ddb1c6d64d97a7120db7beb38ec7/Untitled.png](Chapter2%20%E1%84%83%E1%85%A9%E1%86%BC%E1%84%8C%E1%85%A1%E1%86%A8%20%E1%84%91%E1%85%A1%E1%84%85%E1%85%A1%E1%84%86%E1%85%B5%E1%84%90%E1%85%A5%E1%84%92%E1%85%AA%20%E1%84%8F%E1%85%A9%E1%84%83%E1%85%B3%20%E1%84%8C%E1%85%A5%E1%86%AB%E1%84%83%E1%85%A1%E1%86%AF%E1%84%92%E1%85%A1%E1%84%80%E1%85%B5%204135ddb1c6d64d97a7120db7beb38ec7/Untitled.png)

**Q 2-1 유연한 prettyPrintApple 메서드 구현하기**

```java
public static void prettyPrintApple(List<Apple> inventory, ???) {
	for(Apple apple : inventory) {
		String output = ???.???(apple);
		System.out.println(output);
	}
}
```

```java
//답
public interface AppleFormatter{
	String accept(Apple apple);
}

pulbic class AppleFancyFormatter implements AppleFormatter {
	public String accept(Apple apple){
		String characteristic = apple.getWeight() > 150 ? "heavy" : "light";
		return "A " + characteristic + " " + apple.getColor() + " apple";
	}
}

public class AppleSimpleFormatter implements AppleFormatter {
	public String accept(Apple apple) {
		return "An apple of " + apple.getWeight() + "g";
	}
}

public static void prettyPrintApple(List<Apple> inventory, AppleFormatter formatter) {
	for(Apple apple : inventory) {
		String output = formatter.accept(apple);
		System.out.println(output);
	}
}

//-> 실행
prettyPrintApple(inventory, new AppleFancyFormatter());
prettyPrintApple(inventory, new AppleSimpleFormatter());
```

## 2.3 복잡한 과정 간소화

```java
// 동작의 파라미터화 : 프레디케이트로 사과를 필터링

// 무거운 사과를 선택하는 프레디케이트
public class AppleHeavyWeightPredicate implements ApplePredicate {
	public boolean test(Apple apple){
		return apple.getWeight() > 150;
	}
}

//녹색 사과를 선택하는 프레디 케이트
public class AppleGreenColorPredicate implements ApplePredicate {
	public boolean teste(Apple apple){
		return "green".equals(apple.getColor());
	}
}

public class FilteringApples{
	public static void main(String...args) {
		List<Apple> inventory = Arrays.asList(new Apple(80, "green"),
																					new Apple(155, "green"),
																					new Apple(120, "green"));
		
		List<Apple> heavyApples = filterApples(inventory, new AppleHeavyWeightPredicate());
		List<Apple> greenApples = filterApples(inventory, new AppleGreenColorPredicate());

	}

	public static List<Apple> filterApples(List<Apple> inventory, ApplePredicate p) {
		List<Apple> result = new ArrayList<>();
		for(Apple apple : inventory){
			if(p.test(apple)){
				result.add(apple);
			}
		}
		return result;
	}
}
```

- 익명 클래스(anonymous class) 기법 : 자바에서 클래스의 선언과 인스턴스화를 동시에 수행

### 2.3.1 익명 클래스

자바의 지역 클래스(local class, 블록 내부에 선언된 클래스)와 비슷한 개념

즉석에서 필요한 구현을 만들어서 사용할 수 있는 이름이 없는 클래스

### 2.3.2 다섯 번째 시도 : 익명 클래스 사용

```java
//filterApples 메서드의 동작을 직접 파라미터화
List<Apple> redApples = filterApples(inventory, new ApplePredicate() {
	public boolean test(Apple apple) {
		return "red".equals(apple.getColor());
	}
});

button.setOnAction(new EventHandler<ActionEvent>() {
	public void handle(ActionEvent evnet){
		System.out.println("Woooo a click!!");
	}
});
```

- 익명 클래스로도 부족한 것
    1. 여전히 많은 공간을 차지한다.
    2. 많은 프로그래머가 익명 클래스의 사용에 익숙하지 않다.

**Q 2-2 익명 클래스 문제 : 답 5**

### 2.3.3 여섯 번째 시도 : 람다  표현식 사용

```java
List<Apple> result 
		= filterApples(inventory, (Apple apple) -> "red".equals(apple.getColor());
```

![Chapter2%20%E1%84%83%E1%85%A9%E1%86%BC%E1%84%8C%E1%85%A1%E1%86%A8%20%E1%84%91%E1%85%A1%E1%84%85%E1%85%A1%E1%84%86%E1%85%B5%E1%84%90%E1%85%A5%E1%84%92%E1%85%AA%20%E1%84%8F%E1%85%A9%E1%84%83%E1%85%B3%20%E1%84%8C%E1%85%A5%E1%86%AB%E1%84%83%E1%85%A1%E1%86%AF%E1%84%92%E1%85%A1%E1%84%80%E1%85%B5%204135ddb1c6d64d97a7120db7beb38ec7/Untitled%201.png](Chapter2%20%E1%84%83%E1%85%A9%E1%86%BC%E1%84%8C%E1%85%A1%E1%86%A8%20%E1%84%91%E1%85%A1%E1%84%85%E1%85%A1%E1%84%86%E1%85%B5%E1%84%90%E1%85%A5%E1%84%92%E1%85%AA%20%E1%84%8F%E1%85%A9%E1%84%83%E1%85%B3%20%E1%84%8C%E1%85%A5%E1%86%AB%E1%84%83%E1%85%A1%E1%86%AF%E1%84%92%E1%85%A1%E1%84%80%E1%85%B5%204135ddb1c6d64d97a7120db7beb38ec7/Untitled%201.png)

### 2.3.4 일곱 번째 시도 : 리스트 형식으로 추상화

```java
public interface Predicate<T> {
	boolean test(T t);
}

//형식 파리미터 T 등장!
public static <T> List<T> filter(List<T> list, Predicate<T> p) {
	List<T> result = new ArrayList<>();
	for(T e : list){
		if(p.test(e)){
			result.add(e);
		}
	}
	return result;
}

List<Apple> redApples 
	= filter(inventory, (Apple apple) -> "red.equals(apple.getColor()));

List<String> evenNumbers = filter(numbers, (Integer i) -> i % 2 == 0);
```

## 2.4 실전 예제

지금까지 동작 파라미터화가 변화하는 요구사항에 쉽게 적응하는 유용한 패턴임을 확인했다.

동작 파라미터화 패턴은 동작을 (한 조각의 코드로) 캡슐화한 다음에 메서드로 전달해서 메서드의 동작을 파라미터화한다.

이 절에서는 코드 전달의 개념을 익힐 수 있도록 1. Comparator로 정렬하기, 2. Runnable로 코드 블록 실행하기, 3. GUI 이벤트 처리하기 등 세가지 예제를 소개한다.

### 2.4.1 Comparator로 정렬하기

```java
//java.util.Comparator
public interface Comparator<T> {
	public int compare(T o1, T o2);
}

inventory.sort(new Comparator<Apple>() {
	public int compare(Apple a1, Apple a2) {
		return a1.getWeight().compareTo(a2.getWeight());
	}
});

//람다 표현식
inventory.sort((Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight()));
```

### 2.4.2 Runnable로 코드 블록 실행하기

자신만의 코드 블록을 수행한다는 점에서 스레드 동작은 경량 프로세스(lightweight process)와 비슷하다. 각각의 스레드는 각기 다른 코드를 실행할 수 있다. 나중에 실행될 코드 조각을 어떻게 표현할 수 있는가가 문제다. 자바에서는 Runnable 인터페이스를 이용해서 실행할 코드 블록을 지정할 수 있다.

```java
//java.lang.Runnable
public interface Runnable {
	public void run();
}

Thread t = new Thread(new Runnable() {
	public void run() {
		System.out.println("Hello world");
	}
});

//람다 표현식
Thread t = new Thread(() -> System.out.println("Hello Word"));
```

### 2.4.3 GUI 이벤트 처리하기

일반적으로 GUI 프로그래밍은 마우스 클릭이나 문자열 위로 이동함 등의 이벤트에 대응하는 동작을 수행하는 식으로 동작한다.

자바FX에서는 setOnAction 메서드에 EventHandler를 전달함으로써 이벤트에 어떻게 반응할지 설정할 수 있다.

```java
Button button = new Button("Send");
button.setOnAction(new EventHandler<ActionEvent>() {
	public void handle(ActionEvent event) {
		label.setText("Sent!!");
	}
});

//람다 표현식
button.setOnAction((ActionEvent event) -> label.setText("Sent!!"));
```

## 2.5 요약

### 핵심

- 동작 파라미터화에서는 메서드 내부적으로 다양한 동작을 수행할 수 있도록 코드를 메서드 인수로 전달한다.
- 동작 파라미터화를 이용하면 변화하는 요구사항에 더 잘 대응할 수 있는 코드를 구현할 수 있으며 나중에 엔지니어링 비용을 줄일 수 있다.
- 동작 파라미터화를 이용하면 변화하는 요구사항에 더 잘 대응할 수 있는 코드를 구현할 수 있으며 나중에 엔지니어링 비용을 줄일 수 있다.
- 코드 전달 기법을 이용하면 동작을 메서드의 인수로 전달할 수 있다. 하지만 자바 8 이전에는 코드를 지저분하게 구현해야 했다. 익명 클래스로도 어느 정도 코드를 깔끔하게 만들 수 있지만 자바 8에서는 인터페이스를 상속받아 여러 클래스를 구현해야 하는 수고를 없앨 수 있는 방법을 제공한다.
- 자바 API의 많은 메서드는 정렬, 스레드, GUI 처리 등을 포함한 다양한 동작으로 파라미터화할 수 있다.