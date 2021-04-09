Mybatis `mapper.xml` 파일을 `src/main/java` 폴더 안에서 사용하는 방법에 대해 알아보자.



이클립스에서 `Springboot` 와 `MySQL` 연동을 끝내고 다른 노트북에서 인텔리제이로 실행을 시켰더니 에러가 발생했다.

```
org.apache.ibatis.binding.BindingException: Invalid bound statement (not found)
```

해당 에러에 대해 찾아보니 잘 설명한 글이 있어 가져왔다.

[MyBatis 오류: Invalid bound statement (not found)](https://madplay.github.io/post/mybatis-invalid-bound-statement-not-found-error)



해당 글에 적혀있는 대로 전부 확인해보았지만 이상한 점은 없었다. 그래서 더 구글링해본 결과 mybatis 관련 설정을 해놓은 `application.yml` 파일이 `src/main/resources` 폴더 안에 있었기 때문에 `classpath` 가 `src/main/resources` , `src/main/java` 로 잡히는 것이 아니라 `src/main/resource`로 잡히기 때문에 `mapper` 파일을 찾지 못해 발생하는 에러였다. 당연히 `classpath` 라면 `java` 폴더도 사용할 수 있을 거라고 생각했고 실제로 이클립스에서는 코드가 잘 동작했기 때문에 인텔리제이로 실행했을 때 에러가 발생해서 매우 당황했다.

```yaml
mybatis:
  type-aliases-package: com.example.demo.**.dao
  mapper-locations: classpath:com/exmple/demo/**/sql/*Mapper.xml	# 바로 이부분
  configuration:
    map-underscore-to-camel-case: true

spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/test?serverTimezone=Asia/Seoul&characterEncoding=UTF-8
    username: root
    password: 1234
```



일반적으로 `mapper` 파일은 `src/main/resources` 밑에 폴더를 만들어서 관리하지만 필자는 `src/main/java`에서 `mapper` 파일을 관리하고 싶었기 때문에 방법을 찾아보았다.



`Maven`의 경우 `pom.xml`을 다음과 같이 수정하면 된다.

```xml
<build>
    <resources>
        <resource>
            <directory>src/main/resources</directory>
        </resource>
        <!-- Mapper -->
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.xml</include>
            </includes>
        </resource>
    </resources>
</build>
```



하지만 필자가 만든 프로젝트는 `Gradle` 이었기 때문에 다른 방법으로 수정이 필요했다. 그래서 `application.yml` 설정을 `Java Configuration`으로 변경했다.

###  Java Configuration 으로 변경

```java
@Configuration
@MapperScan(basePackages = {"com.example.demo.**.mapper"})
public class MybatisConfig {

    @Autowired
    ApplicationContext applicationContext;

    @Bean
    public SqlSessionFactoryBean sqlSessionFactory(DataSource dataSource) throws IOException {
        SqlSessionFactoryBean factoryBean = new SqlSessionFactoryBean();
        factoryBean.setDataSource(dataSource);
        // mybatis.type-aliases-package
        factoryBean.setTypeAliasesPackage("com.example.demo.**.dao");
        // mybatis.mapper-locations
        factoryBean.setMapperLocations(applicationContext.getResources("classpath:**/sql/*.xml"));
        // mybatis.configuration.map-underscore-to-camel-case
        Properties properties = new Properties();
        properties.put("mapUnderscoreToCamelCase", true);
        factoryBean.setConfigurationProperties(properties);

        return factoryBean;
    }

    @Bean
    public SqlSessionTemplate sqlSession(SqlSessionFactory sqlSessionFactory) {
        return new SqlSessionTemplate(sqlSessionFactory);
    }
}
```

`application.yml` 파일의 설정을 `Java Configuration` 으로 변경하면서 `SqlSessionFactoryBean` 에  관련설정을 추가했다.



### Mapper Interface 활용하기

이 방법은 번외 느낌으로 `xml`파일을 사용하지 않고 `@Mapper` 인터페이스를 활용하는 방법이다.

```java
@Mapper
public interface UserMapper {
    @Select("SELECT * FROM user WHERE uid = #{uid}")
    User findUser(String uid);
}
```

`@Select`, `@Insert`, `@Update`, `@Delete` 를 사용해서 `sql`문 생성이 가능하다. `@Select` 를 사용할 때 `@ResultMap` 어노테이션으로 `XML`에 설정된 `resultMap`으로 리턴이 가능하다.  어노테이션만 사용해서 바로 실행시킬 수 있는 장점이 있지만, 동적 쿼리를 사용할 수 없다는 단점이 있다. 더 자세히 알고싶다면 다음 URL을 참고하길 바란다. [How use @Select MyBatis annotation](https://examples.javacodegeeks.com/enterprise-java/mybatis/how-use-select-mybatis-annotation/)



### 정리

사실 `xml` 파일을 `resource` 폴더에서 관리하는 게 일반적이지만 필자처럼 `src/main/java` 폴더 밑에서 관리하고 싶은 사람들이 있을 것이다.  에러를 정리하면서 `classpath`에 대해 조금 더 정확하게 알 수 있었고 `Mapper Interface`를 활용하는 법에 대해 알게 되었다. 아래 링크는 `mybatis`에 대해 더 자세하게 알고 싶다면 참고하면 좋을 것 같다.



- [MyBatis 공식문서](https://mybatis.org/mybatis-3/ko/index.html)
- [MyBatis @Mapper 인터페이스는 어떻게 스프링 빈으로 와이어될 수 있을까?](http://wiki.plateer.com/pages/viewpage.action?pageId=7767258)