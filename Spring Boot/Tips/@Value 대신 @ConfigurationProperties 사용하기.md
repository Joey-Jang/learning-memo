# @Value 대신 @ConfigurationProperties 사용하기

### ⚠️ 변경 전
application.properties의 값을 활용하기 위해서 @Value를 사용하고 있음.

매번 빈 내부에서 @Value을 사용하여 값을 가져와야하는 불편함이 있음.

@ConfigurationProperties를 사용하여 빈으로 등록하기로 함.

### ♻️ 변경 후
@ConfigurationProperties과을 활용하여 application.properties의 값들을 Bean으로 바인딩함.
```java
@RequiredArgsConstructor
@Getter
@ConfigurationProperties(prefix = "spring")
public final class SpringAppProperties {

    private final Profiles profiles;

    @RequiredArgsConstructor
    @Getter
    public static final class Profiles {
        private final ProfileEnum active;
    }

}
```

@EnableConfigurationProperties를 사용하여 @ConfigurationProperties annotated 클래스들을 Bean으로 등록해줘야 함.
```java
@EnableConfigurationProperties({SpringAppProperties.class})
@Configuration
public class ConfigurationPropertiesEnablizer {
}
```

이제 다른 Bean에서 spring.profiles.active의 값을 얻기 위해서는 SpringAppProperties을 주입 받아 .getProfiles().getActive()을 호출하면 됨.

### ⛔ 변경 후 이슈  
자주 쓰는 값들은 static 변수로 선언하여, 매번 Bean을 주입 받아서 getter로 값을 꺼내오지 않아도 접근 가능하게 하고 싶었음.

### ✅ 최종 변경안
자주 쓰는 값들을 static field로 선언하고 @PostConstruct 어노테이션을 활용해 수동으로 초기화 해줌. 
```java
@RequiredArgsConstructor
@Getter
@ConfigurationProperties(prefix = "spring")
public final class SpringAppProperties {

    private final Profiles profiles;

    @RequiredArgsConstructor
    @Getter
    public static final class Profiles {
        private final ProfileEnum active;
    }

    //================================
    //  static fields
    //================================

    public static boolean IS_PROD;

    @PostConstruct
    public void initializeStaticFields()
    {
        SpringAppProperties.IS_PROD = ProfileEnum.PROD.equals(this.getProfiles().active);
    }

}
```
