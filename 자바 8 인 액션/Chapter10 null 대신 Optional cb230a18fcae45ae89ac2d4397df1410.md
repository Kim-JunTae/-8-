# Chapter10 null 대신 Optional

### 이 장의 내용

- null 레퍼런스의 문제점과 null을 멀리해야 하는 이유
- null 대신 Optional : null로 부터 안전한 도메인 모델 재구현하기
- Optional 활용 : null 확인 코드 제거하기
- Optional에 저장된 값을 확인하는 방법
- 값이 없을 수도 있는 상황을 고려하는 프로그래밍

- null 레퍼런스의 문제점과 null을 멀리해야 하는 이유
    - 자동차와 자동차 보험을 가지고 있는 사람 객체

    ```java
    public class Person {
    	private Car car;
    	public Car getCar() { return car; }
    }

    public class Car {
    	private Insurance insurance;
    	public Insurance getInsurance() { return insurance; }
    }

    public class Insurance {
    	private String name;
    	public String getName() { return name; }
    }
    ```

    - 문제 발생! : 차를 소유하지 않은 사람도 많다.

        차 없는 사람이라면 NullPointerException 발생

    ```java
    public String getCarInsuranceName(Person person) {
    	return person.getCar().getInsurance().getName();
    }
    ```

    - 예기치 않은 NullPointerException을 피하려면?
        - 보수적인 자세

        ```java
        //예제 10-2
        //null 안전 시도 1 : 깊은의심
        //필요한 곳에 다양한 null 확인 코드 추가, 반드시 필요하지 않은 코드까지도 null 확인 코드를 추가
        public String getCarInsuranceName(Person person) {
        	if(person != null) {
        		Car car = person.getCar();
        		if(car != null) {
        			Insurance insurance = car.getInsurance();
        			if(insurance != null) {
        				return insurance.getName();
        			}
        		}
        	}
        	return "Unknown";
        }

        //예제 10-3
        //null 안전 시도 2 : 너무 많은 출구
        public String getCarInsuranceName(Person person) {
        	if(person == null) {
        		return "Unknown";
        	}
        	Car car = person.getCar();
        	if(car == null) {
        		return "Unknown";
        	}
        	Insurance insurance = car.getInsurance();
        	if(insurance == null) {
        		return "Unknown";
        	}
        	return insurance.getName();
        }
        ```

    ### null 때문에 발생하는 문제

    - 에러의 근원이다.
    - 코드를 어지럽힌다.
    - 아무 의미가 없다
    - 자바 철학에 위배된다.(자바는 개발자로부터 모든 포인터를 숨겼다. 하지만 예외로서 null 포인터가 있다.)
    - 형식 시스템에 구멍을 만든다.
    null은 무형식이며, 정보를 포함하고 있지 않으므로 모든 레퍼런스 형식에 null을 할당할 수 있다. 이런식으로 null이 할당되기 시작하면서 시스템의 다른 부분으로 null이 퍼졌을 때 애초에 null이 어떤 의미로 사용되어는지 알 수 없다.

    ### 다른 언어에서는 null 대신 무엇을 사용하나?

    - 그루비 : 안전 내비게이션 연산자

        def carInsuranceName = person?.car?.insurance?.name

    - 하스켈, 스칼라 등의 함수형 언어

        하스켈 : 선택형 값을 저장할 수 있는 Maybe라는 형식 제공

        스칼라 : T형식의 값을 갖거나 아무 값도 갖지 않을 수 있는 Option[T]라는 구조 제공

    - 자바 8에서는 선택형 값 개념의 영향을 받아 Optional<T>라는 새로운 클래스 사용

