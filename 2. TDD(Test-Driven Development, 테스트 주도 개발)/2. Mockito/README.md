# Mockito 기초 사용법
- Mockito는 모의 객체 생성, 검증, 스텀을 지원하는 프레임워크이다. 

## 의존 설정
- 메이븐 설정
```xml
<dependencies>
	<dependency>
		<groupId>org.junit.jupiter</groupId>
		<artifactId>junit-jupiter</artifactId>
		<version>5.4.0</version>
		<scope>test</scope>
	</dependency>
	<dependency>
		<groupId>org.mockito</groupId>
		<artifactId>mockito-core</artifactId>
		<version>4.5.1</version>
		<scope>test</scope>
	</dependency>
	<dependency>
		<groupId>org.mockito</groupId>
		<artifactId>mockito-junit-jupiter</artifactId>
		<version>4.5.1</version>
		<scope>test</scope>
	</dependency>
</dependencies>
```

- 그래이들 설정
```
dependencies {
	testImplementation('org.junit.jupiter:junit-jupiter:5.4.0')
	testImplementation 'org.mockito:mockito-core:4.5.1'
    testImplementation 'org.mockito:mockito-junit-jupiter:4.5.1'
}
```

## 모의 객체 생성
- Mockito.mock() 메서드를 이용하면 특정 타입의 모의 객체를 생성할 수 있다.

```java
import static org.mockito.Mockito.mock;

public class GameGenMockTest {
    void mockTest() {
        GameNumGen genMock = mock(GameNumGen.class);
    }
}
```

- Mockito.mock() 메서드는 클래스, 인터페이스, 추상 클래스에 대해 모의 객체를 생성할 수 있다. 이 예제에서는 GameNumGen은 다음과 같은 인터페이스라고 가정한다.

```java
public interface GameNumGen {
	String generate(GameLevel level);
}
```

## 스텁 설정
- 모의 객체를 생성한 뒤에는 BDDMockito 클래스를 이용해서 모의 객체에 스텀을 구성할 수 있다. 예를 들어 다음과 같이 BDDMockito.given() 메서드를 이용하면 모의 객체의 메서드가 특정 값을 리턴하도록 설정할 수 있다.

```java
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.BDDMockito.given;
import static org.mockito.Mockito.mock;

public class GameGenMockTest {
	@Test
	void mockTest() {
		GameNumGen genMock = mock(GameNumGen.class);
		given(genMock.generate(GameLevel.EASY)).willReturn("123");
		
		String num = genMock.generate(GameLevel.EASY);
		assertEquals("123", num);
	}
}
```

- 이 코드에서 BDDMockito.given() 메서드는 스텁을 정의할 모의 객체의 메서드 호출을 전달한다. given() 메서드에 이어 willReturn() 메서드는 스텁을 정의한 메서드가 리턴할 값을 지정한다. 
- genMock.generate(GameLevel.EASY) 메서드가 불리면 "123"을 리턴하려고 설정한다.

```java
String num = genMock.generate(GameLevel.EASY);
```
- 모의 객체의 generate() 메서드를 실행한다. 이때 인자로 GameLevel.EASY를 전달하고 있는데, 이는 given()에서 지정한 메서드 인자와 일치하므로 genMock.generate(GameLevel.EASY) 코드는 "123"을 리턴한다.

```java 
(1) 모의 객체 생성
GameNumGen genMock = mock(GameNumGen.class);

(2) 스텁 설정
given(genMock.generate(GameLevel.EASY)).willReturn("123");

(3) 스텁 설정에 매칭되는 메서드 실행
String num = genMock.generate(GameLevel.EASY);
```

- 지정한 값을 리턴하는 대신에 익셉션을 발생하게 설정할 수 도 있다. 이때는 willReturn() 대신 willThrow() 메서드를 사용하면 된다.
```java 
@Test
void mockThrowTest() {
	GameNumGen genMock = mock(GameNumGen.class);
	given(genMock.generate(null)).willThrow(IllegalArgumentException.class);
	
	assertThrows(
		IllegalArgumentException.class,
		() -> genMock.generate(null));
}
```

