# SWAGGER-API 
> SWAGGER API는 API 문서를 자동 생성해주는 도구 입니다.

## 의존성 추가 

> build.gradle

```groovy
...

dependencies {
  ...

	implementation 'org.springdoc:springdoc-openapi-starter-webmvc-ui:2.6.0'

  ...
}

...

```

## 설정 하기 

### src/resources/application.yml

```yaml

...

springdoc:
  version: '1.0.0'
  api-docs:
    path: /api-docs
  default-consumes-media-type: application/json
  default-produces-media-type: application/json
  swagger-ui:
    operations-sorter: alpha
    tags-sorter: alpha
    path: /swagger-ui.html
    disable-swagger-default-url: true
    doc-expansion: none
  paths-to-match:
    - /api/**
```

- version: springdoc 라이브러리의 버전을 설정한다.
- api-docs: API 문서가 제공될 경로를 /api-docs로 설정한다.
- default-consumes-media-type 및 default-produces-media-type: API에서 사용할 기본 미디어 유형을 application/json으로 설정한다.
- swagger-ui: Swagger UI의 외관 및 동작을 설정한다.
- operations-sorter : UI에서 작업을 알파벳 순서로 정렬한다.
- tags-sorter: Swagger : UI에서 태그를 알파벳 순서로 정렬한다.
- path: Swagger UI에 액세스할 수 있는 경로를 /swagger-ui.html로 설정한다.
- disable-swagger-default-url: 기본 Swagger URL을 비활성화하고 명시적인 경로 /swagger-ui.html로만 접근하도록 한다.
- display-query-params-without-oauth2: Swagger UI에서 OAuth2 없이 쿼리 매개변수를 표시한다.
- doc-expansion: Swagger UI에서 문서 확장을 비활성화한다.
- paths-to-match: API 문서화에 포함할 경로 패턴을 설정한다. 여기서는 /api/** 로 시작하는 모든 경로가 문서화된다.


### ...global/configs/SwaggerConfig.java

```java
package org.choongang.project.global.configs;

import io.swagger.v3.oas.annotations.OpenAPIDefinition;
import io.swagger.v3.oas.annotations.info.Info;
import lombok.RequiredArgsConstructor;
import org.springdoc.core.models.GroupedOpenApi;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@OpenAPIDefinition(info=@Info(title = "API 명세서", description = "API 명세서 설명", version = "v1"))
@Configuration
@RequiredArgsConstructor
public class SwaggerConfig {
    @Bean
    public GroupedOpenApi memberApi() {
        String[] paths = {"/v1/**"};

        return GroupedOpenApi.builder()
                .group("회원 API v1") // 그룹 이름을 설정한다.
                .pathsToMatch(paths) // 그룹에 속하는 경로 패턴을 지정한다.
                .build();
    }
}
```

- @OpenAPIDefinition: OpenAPI 명세서의 기본 정보를 정의한다. 여기서는 API의 제목, 설명, 버전을 설정한다.
- memberApi 메서드: "/v1/**" 경로에 매칭되는 API를 "회원 API v1"이라는 그룹으로 묶어서 문서화한다.
GroupedOpenApi.builder(): GroupedOpenApi를 생성하는 빌더를 시작한다.
- group("회원 API v1"): 그룹의 이름을 설정한다.
- pathsToMatch(paths): 그룹에 속하는 경로 패턴을 설정한다.
- build(): GroupedOpenApi 객체를 빌드하여 반환한다.


## 적용하기

```java
package org.choongang.project.member.controllers;

import io.swagger.v3.oas.annotations.Operation;
import io.swagger.v3.oas.annotations.Parameter;
import io.swagger.v3.oas.annotations.Parameters;
import io.swagger.v3.oas.annotations.responses.ApiResponse;
import io.swagger.v3.oas.annotations.tags.Tag;
import org.choongang.project.member.entities.Member;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@Tag(name = "User", description = "회원 API")
@RestController
@RequestMapping("/api/member")
public class MemberController {

    @Operation(
            summary = "회원가입",
            description="회원가입 처리"
    )
    @ApiResponse(
            responseCode = "200",
            description = "회원가입 성공시 응답"
    )

    @Parameters({
            @Parameter(name="email", required = true, description="이메일"),
            @Parameter(name="password", required = true)
    })
    @PostMapping
    public ResponseEntity<Member> join(@RequestBody RequestJoin join) {

        return ResponseEntity.status(HttpStatus.CREATED).body(new Member());
    }

    @GetMapping("/info/{email}")
    public String get(@PathVariable("email") Long email) {
        return "test";
    }
}
```

- @Tag로 설정된 name이 같은 것 끼리 하나의 api 그룹으로 묶고, 그룹에 대한 설명을 기술할 수 있다.
- @Operation으로 메서드에 대한 설명을 기술할 수 있다.
- @ApiResponse로 응답 결과에 따른 response 구조를 미리 확인할 수 있다.
- @Parameter : 요청 매개변수에 대한 자세한 설명을 기술할 수 있다.
- @Parameters : @Parameter를 그룹화 하여 묶는다.

## 적용결과