- Optional 클래스란?

    Optional은 선택형값을 캡슐화하는 클래스이다.

    ![Chapter10%20null%20%E1%84%83%E1%85%A2%E1%84%89%E1%85%B5%E1%86%AB%20Optional%20cb230a18fcae45ae89ac2d4397df1410/Untitled.png](Chapter10%20null%20%E1%84%83%E1%85%A2%E1%84%89%E1%85%B5%E1%86%AB%20Optional%20cb230a18fcae45ae89ac2d4397df1410/Untitled.png)

    값이 있으면 Optional 클래스는 값을 감싼다. 값이 없으면 Optional.empty 메서드로 Optional을 반환한다.

    - null 레퍼런스와 Optional.empty()의 차이점
        - null은 참조하려고 하면 NullPointerException이 발생
        - Optional.empty()는 객체이므로 다양하게 사용 가능
    - 수정된 코드

        Car, Insurance는 O, X의 선택, 보험회사의 이름은 반드시 이름을 가져야 함

    ```java
    //예제 10-4
    public class Person {
    	private Optional<Car> car;
    	public Optional<Car> getCar() { return car; }
    }

    public class Car {
    	private Optional<Insurance> insurance;
    	public Optional<Insurance> getInsurance() { return insurance; }
    }

    public class Insurance {
    	private String name;
    	public String getName() { return name; }
    }
    ```

