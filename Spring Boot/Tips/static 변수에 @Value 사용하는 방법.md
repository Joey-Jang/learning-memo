# static 변수에 @Value 사용하는 방법

### ⚠️ 문제 상황
application.properties의 값을 static field로 받아서 사용하는 경우가 있음.

그러나 @Value 어노테이션은 static field를 초기화하지 않기 때문에 조회해보면 null이 나옴.
```java
@Component
public class SampleComponent {

    @Value("${spring.profiles.active}")
    public static String profile;

}
```

### ❓원인
@Value 어노테이션은 BeanPostProcessor에 의해 처리됨.

즉 등록된 Bean에 한해서 처리되는데 static 변수는 Bean 등록 여부와 상관없이 Static 영역에 할당되기 때문에,  
Spring은 static field에 대한 @Value 어노테이션을 지원하지 않음.

### ✅ 해결 방법
해당 static field에 대한 setter method를 만들고 @Value 어노테이션을 어노테이트함.
```java
@Component
public class SampleComponent {

    public static String profile;

    @Value("${spring.profiles.active}")
    public void setProfile(String profile)
    {
        this.profile = profile;
    }

}
```
