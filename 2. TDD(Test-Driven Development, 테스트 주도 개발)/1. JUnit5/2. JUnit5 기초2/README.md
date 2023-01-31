# JUnit 5 기초2

## 조건에 따른 테스트 
- JUnit 5는 조건에 따라 테스트를 실행할지 여부를 결정하는 기능을 제공합니다. 
- 이들 애노테이션은 org.junit.jupiter.api.condition 패키지에 속해 있다.
	- @EnabledOnOs, @DisabledOnOs
	- @EnabledOnJre, @DisabledOnJre
	- @EnabledIfSystemProperty, @DisabledIfSystemProperty
	- @EnabledIfEnvironmentVariable, @DisabledIfEnvironmentVariable
- 테스트 메서드가 특정 운영체제에서만 동작해야 한다면 @EnabledOnOs 애노테이션을 사용한다. 반대의 경우는 @DisabledOnOs 애노테이션을 사용한다. 

```java
public class OsTmpPathTest {
	@Test
	@EnabledOnOs(OS.WINDOWS)
	void windowTmpPath() {
		Path tmpPath = Paths.get("C:\\Temp");
		assertTrue(Files.isDirectory(tmpPath));
	}
	
	@Test
	@EnabledOnOs(OS.LINUX)
	void linuxTmpPath() {
		Path tmpPath = Paths.get("/tmp");
		assertTrue(Files.isDirectory(tmpPath));
	}
}
```

- OS 타입은 열거 타입으로 WINDOWS, MAC, LINUX와 같은 운영체제 이름을 값으로 정의하고 있다.  @EnabledOnOs 애노테이션과 @DisabledOnOs 애노테이션은 OS 열거 타입을 사용해서 테스트를 실행하거나 실행하지 않을 운영체제 조건을 지정한다.
- 자바 버전에 따라 테스트를 실행하고 싶다면 @EnabledOnJre 애노테이션을 사용한다. 반대의 경우는 @DisabledOnJre 애노테이션을 사용한다. 

```java
@Test
@EnabledOnJre({JRE.JAVA_8, JRE.JAVA_9, JRE.JAVA_10, JRE.JAVA_11})
void testOnJre() {
	assertEquals(LocalDate.of(1919,3,1), LocalDate.of(2019, 3, 1).minusYears(100));
}
```

- JRE 열거 타입은 자바 버전을 정의하고 있다. 5.4.0 기준으로 JRE 열거 타입은 JAVA_8 부터 JAVA_13까지 값을 정의하고 있고 추가로 OTHER 값을 정의하고 있다. 자바 7이나 자바 6을 사용할 때 JRE.OTHER를 사용할 수 있지만, 자바 8버전부터 JUnit 5를 사용할 수 있기 때문에 실질적으로 JRE.OTHER를 사용할 일은 없다.

- @EnabledIfSystemProperty 애노테이션과 @DisabledIfSystemProperty 애노테이션은 시스템 프로퍼티 값을 비교하여 테스트 실행 여부를 결정한다. 
- 다음 코드는 java.vm.name 시스템 프로퍼티 값이 OpenJDK를 포함하고 있으면 해당 텍스트를 실행한다.

```java
@Test
@EnabledIfSystemProperty(named = "java.vm.name", matches = ".*OpenJDK.*")
void openJdk() {
	assertEquals(2, 1 + 1);
}
```

- @EnabledIfSystemProperty 애노테이션의 named 속성은 시스템 프로퍼티의 이름을 지정하고 matches 속성에는 값의 일치 여부를 검사할 때 사용할 정규 표현식을 지정한다.

- @EnabledIfEnvironmentVariable 애노테이션도 named 속성과 matches 속성을 사용한다. 차이점은 named 속성에 환경변수 이름을 사용한다는 것이다.

## 태깅과 필터링
- @Tag 애노테이션은 태스크에 태그를 달 때 사용한다. @Tag 애노테이션은 클래스와 테스트 메서드에 적용할 수 있다. 

```java
import org.junit.jupiter.api.Tag;
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.assertEquals;

@Tag("integration")
public class TagTest {
    
    @Tag("very-slow")
    @Test
    void verySlow() {
        int result = someVerySlowOp();
        assertEquals(result, 0);
    }
}
```
- 이 코드는 verySlow() 테스트 메서드에 이름이 "slow"인 태그를 붙였다. 여러 태그를 붙이고 싶다면 각 이름마다 @Tag 애노테이션을 사용하면 된다.
- 태그의 이름은 다음 규칙을 따라야 한다.
	- null이 공백이면 안 된다.
	- 좌우 공백을 제거한 뒤에 공백을 포함하면 안 된다.
	- ISO 제어 문자를 포함하면 안 된다.
	- 다음 글자를 포함하면 안된다. - ()&\|!
	