- 타입 대신에 익셉션 객체를 인자로 받는 willThrow() 메서드를 사용해도 된다.

```java
given(genMock.generate(null)).willThrow(new IllegalArgumentException());
```

- 리턴 타입이 void인 메서드에 대해 익셉션을 발생하려면 BDDMockito.willThrow() 메서드로 시작한다.

```java
package tdd01;

import org.junit.jupiter.api.Test;

import java.util.List;

import static org.junit.jupiter.api.Assertions.assertThrows;
import static org.mockito.BDDMockito.willThrow;
import static org.mockito.Mockito.mock;

public class VoidMethodStubTest {
    @Test
    void voidMethodWillThrowTesst() {
        List<String> mockList = mock(List.class);
        willThrow(UnsupportedOperationException.class)
                .given(mockList)
                .clear();

        assertThrows(
                UnsupportedOperationException.class,
                () -> mockList.clear()
        );
    }
}
```
- BDDMockito.willThrow() 메서드는 발생할 익셉션 타입이나 익셉션 객체를 인자로 받는다. 이어서 given() 메서드는 모의 객체를 전달받는다. 모의 객체의 메서드 실행이 아닌 모의 객체임에 유의한다. 이때 given() 메서드는 인자로 전달받은 모의 객체 자신을 리턴하는데 이때 익셉션을 발생할 메서드를 호출한다. 

```java
willThrow(UnsupportedOperationException.class)
                .given(mockList)
                .clear();
```
- 실제로 모의 객체의 메서드를 호출하지 않으며 단지 익셉션을 발생할 모의 객체를 설정하는 것뿐이다.

## 인자 매칭 처리

```java
given(genMock.generate(GameLevel.EASY)).willReturn("123");

String num = genMock.generate(GameLevel.NORMAL);
```

- 이 코드는 스텁을 설정할 때 generate() 메서드의 인자로 GameLevel.EASY를 전달하고 있는데, 실제로 generate() 메서드를 호출할 때는 GameLevel.NORMAL을 인자로 전달했다. 이 경우 genMock.generate(GameLevel.NORMAL) 코드는 스텁을 설정할 때 사용한 인자와 일치하지 않으므로 "123"이 아닌 null을 리턴한다.

|Mockito는 일치하는 스텁 설정이 없을 경우 리턴 타입의 기본 값을 리턴한다. 예를 들어 리턴 타입이 int면 0을 리턴하고 boolean이면 false를 리턴한다. 기본 데이터 타입이 아닌 String이나 List와 같은 참조 타입이면 null을 리턴한다.

- org.mockito.ArgumentMatchers 클래스를 사용하면 정확하게 일치하는 값 대신 임의의 값에 일치하도록 설정할 수 있다. 

```java
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.BDDMockito.given;
import static org.mockito.Mockito.mock;

public class AnyMatcherTest {
    @Test
    void anyMatchTest() {
        GameNumGen genMock = mock(GameNumGen.class);
        given(genMock.generate(any())).willReturn("456");

        String num = genMock.generate(GameLevel.EASY);
        assertEquals("456", num);

        String num2 = genMock.generate(GameLevel.NORMAL);
        assertEquals("456", num2);
    }
}
```
- 스텁을 설정할 때 ArgumentMatchers.any() 메서드를 인자 위치에 전달했다. 이 메서드를 사용하면 모든 값에 일치하도록 스텁을 설정한다. 따라서 generate() 메서드는 모두 "456"을 리턴한다.

- Mockito 클래스와 BDDMockito 클래스는 ArgumentMatchers 클래스를 상속하고 있으므로 ArgumentMatchers.any() 대신에 Mockito.any()나 BDDMockito.any()를 사용해도 된다.

- ArgumentMatchers 클래스는 any() 외에도 다음의 메서드를 제공한다.
