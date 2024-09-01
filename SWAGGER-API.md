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

> src/resources/application.yml

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


