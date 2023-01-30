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