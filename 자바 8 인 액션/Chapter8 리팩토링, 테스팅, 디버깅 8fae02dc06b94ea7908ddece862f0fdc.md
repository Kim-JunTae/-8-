# Chapter8 리팩토링, 테스팅, 디버깅

이 장의 내용
- 람다 표현식으로 코드 리팩토링하기
- 람다 표현식이 객체지향 설계 패턴에 미치는 영향
- 람다 표현식 테스팅
- 람다 표현식과 스트림 API 사용코드 디버깅

# 8.1 가독성과 유연성을 개선하는 리팩토링

간결, 유연성. 람다, 메서드 레퍼런스, 스트림 등의 기능을 이용해서 리팩토링

## 8.1.1 코드 가독성 개선

3가지 리팩토링 예제

- 익명 클래스를 람다 표현식으로 리팩토링하기(8.1.2)

    ```java
    //익명 클래스
    Runnable r1 = new Runnable() {
    	public void run() {
    		System.out.println("Hello");
    	}
    };
    //람다 표현식
    Runnable r2 = () -> System.out.println("Hello");

    //모든 익명클래스를 람다표현식으로 변환 X
    //1. 익명 클래스에서 사용한 this와 super는 람다표현식에서 다른 의미를 갖는다.
    //  익명클래스 this : 익명클래스 자신, 람다표현식 this : 람다를 감싸는 클래스
    //2. 익명 클래스는 감싸고 있는 클래스의 변수를 가릴 수 있다. 람다는 가릴 수 없다.
    // 컴파일 에러!(변수를 가릴 수 없다)
    int a = 10;
    Runnable r1 = () -> {
    	int a = 2;
    	System.out.println(a);
    };
    //작동
    Runnable r2 = new Runnable() {
    	public void run() {
    		int a = 2;
    		System.out.println(a);
    	}
    };

    //익명 클래스를 람다 표현식으로 바꾸면 콘텍스트 오버로딩에 따른 모호함이 초래될 수 있다.
    //예
    interface Task {
    	public void execute();
    }
    public static void doSomething(Runnable r) { r.run(); }
    public static void doSomething(Task a) { r.execute(); }

    //익명클래스
    doSomething(new Task() {
    	public void execute() {
    		System.out.println("Danger danger!!");
    	}
    });

    //람다표현식
    doSomething(() -> System.out.println("Danger danger!!")); //Runnable? Task?

    //명시적 형변환을 통해 모호함 제거
    doSomething((Task)() -> System.out.println("Danger danger!!")); //Task

    //IDE통합개발환경에서 제공하는 리팩토링 기능을 이용하자.
    ```

- 람다 표현식을 메서드 레퍼런스로 리팩토링하기(8.1.3)
- 명령형 데이터 처리를 스트림으로 리팩토링하기(8.1.4)

## 8.1.2 익명 클래스를 람다 표현식으로 리팩토링하기

## 8.1.3 람다 표현식을 메서드 레퍼런스로 리팩토링하기

## 8.1.4 명령형 데이터 처리를 스트림으로 리팩토링하기

## 8.1.5 코드 유연성 개선

# 8.2 람다로 객체지향 디자인 패턴 리팩토링하기

## 8.2.1 전략

## 8.2.2 템플릿 메서드

## 8.2.3 옵저버

## 8.2.4 의무 체인

## 8.2.5 팩토리

# 8.3 람다 테스팅

## 8.3.1 보이는 람다 표현식의 동작 테스팅

## 8.3.2 람다를 사용하는 메서드의 동작에 집중하라

## 8.3.3 복잡한 람다를 개별 메서드로 분할하기

## 8.3.4 고차원 함수 테스팅

# 8.4 디버깅

## 8.4.1 스택 트레이스 확인

## 8.4.2 정보 로깅

# 8.5 요약