- @Tag 애노테이션을 이용하면 메이븐이나 그래들에서 실행할 테스트를 선택할 수 있다.
- 메이븐 설정 예
```xml
<plugin>
	<artifactId>maven-surefire-plugin</artifactId>
	<version>2.22.1</version>
	<configuration>
		<groups>integration</groups>
		<excludedGroups>slow | very-slow</excludedGroups>
	</configuration>
</plugin>
```

- \<groups\> 요소는 실행에 포함할 태그를 지정하고   \<excludedGroups\> 요소는 실행에서 제외할 태그를 지정한다. 위 코드는 integration 태그를 대상에 포함하고 slow 태그나 very-slow 태그는 테스트 대상에서 제외한다. 

- 그레이들 설정 예

```
test {
	useJUnitPlatform {
		includeTags 'integration'
		excludeTags 'slow | very-slow'
	}
}
```

- 테스트 포함 대상이나 제외 대상을 지정할 때 태그 식을 사용하는데 태그 식에서는 다음 연산을 이용해서 식을 조합할 수 있다. 
	- ! : NOT 연산, 예) !integration
	- & : AND 연산, 예) slow & mock-server
	- \| : OR 연산, 예) slow | very-slow
	
- 이 연산을 사용하면 다양한 태그 조합을 사용해서 테스트 대상을 구분할 수 있다.

## 중첩 구성
- @Nested 애노테이션을 사용하면 중첩 클래스에 테스트 메서드를 추가할 수 있다. 

```java
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Nested;
import org.junit.jupiter.api.Test;

public class Outer {
    @BeforeEach
    void outerBefore() {}

    @Test
    void outer() {}

    @Nested
    class NestedA {
        @BeforeEach
        void nestedBefore() {}

        @Test
        void nested() {}

        @AfterEach
        void nestedAfter() {}
    }
}
```

- 위 코드를 기준으로 중첩 클래스의 테스트 메서드인 nested1()을 실행하는 순서는 다음과 같다.
	- 1\. Outer 객체 생성 
	- 2\. NestedA 객체 생성
	- 3\. outerBefore() 메서드 실행
	- 4\. nestedBefore() 메서드 실행
	- 5\. nested1() 메서드 실행
	- 6\. nestedAfter() 메서드 실행
	- 7\. outerAfter() 메서드 실행
	
- 중첩된 클래스는 내부 클래스이므로 외부 클래스의 멤버에 접근할 수 있다. 이 특징을 활용하면 상황별로 중첩 테스트 클래스를 분리해서 테스트 코드를 구성할 수 있다.

```java
public class UserServiceTest {
	private MemoryUserRepository memoryRepo;
	private UserService userService;
	
	@BeforeEach
	void setup() {
		memoryRepo = new MemoryUserRepository();
		userService = new UserService(memoryRepo);
	}
	
	@Nested
	class GivenUser {
		@BeforeEach
		void givenUser() {
			memoryRepo.save(new User("user", "name"));
		}
		
		@Test
		void dupId() { ... }
	}
	
	@Nested
	class GivenNoDupId {
		...
	}
}
```

## 테스트 메시지

```java
List<Integer> ret = getResults();
List<Integer> expected = Arrays.asList(1,2,3);
for (int i = 0; i < expected.size(); i++) {
	assertEquals(expected.get(i), ret.get(i));
}
```

- 이 테스트 코드는 for 구문을 이용해서 List에 대한 검증을 수행하고 있다. 루프를 도는 과정에서 값 검증에 실패하면 다음과 같은 검증 실패 메시지가 출력된다.

```
org.opentest4j.AssertionFailedError:
Expected : 2
Actual : 6
```
- 이 에러 메세지만으로는 List의 몇 번째 요소를 검증하다가 실패했는지 알 수 없다. expected에서 기대값이 2인 요소가 몇 번째인지 코드를 뒤져야 한다. 예제 코드에서는 2인 요소가 한 개 뿐이지만 만약 2인 요소가 두 개 이상이면 찾기는 더 힘들어진다.
- 설명 문자열을 사용하면 이런 문제를 쉽게 해결할 수 있다. 다음 코드는 같은 테스트 코드에 assertEquals() 메서드의 세 번째 인자로 설명 문자열을 추가한 것이다.

```java
List<Integer> ret = getResults();
List<Integer> expected = Arrays.asList(1, 2, 3);
for (int i = 0; i < expected.size(); i++) {
	assertEquals(expected.get(i), ret.get(i), "ret[" + i + "]");
}
```
- assertEquals() 메서드의 세 번째 인자는 테스트에 실패할 때 표시할 메시지로 사용된다. 위 테스트 코드가 실패하면 다음과 같이 메세지로 전달한 문자열이 실패 안내 문구에 함께 표시된다.

