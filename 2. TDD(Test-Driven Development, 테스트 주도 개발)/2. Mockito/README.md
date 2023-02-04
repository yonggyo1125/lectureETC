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
	- anyInt(), anyShort(), anyLong(), anyByte(), anyChar(), anyDouble(), anyFloat(), anyBoolean() :  기본 데이터 타입에 대한 임의 값 일치 
	- anyString() : 문자열에 대한 임의 값 일치
	- any() : 임의 타입에 대한 일치
	- anyList(), anySet(), anyMap(), anyCollection() : 임의 콜렉션에 대한 일치
	- matches(String), matches(Pattern): 정규표현식을 이용한 String 값 일치 여부
	- eq(값) : 특정 값과 일치 여부
	
- 스텁을 설정할 메서드 인자가 두 개 이상인 경우 주의할 점이 있다. 

```java 
List<String> mockList = mock(List.class);
given(mockList.set(anyInt(), "123")).willReturn("456");
String old = mockList.set(5, "123");
```

- mockList.set() 메서드의 스텁을 설정할 때 첫 번째 인자는 anyInt() 메서드를 이용해서 임의의 int 값에 일치하도록 했고 두 번째 인자는 "123" 값을 사용해서 정확한 값에 일치하도록 했다. 그리고 mockList.set(4, "123") 코드를 실행하고 있다. 이 코드는 문제 없이 동작할 것 같지만 실제로 mockList.set(5, "123") 코드를 실행하면 익셉션이 발생한다. 
- ArgumnentMatchers의 anyInt()나 any() 등의 메서드는 내부적으로 인자의 일치 여부를 판단하기 위해 ArgumentMatcher를 등록한다. Mockito는 한 인자라도 ArgumentMatcher를 사용해서 설정한 경우 모든 인자를 ArgumentMatcher를 이용해서 설정하도록 하고 있다.
- 임의의 값과 일치하는 인자와 정확하게 일치하는 인자를 함께 사용하고 싶다면 다음과 같이 ArgumentMatchers.eq() 메서드를 사용해야 한다.

```java
@Test
void mixAnyAndEq() {
	List<String> mockList = mock(List.class);

	given(mockList.set(anyInt(), eq("123"))).willReturn("456");

    String old = mockList.set(5, "123");
   assertEquals("456", old);
}
```

## 행위 검증

- 모의 객체의 역할 중 하나는 실제로 모의 객체가 불렸는지 검증하는 것이다. 모의 객체의 특정 메서드가 불렸는지 확인하는 코드는 다음과 같다.

```java
import org.junit.jupiter.api.Test;

import static org.mockito.BDDMockito.then;
import static org.mockito.Mockito.mock;

public class GameTest {
    @Test
    void init() {
        GameNumGen genMock = mock(GameNumGen.class);
        Game game = new Game(genMock);
        game.init(GameLevel.EASY);

        then(genMock).should().generate(GameLevel.EASY);
    }
}
```
- BDDMockito.then()은 메서드 호출 여부를 검증할 모의 객체를 전달받는다. should() 메서드는 모의 객체의 메서드가 불려야 한다는 것을 설정하고 should() 메서드 다음에 실제로 불려야 할 메서드를 지정한다. 이 코드에서 genMock 모의 객체의 generate() 메서드가 GameLevel.EASY 인자를 사용해서 호출되었는지 검증한다. 

- 정확한 값이 아니라 메서드가 불렸는지 여부가 중요하다면 any(), anyInt() 등을 사용해서 인자를 지정하면 된다.

```java
then(genMock).should().generate(any());
```

- 정확하게 한 번만 호출된 것을 검증하고 싶다면 should() 메서드에 Mockito.only()를 인자로 전달한다.

```java
then(genMock).should(only()).generate(any());
```

- 메서드 호출 횟수를 검증하기 위해 Mockito 클래스가 제공하는 메서드는 다음과 같다.
	- only() : 한 번만 호출
	- times(int) : 지정한 횟수만큼 호출
	- never() : 호출하지 않음
	- atLeast(int) : 적어도 지정한 횟수만큼 호출
	- atLeastOnce() : atLeast(1)과 동일
	- atMost(int) : 최대 지정한 횟수만큼 호출 
	
	
## 인자 캡쳐
- 단위 테스트를 실행하다보면 모의 객체를 호출할 때 사용한 인자를 검증해야 할 때가 있다. String이나 int와 같은 타입은 쉽게 검증할 수 있지만 많은 속성을 가진 객체는 쉽게 검증하기 어렵다. 이럴 때 사용할 수 있는 것이 인자 캡처이다.
- Mockito의 ArgumentCaptor를 사용하면 메서드 호출 여부를 검증하는 과정에서 실제 호출할 때 전달한 인자를 보관할 수 있다. 

```java
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.mockito.ArgumentCaptor;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.BDDMockito.then;
import static org.mockito.Mockito.mock;

public class UserRegisterMockTest {
    private UserRegister userRegister;
    private EmailNotifier mockEmailNotifier = mock(EmailNotifier.class);

	... 생략 
	
    @DisplayName("가입하면 메일을 전송함")
    @Test
    void whenRegisterThenSendMail() {
        userRegister.register("id", "pw", "email@Email");
        ArgumentCaptor<String> captor = ArgumentCaptor.forClass(String.class);
        then(mockEmailNotifier).should().sendRegisterEmail(captor.capture());

        String realEmail = captor.getValue();
        assertEquals("email@email.com", realEmail);
    }
}
```

- String 타입의 인자를 보관할 수 있는 ArgumentCaptor를 생성한다. 이렇게 생성한 ArgumentCaptor를 다음과 같이 모의 객체 호출 여부를 검증하는 코드에서 인자로 전달한다. 인자로 전달할 때는 ArgumentCaptor#captor() 메서드를 전달한다. 

```java
ArgumentCaptor<String> captor = ArgumentCaptor.forClass(String.class);
then(mockEmailNotifier).should().sendRegisterEmail(captor.capture());
```

- 모의 객체를 실행할 때 사용한 인자는 ArgumentCaptor#getValue() 메서드로 구할 수 있다. 이렇게 구한 값을 검증에 사용하면 된다.

## JUnit 5 확장 설정
- Mockito의 JUnit5의 확장 기능을 사용하면 애노테이션을 이용해서 모의 객체를 생성할 수 있다. 확장 기능을 사용하면 mockito-junit-jupiter 의존을 추가해야 한다.

- 메이븐 설정

```xml
<dependency>
	<groupId>org.mockito</groupId>
	<artifactId>mockito-junit-jupiter</artifactId>
	<version>2.26.0</version>
	<scope>test</scope>
</dependency>
```

- 그래이들 설정

```
testImplementation 'org.mockito:mockito-junit-jupiter:4.5.1'
```

- 의존을 추가했다면 MockitoExtension 확장을 사용할 수 있다. 이 확장 기능을 사용하면 @Mock 애노테이션을 붙인 필드에 대해 자동으로 모의 객체를 생성해 준다. 다음 코드는 genMock 필드에 GameNumGen 타입에 대한 모의 객체를 할당한다.

```java
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

@ExtendWith(MockitoExtension.class)
public class JUnit5ExtensionTest {
    @Mock
    private GameNumGen genMock;
}
```

