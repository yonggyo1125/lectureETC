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

```