```
org.opentest4j.AssertionFailedError: ret[1] ==> 
Expected: 2
Actual: 6
```

- 실패 문구를 보면 ret와 1번 인덱스 값을 검증하다 실패했다는 것을 쉽게 알 수 있다. 이렇게 한 테스트 메서드에 구분이 쉽지 않은 단언을 여러 번 하는 경우 메세지를 사용해서 각 단언을 쉽게 구분할 수 있다.

## @TempDir 애노테이션을 이용한 임시 폴더 생성
- 파일과 관련된 테스트 코드를 만들다 보면 임시로 사용할 폴더가 필요할 때가 있다. 테스트를 시작하기 전에 임시로 사용할 폴더를 만들고 임시 폴더에 파일을 생성하고 테스트가 끝나면 임시 폴더와 파일을 삭제하는 코드를 직접 만들 수 있지만, 이는 꽤 성가신 작업이다.
- JUnit 5.4 버전에 추가된 @TempDir 애노테이션을 사용하면 임시 폴더 관련 작업을 테스트 코드에서 쉽게 처리할 수 있다. @TempDir 애노테이션을 필드 또는 라이프사이클 관련 메서드나 테스트 메서드의 파라미터로 사용하면 JUnit은 임시 폴더를 생성하고 @TempDir 애노테이션을 붙인 필드나 파라미터에 임시 폴더 경로를 전달한다. @TempDir 애노테이션은 File 타입이나 Path 타입에 적용할 수 있다.

```java
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.io.TempDir;

import java.io.File;

public class TempDirTest {
    @TempDir
    File tempFolder;

    @Test
    void fileTest() {
        // tempFolder에 파일 생성등 작업
    }
}
```

- 위 코드는 File 타입의 tempFolder 필드에 @TempDir 애노테이션을 적용했다. 이 경우 테스트 메서드를 실행하기 전에 임시 폴더를 생성하고 그 폴더를 tempFolder 필드에 할당한다.
- 필드에 적용하면 각 테스트 메서드를 실행할 때마다 임시 폴더를 생성한다. 만약 특정 테스트 메서드에서만 임시 폴더를 생성해서 사용하고 싶다면 테스트 메서드의 파라미터에 @TempDir 애노테이션을 적용하면 된다.

```java
@Test
void fileTest(@TempDir Path tempFolder) {
	... 테스트 코드
}
```

- 테스트를 실행한 뒤에는 생성한 임시 폴더를 삭제한다. 물론 이 과정에서 임시 폴더에 작성한 파일도 함께 삭제한다.

- 특정 테스트 클래스 단위로 임시 폴더를 생성하고 싶다면 정적 필드에 @TempDir 애노테이션을 붙인다. 정적 필드에 @TempDir 애노테이션을 적용하면 각 테스트 메서드마다 임시 폴더를 생성하지 않는다. 대신 테스트 클래스의 모든 테스트 메서드를 실행하기 전에 임시 폴더를 한번 생성하고 모든 테스트 메서드의 실행이 끝난 뒤에 임시 폴더를 삭제한다.

```java
public class TempDirTest {
	@TempDir
	static File tempFolderPerClass;
	
	... 생략
}
```

- 정적 필드 대신 @BeforeAll 메서드의 파라미터에 @TempDir 애노테이션을 적용해도 된다.

```java
public class TempDirTest {
	@BeforeAll
	static void setup(@TempDir File tempFolder) {
		... 
	}
}
```

## @Timeout 애노테이션을 이용한 테스트 실행 시간 검증
- @Timeout 애노테이션은 JUnit 5.5 버전부터 지원하는 애노테이션으로 @Timeout 애노테이션을 사용하면 테스트가 일정 시간 내에 실행되는지 검증할 수 있다. 
- 다음은 @Timeout 애노테이션을 이용해서 테스트 메서드가 1초 이내에 실행되는지 검증하는 예를 보여준다.

```java
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.Timeout;

public class TimeoutTest {
    @Test
    @Timeout(1)
    void sleep2seconds() throws InterruptedException {
        Thread.sleep(2000);
    }
}
```

- 테스트 메서드 내에서 2초 동안 실행을 멈추므로 이 테스트의 실행 시간은 1초를 초과해서 실패하게 된다. 테스트 실패 메시지는 다음과 같다.
```
sleep2seconds() timed out after 1 second
java.util.concurrent.TimeoutException: sleep2seconds() timed out after 1 second
```

- 초가 아닌 다른 시간 단위를 사용하고 싶다면 unit 속성에 TimeUnit 값을 지정하면 된다. 다음은 밀리초 단위로 설정하는 예를 보여준다.

```java
@Test
@Timeout(value = 500, unit = TimeUnit.MILLISECONDS)
void sleep40Mills() throws InterruptedException {
	Thread.sleep(40);
}
```