- Optional 적용 패턴, 활용

    ### Optional 객체 만들기

    - 빈 Optional

        Optional<Car> optCar = Optional.empty();

    - null이 아닌 값으로 Optional 만들기

        Optional<Car> optCar = Optional.of(car);

        car가 null이라면 즉시 NullPointerException이 발생한다.

    - null 값으로 Optional 만들기

        Optional<Car> optCar = Optional.ofNullable(car);

        car가 null이면 빈 Optional 객체가 반환된다.

    ### 맵으로 Optional의 값을 추출하고 변환하기

    ```java
    //예 : 보험회사의 이름 추출
    String name = null;
    if(insurance != null) {
    	name = insurance.getName();
    }

    //Optional의 map 메서드
    Optional<Insurance> optInsurance = Optional.ofNullable(insurance);
    Optional<String> name = optInsurance.map(Insurance::getName);

    public String getCarInsuranceName(Person person) {
    	return person.getCar().getInsurance().getName();
    }
    ```

    ![Chapter10%20null%20%E1%84%83%E1%85%A2%E1%84%89%E1%85%B5%E1%86%AB%20Optional%20cb230a18fcae45ae89ac2d4397df1410/Untitled%201.png](Chapter10%20null%20%E1%84%83%E1%85%A2%E1%84%89%E1%85%B5%E1%86%AB%20Optional%20cb230a18fcae45ae89ac2d4397df1410/Untitled%201.png)

    ```java
    Optional<Person> optPerson = Optional.of(person);
    Optional<String> name = 
    				optPerson.map(Person::getCar)
    								 .map(Car::getInsurance)
    								 .map(Insurance::getName);
    //그러나 위 코드는 컴파일 되지 않는다.
    //getCar는 Optional<Car>형식의 객체를 반환
    ```

    ### flatMap으로 Optional 객체 연결

    ![Chapter10%20null%20%E1%84%83%E1%85%A2%E1%84%89%E1%85%B5%E1%86%AB%20Optional%20cb230a18fcae45ae89ac2d4397df1410/Untitled%202.png](Chapter10%20null%20%E1%84%83%E1%85%A2%E1%84%89%E1%85%B5%E1%86%AB%20Optional%20cb230a18fcae45ae89ac2d4397df1410/Untitled%202.png)

    - Optional로 자동차의 보험회사 이름 찾기

    ```java
    //예제 10-5
    public String getCarInsuranceName(Optional<Person> person) {
    	return person.flatMap(Person::getCar)
    							 .flatMap(Car::getInsurance)
    							 .map(Insurance::getName)
    							 .orElse("Unknown"); // 결과 Optional이 비어있으면 기본값 사용
    }
    ```

    - Optional을 이용한 Person/Car/Insurance 참조 체인

    ![Chapter10%20null%20%E1%84%83%E1%85%A2%E1%84%89%E1%85%B5%E1%86%AB%20Optional%20cb230a18fcae45ae89ac2d4397df1410/Untitled%203.png](Chapter10%20null%20%E1%84%83%E1%85%A2%E1%84%89%E1%85%B5%E1%86%AB%20Optional%20cb230a18fcae45ae89ac2d4397df1410/Untitled%203.png)

    도메인 모델에 Optional을 사용했을 때 데이터를 직렬화할 수 없는 이유
    Optional 클래스 설계자는 Optional의 용도가 선택형 반환값을 지원하는 것이라고 명확하게 정의했다.
    → 예제 10-4에서 했던 값의 여부를 구체적으로 표현한 것은 가정하지 않았다.(필드 형식으로 사용할 것을 가정하지 않았다)
    → Serializable 인터페이스를 구현하지 않는다.?
    직렬화 모델이 필요하다면 Optional로 값을 반환받을 수 있는 메서드를 추가하는 방식을 권장한다고 한다. 

    ```java
    //예제 10-4
    public class Person {
    	private Optional<Car> car;
    	public Optional<Car> getCar() { return car; }
    }

    public class Car {
    	private Optional<Insurance> insurance;
    	public Optional<Insurance> getInsurance() { return insurance; }
    }

    public class Insurance {
    	private String name;
    	public String getName() { return name; }
    }

    //직렬화 모델이 필요하다면 권장하는 방식
    public class Person {
    	private Car car;
    	public Optional<Car> getCarAsOptional() {
    		return Optional.ofNullable(car);
    	}
    }
    ```

    ### 디폴트 액션과 Optional 언랩

    - get() : 값을 읽는 가장 간단한 메서드이지만 안전하지 않다. 래핑된 값이 없으면 NoSuchElementException을 발생시킨다.
    - orElse() : 값이 없을 때 디폴트값 제공할 수 있다.
    - orElseGet(Supplier<? extends T> other) : 게으른 버전의 메서드, Optional에 값이 없을 때만 Supplier가 실행, 디폴트 메서드를 만드는데 시간이 걸리거나(효율성 때문에) Optional이 비어있을 때만 디폴트 값을 생성하고 싶다면(디폴트값이 반드시 필요한 상황) 사용
    - orElseThrow(Supplier<? extends X> exceptionSupplier) : Optional이 비어있을 때 예외를 발생시킨다는 점에서 get 메서드와 비슷하지만 발생시킬 예외의 종류를 선택할 수 있다.
    - ifPresent(Consumer<? super T> consumer) : 값이 존재할 때 인수로 넘겨준 동작일 실행할 수 있다. 값이 없으면 아무 일도 일어나지 않는다.

    ### 두 Optional 합치기

    - 예시 Person과 Car 정보를 이용해서 가장 저렴한 보험료를 제공하는 보험회사를 찾는 복잡한 비즈니스 로직을 구현한 외부 서비스
    - 두 Optional을 인수로 받아서 Optional<Insurance>를 반환하는 null 안전 버전의 메서드를 구현해야함.
    - 퀴즈 10-1

        ```java
        public Optional<Insurance> nullSafeFindCheapestInsurance(Optional<Person> person, 
                                                                 Optional<Car> car){
        	return person.flatMap(p -> car.map(c -> findCheapestInsurance(p, c)));
        }
        ```

    ### 필터로 특정값 거르기

    - 종종 객체의 메서드를 호출해서 어떤 프로퍼티를 확인해야 할 때가 있단다..?
    - 예시 이름이 "CambridgeInsurance"인지 확인해야 한다고 가정

    ```java
    Insurance insurance = ....;
    if(insurance != null && "CambridgeInsurance".equals(insurancec.getName())) {
    	System.out.println("ok");
    }

    //Optional 객체에 filter 메서드 이용
    Optional<Insurance> optInsurance = ...;
    optInsurance.filter(insurance ->
    													"CambridgeInsurance".equals(insurance.getName()))
    						.ifPresent(x -> System.out.println("ok"));
    ```

    - 퀴즈 10-2

    ![Chapter10%20null%20%E1%84%83%E1%85%A2%E1%84%89%E1%85%B5%E1%86%AB%20Optional%20cb230a18fcae45ae89ac2d4397df1410/Untitled%204.png](Chapter10%20null%20%E1%84%83%E1%85%A2%E1%84%89%E1%85%B5%E1%86%AB%20Optional%20cb230a18fcae45ae89ac2d4397df1410/Untitled%204.png)

