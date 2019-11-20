---
layout: post
title: Spring Boot Project에 DataBase 연동하기
---

이글은 신규 프로젝트 진행정리 2번쨰글입니다.
이전글들을 읽으시면 이해가 더 편합니다.
[1편 Spring Initializr를 이용해 SpringBoot Project 생성하고 분석하기](https://kmjoo91.github.io/SpringInitializr/)

DB연동하기전에 pom.xml에 뺴먹은사항부터 추가한다. 
## pom.xml추가사항

### `<repositories>`추가
```xml
<repositories>
        <repository>
            <id>사내 레파지토리</id>
            <name>사내 레파지토리</name>
            <url>http://사내레파지토리</url>
        </repository>
        <repository>
            <id>사내 스냅샷 레파지토리</id>
            <name>사내 스냅샷 레파지토리</name>
            <url>http://사내스냅샷레파지토리y</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
            <releases>
                <enabled>false</enabled>
            </releases>
        </repository>
    </repositories>

    <pluginRepositories>
        <pluginRepository>
            <id>사내 레파지토리</id>
            <name>사내 레파지토리</name>
            <url>http://사내레파지토리</url>
        </pluginRepository>
    </pluginRepositories>
```


### `<profiles>` 추가
```xml
<profiles>
        <profile>
            <id>local</id>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <properties>
                <profile.id>local</profile.id>
            </properties>
        </profile>

        <profile>
            <id>alpha</id>
            <properties>
                <profile.id>alpha</profile.id>
            </properties>
        </profile>

        <profile>
            <id>release</id>
            <properties>
                <profile.id>release</profile.id>
            </properties>
        </profile>
    </profiles>
```


이제 DataBase를 연결한다!!!!
# DataBase 연결

### HikariCP
여러 CP가 존재하지만 HikariCP채택.
이유는 여러가지가 존재한다.
- 성능상 가장 뛰어나다고한다.
- 요즘 많이 쓰고있어서 쓰고싶다.
- spring boot jdbc에서 기본으로 제공하여 다른 dependency가 필요없다.
 ![HikariCP의존성](https://github.com/kmjoo91/kmjoo91.github.io/blob/master/images/HikariCP%EC%9D%98%EC%A1%B4%EC%84%B1.png?raw=true)  
<br>

### HikariCP 설정 필수값
누가봐도 당연히 설정해야하므로 설명은 넘어간다.
- dataSourceClassName
- jdbcUrl
- username
- password


### 필수값 외 설정값
- maximumPoolSize : 데이터베이스에 대한 최대 실제 연결수.
- connectionTimeout : 데이터베이스와 connection을 맺을 때 limit시간지정. 초과시 SQLException발생
- 연결이 되어있는지 테스트할 limit시간지정. connectionTimeout보다 작아야한다.

### 설정하려다 안한값.
- minimumIdle : 아무런 일을 하지않아도 적어도 이 옵션에 설정 값 size로 커넥션들을 유지. 설정을 하지않은 이유는 성능이 중요한 component인데 설정하지 않을 때 최적의 성능이 나온다고한다. (default: same as maximumPoolSize)

### 나머지 설정값
[여기](https://github.com/brettwooldridge/HikariCP)를 참고한다.



<br><br><br><br><br>

## DataSource 생성
### 1. 설정값 생성 모델정의
```java 
@Setter
@Getter
public class DataSourceProperties {
	private String driverClassName;
	private String url;
	private String username;
	private String password;
	private int maximumPoolSize = 10;
	private long connectionTimeout = 30000L;
	private long validationTimeout = 5000L;
}
```


### 2. 설정값을 이용해 DataSource 생성코드 작성
uMon의 경우 여러 DB에 접속해야하기때문에 DataSource를 여러개 생성해야한다.  
따라서 설정정보를 담은 객체를 받아 DataSource를 생성해주는 메소드를 만들어 설정정보만 바꾼 DataSource를 만들수 있도록한다.  

```java
private HikariDataSource createHikariDataSource(DataSourceProperties datasourceProperties) {
        //필수 값은 Builder를 이용해 셋팅.
		HikariDataSource hikariDataSource = DataSourceBuilder.create()
			.type(HikariDataSource.class)
			.driverClassName(datasourceProperties.getDriverClassName())
			.url(datasourceProperties.getUrl())
			.username(datasourceProperties.getUsername())
			.password(datasourceProperties.getPassword())
			.build();

        // 설정값 적용
		hikariDataSource.setMaximumPoolSize(datasourceProperties.getMaximumPoolSize());
		hikariDataSource.setConnectionTimeout(datasourceProperties.getConnectionTimeout());
		hikariDataSource.setValidationTimeout(datasourceProperties.getValidationTimeout());

		return hikariDataSource;
	}
```


### 3. yml에서 설정값 읽어와 DataSource 생성
@ConfigurationProperties을 이용해 설정값을 읽어온 DataSourceProperties객체 생성.

### @ConfigurationProperties이란?
properties나 yaml, yml 파일에서 값을 읽어서 셋팅해주는 어노테이션이다.

prefix를 지정해 거기에 맞는 값들을 읽어셋팅해준다.
위의 DataSourceProperties클래스를 예로 들어서보자.  
  
application.yml파일예시
```yaml
app:
  datasource:
    oracle:
      common:
        driver-class-name: oracle.jdbc.driver.OracleDriver
        url: jdbc:url
        username: id
        password: password
        maximum-pool-size: 10
```

@ConfigurationProperties 예시
```java
@Bean
@ConfigurationProperties(prefix = "app.datasource.oracle.common")
public DataSourceProperties commonDatasourceAccessProperties() {
    return new DataSourceProperties();
}
```

위의 예시처럼 소스코드를 구성한다면 DataSourceProperties에 setter를 이용해 driverClassName, url, username, password, maximumPoolSize가 셋팅된다.

application.properties파일에서는 driver-class-name만 예시로 들겠다.
읽어올 수 있는 Property 목록
- app.datasource.oracle.common.driver-class-name
- app.datasource.oracle.common.driverClassName
- app.datasource.oracle.common.driver_class_name
- APP_DATASOURCE_ORACLE_COMMON_DRIVER_CLASS_NAME


### Location속성
application.yml 이 아닌 다른 yml파일에서 Property를 읽어올 수 있게 해주는 속성이다.
그러나 현재 Spring Boot에서는 사라진 속성이다.


### Location대체 방법
작은 프로젝트라면 application.yml만으로도 충분하지만 나눠야할 때가 있을것이다.
그럴때 @PropertySource를 사용하는 방법이 있지만 맘에 들지 않는다.....따라서 설명은 [여기](https://kingbbode.tistory.com/39)로 대체한다.  
  
 내 경우에 여러 방법을 시도해봤지만 그나마 맘에드는 방식은.....
 
 application.yml에 spring.profiles.include를 활용하는 방식이다.
 appication-{profiles}.yml을 읽어오므로 profiles에 여러가지를 추가해주는 방식이다.
 
예를 들면 DB설정을 application-datasource-local.yml, application-datasource-dev.yml, application-datasource-release.yml등으로 나누고, 그냥 설명을 위해 application-etc.yml도 있다고 치자.

그럼 application.yml 에 아래 코드를 추가해주면 된다.
```yaml
spring:
  profiles:
    include: datasource-${spring.profiles.active}, etc
```



## 4. Mybatis 설정
기존 컴포넌트의 설정을 거의 가져왔으며 분석만했다.

### 1. SqlSessionFactoryBean
DataSource가 여러개이므로 간단하게 DataSource를 파라미터로 받아 SqlSessionFactoryBean을 만들어 return하는 메소드를 생성
Bean 생성때만 쓰이는데 Config파일이 나뉘어져 따로 Config파일을 만들고 static으로 만들었다.
```java
static SqlSessionFactoryBean createSqlSessionFactoryBean(DataSource dataSource) throws IOException {
    SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
    sqlSessionFactoryBean.setDataSource(dataSource);
    sqlSessionFactoryBean.setMapperLocations(new PathMatchingResourcePatternResolver().getResources("classpath*:/resource/mapper/**/mapper/*.xml"));
    sqlSessionFactoryBean.setConfigLocation(new ClassPathResource("mybatis/mybatisConfiguration.xml"));

    return sqlSessionFactoryBean;
}
```


### 2. TransactionManager
TransactionManager도 마찬가지로 DataSource만 파라미터로 받아 TransactionManager를 return하는 메소드를 생성했다.
DataSourceTransactionManager을 그대로 사용한 이유는 Mybatis공식 문서에서 Mybatis에 종속적인 새로운 트랜잭션 관리를 만드는것 보다,  
Spring에 자연스럽게 연동될 수 있는 DataSourceTransactionManager이 좋다고해서 사용한다.
```java
static DataSourceTransactionManager createDataSourceTransactionManager(DataSource dataSource) {
    DataSourceTransactionManager transactionManager = new DataSourceTransactionManager(dataSource);
    //TODO 설정해야만할까?
    transactionManager.setGlobalRollbackOnParticipationFailure(false);

    return transactionManager;
}
```

### GlobalRollbackOnParticipationFailure
이 설정의 경우 간단히 설명하자면 실패한 트랜잭션이 한개라도 존재한다면 전부 롤백을 시키는 옵션이다. (defualt: true)
일단 그냥 가져와서 false로 설정했지만 이 컴포넌트에서는 그게 필요할까싶다....
비지니스 적인 부분이라 코드리뷰등의 자리에서 팀원들과 토론 후 설정하는게 좋을것같다.
이 설정에 대해서는 우아한형제들 [포스트](http://woowabros.github.io/experience/2019/01/29/exception-in-transaction.html)에 잘 설명되어있다.

나머지 설정 및 함수들은 크게 설명할 필요는 없어보인다.
보고싶다면 [여기](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/jdbc/datasource/DataSourceTransactionManager.html)를 참고


### 3. MapperScannerConfigurer
Mapper들을 하나씩 등록하지 않고 Scan해주는 아주아주편리한 Class이다.

### BasePackage
java doc과 구글 번역이 설명을 다 해준다.
![BasePackageJavaDoc](https://github.com/kmjoo91/kmjoo91.github.io/blob/master/images/BasePackageJavaDoc.png?raw=true)
다만 찾아보니 BasePackage만 설정할 경우 Package하위의 모든 interface들을 Mapper로 등록한다고한다.

### AnnotationClass
이것도 java doc과 구글 번역이 거의다 설명해준다.
![AnnotationClassJavaDoc](https://github.com/kmjoo91/kmjoo91.github.io/blob/master/images/AnnotationClassJavaDoc.png?raw=true)
다만 지정된 주석이라는 부분이 어노테이션을 뜻하는것 같다.
그리고 markerInterface라는 것과 결합이 가능하다고하니 이것도 봐야겠다.......

일단 이걸 사용하는 것은 이 컴포넌트의 경우 여러개의 DataSource를 사용한다. 그러다 보니 BasePackage만 설정할 경우 하나의 Mapper가 여러개의 SqlSessionFactoryBean에 등록될 수 있다. 그래서 이걸 사용해 보완했다.
또한 그럴일은 적겠지만 MapperInterface가 다른 Interface와 같은 package에 있다면 사용해야겠다.

### MarkerInterface
얘는 좀 java doc과 구글번역이 일을 덜한다.
![MarkerInterfaceJavaDoc](https://github.com/kmjoo91/kmjoo91.github.io/blob/master/images/MarkerInterfaceJavaDoc.png?raw=true)

대충 해석하자면 Mapper가 상속받는 부모를 지정해주는 역할을 하는것같다.
MarkerInterface란 이름도 Mapper가 interface일테고 부모도 Interface인데 부모가 얘네만 scan하라고 Marking하는 역할을 한다고 보고 지은것같다.

### SqlSessionFactoryBeanName
SqlSessionFactoryBean을 지정해준다. 더이상의 자세한 설명은 생략한다.




<br><br><br><br><br><br>
아마도 이정도면 DB연동은 끝난것 같다.
시간관계상 테스트 및 테스트글은 다음에 작성!