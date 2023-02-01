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