- Optional을 사용한 실용 예제
    - 값이 없는 상황을 처리하던 기존의 알고리즘과는 다른 관점에서 접근해야한다.
    → 코드 구현만 바꾸는 것이 아니라 네이티브 자바 API와 상호작용하는 방식도 교체

    ### 잠재적으로 null이 될 수 있는 대상을 Optional로 감싸기

    ```java
    //기존 : null을 반환하면서 요청한 값이 없거나 어떤 문제로 계산에 실패했음을 알린다.
    Object value = map.get("key");

    //방법 1 : 기존의 방식 if-then-else

    //방법 2 : Optional.ofNullable 이용
    Optional<Object> value = Optional.ofNullable(map.get("key"));

    ```

    ### 예외와 Optional

    자바 API는 어떤 이유에서 값을 제공할 수 없을 때 null을 반환하는 대신 예외를 발생시킬 때도 있다. (예 : Interger.parseInt(String) 정적 메서드 → try/catch)

    ```java
    //정수로 변환할 수 없는 문자열 문제를 빈 Optional로 해결할 수 있다.
    //유틸리티 클래스 OptionalUtility를 만들자!
    public static Optional<Integer> stringToInt(String s) {
    	try {
    		return Optional.of(Integer.parseInt(s));
    	} catch(NumberFormatException e) {
    		return Optional.empty();
    	}
    ```

    ### 기본형 Optional과 이를 사용하지 말아야 하는 이유

    음... Optional도 기본형으로 특화된 OptionalInt, OptionalLong, OptionalDouble 등의 클래스를 제공. 그러나 Optional의 최대 요소 수는 한 개이므로 성능 개선의 기대가 없다.

    또한 기본형 특화 Optional은 map, flatMap, filter 등을 지원하지 않으므로 기본형 특화 Optional을 사용할 것을 권장하지 않는다.

    ### 응용

    - 프로그램의 설정 인수로 Properties를 전달한다고 가정

    ```java
    Properties props = new Properties();
    props.setProperty("a", "5");
    props.setProperty("b", "true");
    props.setProperty("c", "-3");

    public readDuration(Properties props, String name)

    assertEquals(5, readDuration(param, "a"));
    assertEquals(0, readDuration(param, "b"));
    assertEquals(0, readDuration(param, "c"));
    assertEquals(0, readDuration(param, "d"));

    //복잡 : 프로퍼티에서 지속시간을 읽는 명령형 코드
    public int readDuration(Properties props, String name) {
    	String value = props.getProperty(name);
    	//요청한 이름에 해당하는 프로퍼티가 존재하는지 확인한다.
    	if(value != null){
    		try {
    			//문자열 프로퍼티를 숫자로 변환하기 위해 시도한다.
    			int i = Integer.parseInt(value);
    			//결과 숫자가 양수인지 확인한다.
    			if(i > 0) {
    				return i;
    			}
    		} catch (NumberFormatException nfe) { }
    	}
    	//하나의 조건이라도 실패하면 0을 반환한다.
    	return 0;
    }

    //개선 : 퀴즈 10-3 Optional로 프로퍼티에서 지속시간 읽기
    public int readDuration(Properties props, String name) {
    	return Optional.ofNullable(props.getProperty(name))
    								 .flatMap(OptionalUtility::stringToInt)
    								 .filter(i -> i > 0)
    								 .orElse(0);
    }
    //Optional과 스트림에서 사용한 방식은 
    //여러 연산이 서로 연결되는 데이터베이스 질의문과 비슷한 형식을 갖는다.
    ```

### 요약

- 역사적으로 프로그래밍 언어에서는 null 레퍼런스로 값이 없는 상황을 표현해왔다.
- 자바 8에서는 값이 있거나 없음을 표현할 수 있는 클래스 java.util.Optional<T>를 제공
- 팩토리 메서드 Optional.empty, Optional.of, Optional.ofNullable 등을 이용해서 Optional 객체를 만들 수 있다.
- Optional 클래스는 스트림과 비슷한 연산을 수행하는 map, flatMap, filter 등의 메서드를 제공한다.
- Optional로 값이 없는 상황을 적절하게 처리하도록 강제할 수 있다. 즉, Optional로 예상치 못한 null 예외를 방지할 수 있다.
- Optional을 활용하면 더 좋은 API를 설계할 수 있다. 즉 사용자는 메서드의 시그니처만 보고도 Optional값이 사용되거나 반환되는지 예측할 수 있다.