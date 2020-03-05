---
title: "[Spring boot] 커스텀 설정 프로퍼티 클래스 Test 하기"
categories: spring-boot 
---

스프링 부트 커스텀 설정 클래스 사용하기 
---
스프링 부트는 외부 설정 파일 설정을 properties or yml 파일을 통해 간단하게 제공 하고 있다.
그 중 한가지 방법을 사용하고 테스트 코드를 통해 확인 하는 방법을 소개 하고자 한다.

우선 예제로 사용할 간단한 설정 파일과 맵핑될 class 파일을 생성 한다.

#### application.yml
```yml
acme:
  remote-address: 192.168.1.1
  security:
    username: admin
    roles:
      - USER
      - ADMIN
```

#### AcmeProperties.clas
```java
@Data
@Component
@ConfigurationProperties(prefix = "acme")
public class AcmeProperties {

    private boolean enabled;
    private InetAddress remoteAddress;
    private final Security security = new Security();

    @Data
    public static class Security {
        private String username;
        private String password;
        private List<String> roles = new ArrayList<>(Collections.singleton("USER"));
    }
}
```

#### 사용한 Annotation 의미 

| Annotation | Description |
|---|---|
| `@Data` | getter/setter/toString 등 자동 생성 ( lombok ) |
| `@Component` | class를 Bean에 등록|
| `@ConfigurationProperties` | 프로퍼티 값을 객체에 바인딩|

`@ConfigurationProperties`의 `prefix=acme` 설정을 통해 `application.yml`의 `acme` 하위에 있는 proeprties가 class에 맵핑 된다. ( `prefix` 설정 하지 않을 경우엔 root를 의미함 )
`@Componet`를 사용하여 container에 등록 해준다 .

위의 간단한 코드를 통해 spring-boot에서 load되는지 확인 해보자.

#### ExternalConfigApplication.class
```java
@SpringBootApplication
public class ExternalConfigApplication {

	public static void main(String[] args) {
		SpringApplication.run(ExternalConfigApplication.class, args);
	}
}
```

#### MyService.class
```java
@Service
public class MyService {

    private final AcmeProperties properties;

    @Autowired
    public MyService(AcmeProperties properties) {
        this.properties = properties;
    }

    @PostConstruct
    public void openConnection() {
        System.out.println(this.properties.getRemoteAddress());
        System.out.println(this.properties.getSecurity().getUsername());
        System.out.println(this.properties.getSecurity().getRoles());
    }
}
```
외부 설정파일을 값을 확인을 위해 간단한 `@Service`를 통한 확인 결과.
```bash
192.168.1.1
admin
[USER, ADMIN]
```

스프링 부트 커스텀 설정 클래스 Test 하기 
---

#### AcmePropertiesTest.class
```java
@RunWith(SpringRunner.class)
@EnableAutoConfiguration
@ContextConfiguration(classes = AcmeProperties.class, initializers = ConfigFileApplicationContextInitializer.class)
public class AcmePropertiesTest {

    @Autowired
    private AcmeProperties properties;

    @Test
    public void givenDefaultTPS_whenVariableRetrieved_thenDefaultFileReturned() {
        assertThat("192.168.1.1",  is(this.properties.getRemoteAddress()));
        assertThat("admin",  is(this.properties.getSecurity().getUsername()));
        assertThat(Arrays.asList("USER", "ADMIN"),  is(this.properties.getSecurity().getRoles()));
    }
}
```

| Annotation | Description |
|---|---|
| `@RunWith` | 테스트러너 지정  |
| `@EnableAutoConfiguration` | Configure bean을 자동으로 등록  |
| `@ContextConfiguration` | 설정 파일의 위치를 지정할 때 사용 |

위 테스트 코드에서 `@ContextConfiguration`에 `initializers`를 지정 해주지 않으면 `application.yml`의 `properties`을 읽어 오지 못 한다.


> 작성된 예제 코드는 [github](https://github.com/wildwhale/spring-boot/tree/master/external-config)에 공개 되어 있습니다.

 

