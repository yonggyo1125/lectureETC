# JUnit 5의 기초

## JUnit 5 모듈 구성
- JUnit 5는 크게 세 개의 요소로 구성되어 있다.
	- JUnit  플랫폼 테스팅 프레임워크를 구동하기 위한 런처와 테스트 엔진을 위한 API를 제공한다.
	- JUnit 주피터(Jupiter) : JUnit 5를 위한 테스트 API와 실행 엔진을 제공한다.
	- JUnit 빈티지(Vintage) : JUnit 3과 4로 작성된 테스트를 JUnit 5 플랫폼에서 실행하기 위한 모듈을 제공한다.
	
- JUnit 5의 모듈 구성

![JUnit5의 모듈 구성](https://raw.githubusercontent.com/yonggyo1125/lectureETC/master/2.%20TDD(Test-Driven%20Development%2C%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%A3%BC%EB%8F%84%20%EA%B0%9C%EB%B0%9C)/1.%20JUnit5/1.%20JUnit5%20%EA%B8%B0%EC%B4%88/images/image1.png)


- JUnit5는 테스트를 위한 API로 주피터 API를 제공한다. 
- 주피터 API를 사용해서 테스트를 작성하고 실행하려면 상기 모듈 구성 이미지에서 보이는 주피터 관련 모듈을 의존에 추가하면 된다. 
- 메이븐 의존은 다음과 같이 설정한다. 
- 여기서 junit-jupiter 모듈은 junit-jupiter-api 모듈, junit-jupiter-params 모듈, junit-jupiter-engine 모듈을 포함한다. 
- 이 세 모듈은 JUnit으로 테스트 코드를 작성하고 실행하기 위한 모듈이다.

#### pom.xml

```xml
<dependencies>
	<dependency>
		<groupId>org.junit.jupiter</groupId>
		<artifactId>junit-jupiter</artifactId>
		<version>5.5.0</version>
		<scope>test</scope>
	</dependency>
</dependencies>

<build>
	<plugins>
		<plugin>
			<artifactId>maven-surefire-plugin</artifactId>
			<version>2.22.1</version>
		</plugin>
		.. 다른 설정 생략 
	</plugins>
</build>
```

- JUnit 5를 이용해서 테스트를 실행하려면 JUnit 5 플랫폼이 제공하는 플랫폼 런처를 사용해야 한다. 
- 메이븐은 maven-surefire-plugin 2.22.0 버전부터 JUnit 5 플랫폼을 지원하므로 따로 플랫폼을 설정하지 않아도 된다.

- 그래이들은 다음과 같이 설정한다.
- JUnit 주피터 API를 테스트 구현으로 사용하고 JUnit 플랫폼을 이용해서 테스트를 실행하도록 설정하면 된다. 

#### build.gradle

```
plugins {
    id 'java'
}

group 'org.example'
version '1.0-SNAPSHOT'

repositories {
    mavenCentral()
}

dependencies {
    testImplementation 'org.junit.jupiter:junit-jupiter-api:5.8.1'
}

test {
    useJUnitPlatform()
}
```

- testImplementation을 사용하여 junit-jupiter 의존을 추가한다. test 태스크는 JUnit 5 플랫폼을 사용하도록 설정한다.

| 그래이들 4.5 버전부터 JUnit5 플랫폼을 지원한다. 그 이전 버전을 사용하고 있다면 JUnit 4 버전을 사용하거나 그레이들 버전을 올릴 것을 권장합니다.


## @Test 애노테이션과 테스트 메서드
- JUnit 모듈을 설정했다면 JUnit을 이용해서 테스트 코드를 작성하고 실행할 수 있다. 
- 테스트로 사용할 클래스를 만들고 @Test 애노테이션을 메서드에 붙이면 된다.

```java
package tdd01;

import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.assertEquals;

public class SumTest {
    @Test
    void sum() {
        int result = 2 + 3;
        assertEquals(5, result);
    }
}
```

- 테스트 클래스의 이름을 특별하는 규칙은 없지만 보통 다른 클래스와 구분을 쉽게 하기 위해 'Test'를 접미사로 붙인다. 
- 테스트를 실행할 메서드에는 @Test 애노테이션을 붙인다.
- 이때 @Test 애노테이션을 붙인 메서드는 private이면 안 된다.
- JUnit의 Assertions 클래스는 assertEquals() 메서드와 같이 값을 검증하기 위한 목적의 다양한 메서드를 제공한다.


## 주요 단언 메서드

- Assertions 클래스는 assertEquals()를 포함해 다음과 같은 단언 메서드를 제공한다.

|메서드|설명|
|----|----|
|assertEquals(expected, actual)|실제 값(actual)이 기대하는 값(expected)과 같은지 검사한다.|
|assertNotEquals(unexpected, actual)|실제 값(actual)이 특정 값(unexpected)과 같지 않은지 검사한다.|
|assertSame (Object expected, Object actual)|두 객체가 동일한 객체인지 검사한다.|
|assertNotSame(Object unexpected, Object actual)|두 객체가 동일하지 않은 객체인지 검사한다.|
|assertTrue(boolean condition)|값이 true인지 검사한다.|
|assertFalse(boolean condition)|값이 false인지 검사한다.|
|assertNull(Object actual)|값이 null인지 검사한다.|
|assertNotNull(Object actual)|값이 null이 아닌지 검사한다.|
|fail()|테스트를 실패 처리한다.|

- 주요 타입별로 assertEquals() 메서드가 존재한다. 예를 들어 int 타입을 위한 assertEquals() 메서드, Long 타입을 위한 assertEquals() 메서드, Object를 위한 assertEquals() 메서드 등이 존재한다. assertEquals() 메서드를 사용할 때 주의할 점은 첫 번째 인자가 기대하는 값이고 두 번째 인자가 검사하려는 값이라는 점이다.

- assertEquals(Object expected, Object actual) 메서드는 equals() 메서드를 이용해서 두 객체가 같은지 비교한다. 
- LocalDate 클래스는 알맞게 equals() 메서드를 구현했으므로 다음과 같이 assertEquals() 메서드를 이용해서 두 객체가 같은 값을 갖는지 비교할 수 있다.

```java
LocalDate dateTime1 = LocalDate.now();
LocalDate dateTime2 = LocalDate.now();
assertEquals(dateTime1, dateTime2);
```

- fail() 메서드는 테스트에 실패했음을 알리고 싶을 때 사용한다. 예를 들어 ID와 암호로 전달 받은 파라미터 값이 null이면 IllegalArgumentException이 발생하도록 인증 기능을 구현했다고 가정하자. 테스트 코드는 ID와 암호로 null을 전달했는데 익셉션이 발생하지 않으면 테스트에 실패했다고 볼 수 있다. 이럴 때 fail()을 사용할 수 있다. 

```java
try {
	AuthService authService = new AuthService();
	authService.authenticate(null, null);
	fail(); // 이 지점에 다다르면 fail() 메서드는 테스트 실패 에러를 발생
} catch (IllegalArgumentException e) {

}
``` 

- 익셉션 발생 유무가 검증 대상이라면 fail() 메서드를 사용하는 것보다 다음 두 메서드를 사용하는 것이 더욱 명시적이다.

|메서드|설명|
|----|----|
|assertThrows(Class\<T\> expectedType, Executable executable)|executable을 실행한 결과로 지정한 타입의 익셉션이 발생하는지 검사한다.|
|assertDoesNotThrow(Executable executable)|executable을 실행한 결과로 익셉션이 발생하지 않는지 검사한다.|

- assertThrows()를 이용해서 지정한 익셉션이 발생하는지 검사하는 코드 예

```java
assertThrows(IllegalArgumentException.class, () -> {
	AuthService authService = new AuthService();
	authService.authenticate(null, null);
});
```

- assertThrows() 메서드는 발생한 익셉션 객체를 리턴한다. 발생한 익셉션을 이용해서 추가로 검증이 필요하면 assertThrows() 메서드가 리턴한 익셉션 객체를 사용하면 된다.

```java
IllegalArgumentException throws = assertThrows(IllegalArgumentException.class, () -> {
	AuthService authService = new AuthService();
	authService.authenticate(null, null);
});

assertTrue(thrown.getMessage().contains("id"));
```

|assertThrows()와 assertDoesNotThrow() 메서드에서 사용하는 Executable 인터페이스는 다음과 같이 execute() 메서드를 가진 함수형 인터페이스이다.

```java
package org.junit.jupiter.api.function;

public interface Executable {
	void execute() throws Throwable;
}
```

- assert 메서드는 실패하면 다음 코드를 실행하지 않고 바로 익셉션을 발생한다. 예를 들면 다음 코드는 첫 번째 assertEquals() 메서드에서 검증에 실패하기 때문에 그 시점에 AssertionFailError를 발생한다. 따라서 두 번째 assertEquals() 메서드는 실행되지 않는다.

```java
assertEquals(3, 5 / 2); // 검증 실패로 에러 발생
assertEquals(4, 2 * 2); // 이 코드는 실행되지 않음
```

- 경우에 따라 일단 모든 검증을 실행하고 그 중에 실패한 것이 있는지 확인하고 싶을 때가 있는데, 이 때 사용할 수 있는 것이 assertAll() 메서드이다. 다음은 assertAll() 메서드의 사용 예이다.

```java
assertAll(
	() -> assertEquals(2, 5 / 2),
	() -> assertEquals(4, 2 * 2),
	() -> assertEquals(6, 11 / 2)
);
```

- assertAll() 메서드는 Executable 목록을 가변 인자로 전달받아 각 Executable을 실행한다. 실행 결과로 검증에 실패한 코드가 있다면 그 목록을 모아서 에러 메시지로 보여준다. 

```
Multiple Failures (2 failures)
	org.opentest4j.AssertionFailedError: expected: <3> but was: <2>
	org.opentest4j.AssertionFailedError: expected: <6> but was: <5>
Expected :6
Actual   :5
<Click to see difference>
... 생략 ...
```


## 테스트 라이프사이클

### @BeforeEach 애노테이션과 @AfterEach 애노테이션 
- JUnit은 각 테스트 메서드마다 다음 순서대로 코드를 실행한다.<br>
	1\. 테스트 메서드를 포함한 객체 생성 
	2\. (존재하면) @BeforeEach 애노테이션이 붙은 메서드 실행
	3\. @Test 애노테이션이 붙은 메서드 실행
	4\. (존재하면) @AfterEach 애노테이션이 붙은 메서드 실행
	
```java

```