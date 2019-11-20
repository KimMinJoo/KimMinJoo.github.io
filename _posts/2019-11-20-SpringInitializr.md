---
layout: post
title: Spring Initializr를 이용해 SpringBoot Project 생성하고 분석하기
---
신규프로젝트 1번쨰 진행글

## 1. spring boot 프로젝트 생성
[Spring Initializr](https://start.spring.io/)를 이용해 생성.
일단은 아래와같이 셋팅. 필요한게 있으면 더 추가하고 필요없는게 있으면 빼도록하자.
- Project : Maven
- Language : Java
- Spring Boot : 2.2.1
- Dependencies : Lombok, Spring Web, Spring DataJDBC, MyBatisFramework

이렇게 선택하고 생성하면 zip파일이 생긴다.  
압축을 풀고 intelliJ에서 프로젝트로 생성하면 기본 프로젝트 골격 생성은 끝!  


<br><br><br><br><br><br>

## 2. 기본 pom.xml설정하기
Maven Project 이므로 생성 후 가장 기본적으로 pom.xml을 본다!  

### 생성후 pom.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.2.1.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.me</groupId>
	<artifactId>moniotor</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>demo</name>
	<description>demo project for Spring Boot</description>

	<properties>
		<java.version>11</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jdbc</artifactId>
		</dependency>
		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>2.1.1</version>
		</dependency>

		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<optional>true</optional>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
			<exclusions>
				<exclusion>
					<groupId>org.junit.vintage</groupId>
					<artifactId>junit-vintage-engine</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

</project>
```

일단 pom.xml을 위에서 부터 살펴보자.  

### `<moduleVersion>`  
`<modelVersion>`의 경우 4.0.0이라고 써있는데 이것은 maven의 pom.xml모델버전이라고한다. pom.xml도 버전이 있는모양.... 처음알았다.  
([출처](https://jeong-pro.tistory.com/168))  
근데 팀내 가장오래된 프로젝트도 4.0.0이다. 그냥 4.0.0만 쓰이는듯하다. 나중에 자세히 알아봐야겠다.  

### `<parent>`  
먼저 parent에 있는 항목들은 [Spring Initializr](https://start.spring.io/)에서 설정해준 값들로 잘 들어가있다. 
뭔지 모르겠다면 만들어보자 만들어보면 알 수 있다.
`<relativePath>`의 경우 내가 설정해준게아니지만 친절하게 영어로 써있다. maven도 상속이 가능한데 이때 parent가 상위폴더가 아닌 다른폴더에 있을때 설정해주는 값이다.  
ex)
 ``` <relativePath>../../ParentProject/pom.xml</relativePath> ```  
   
###  `<groupId>, <artifactId>, <version>, <name>, <description>`  
그 아래부분도 parent에 있는것과 같이 내가 설정해준 값으로 들어간다. 따라서 설명생략!  
모르겠다면 아까말했듯이 한번 만들어보면 알 수 있다. (그래도 모르면 물어본다.)  
   
###  `<properties>`  
다음은 properties다.
여기엔 기본적으로 java.version이 들어가있다. 이건 내가 선택한버전인 11로 들어가있다.  
[Spring Initializr](https://start.spring.io/)에서 처음 만들때 버전을 잘못선택했다면 여기서 바꿔줄것!  
근데 보통 properties라면 다른곳에서 사용하기 마련인데 java.version은 사용하는데도 없는데 기본으로 들어간다!?!?  
찾아보니 SpringBoot명세라고 한다. ([출처](https://www.baeldung.com/maven-java-version))  
참고로 SpringBoot 2는 1.8부터 동작한다.  
  
  
### `<dependencies>`
이건 아까 선택한 애들이 들어가있다.  
1.spring boot 프로젝트 생성 의 dependencies에서 선택한 애들을 보자  
### 1. Lombok
```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
</dependency>
```
### 2. Spring Web,
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```
### 3. Spring DataJDBC
내가 넣고 싶었던건 spring-boot-starter-jdbc였는데  엉뚱한게 들어갔다... 바꿔주도록하자!  
선택하는 화면에서 JDBC API를 선택했어야했다.... 따로 확인해봤다!
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jdbc</artifactId>
</dependency>
```
### 4. MyBatisFramework
```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.1</version>
</dependency>
```
### 5. test
이건 내가 선택한건 아니지만 기본으로 들어가는듯하다.  
다들 spring처럼 test를 기본으로 생각하는 개발습관을 기르자.  
여기는 특이하게 exclusion이 들어가있다.  
vintage-engine은 junit4를 실행해주는 engine인데 SpringBoot에서 junit5가 기본이 된것같다.(몇 버전부터인지는 모른다.)  
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
    <exclusions>
        <exclusion>
            <groupId>org.junit.vintage</groupId>
            <artifactId>junit-vintage-engine</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```


### `<build>`
spring-boot-maven-plugin 이 들어있다.  
설명하자면 길다.  
[여기](https://docs.spring.io/spring-boot/docs/current/maven-plugin/usage.html)에서 확인하자.



<br><br><br><br><br><br>


## 3. pom.xml수정
## `<relativePath>`
불필요하여 삭제했다.

## `<dependencies>`
### 1. spring-boot-starter-tomcat 분리
가장 먼저 spring-boot-starter-tomcat을 분리했다.  
spring-boot-starter-tomcat의 경우 원래 spring-boot-starter-web에서 의존성을 가지기 때문에 따로 안해줘되지만 버전관리등의 이유로 따로 관리하고싶었다.
(쓸데없는 선택이 아니길)

### 2. mockito, commons-lang3, commons-collections4추가
이 3개는 꼭 쓸거같으니 미리 추가해줬다. 아마 99.9999%는 쓸것이다.


## `<build>` 수정

### 1. testResources 추가
```xml
<testResources>
    <testResource>
        <directory>src/test/resources</directory>
    </testResource>
</testResources>
```

### 2. spring-boot-maven-plugin 수정
### `<executions>` 추가
인프라상태상 build서버와 배포서버가 다르기 때문에 기본 repackage로 해준다.  
local에서는 intelliJ 기능으로 커버한다!  
```xml
<executions>
    <execution>
        <goals>
            <goal>repackage</goal>
        </goals>
    </execution>
</executions>
```

### `<configuration>`
배포 경로 및 배포 파일명을 정해준다.
```xml
<configuration>
    <outputDirectory>${deploy-path}</outputDirectory>
    <finalName>${deploy-name}</finalName>
</configuration>
```

### 3. clean plugin 추가
빌드경로와 배포경로가 다르다면 이건 꼭꼭 해줘야하는 설정이므로 추가!
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-clean-plugin</artifactId>
    <configuration>
        <filesets>
            <fileset>
                <directory>${deploy-path}</directory>
                <includes>
                    <include>**/*</include>
                </includes>
            </fileset>
        </filesets>
    </configuration>
</plugin>
```


## 4. application.yml 작성
보통 application.properties로 작성하지만 요즘 yml도 많이 작성을 하고있고 팀내에서 한번도 사용안해봤으므로 사용법을 익히고 공부하잔 의미로 yml채택  
  
일단 yml의 장점은 단계를 나눌 수 있어서 조금 더 보기편하다!  
아래는 예시! 인터넷에 올리기 힘든 DB정보여서 값들은 가렸다.  
```yml
server:
  port: 8088

---
#app
app:
  datasource:
    oracle:
      common:
        driver-class-name: oracle.jdbc.driver.OracleDriver
        url: jdbc:디비주소
        username: 계정이름
        password: 비밀번호
        minimum-idle: 0
        maximum-pool-size: 10
      oth:
        driver-class-name: oracle.jdbc.driver.OracleDriver
        url: jdbc: 디비주소
        username: 계정이름
        password: 비밀번호
        minimum-idle: 0
        maximum-pool-size: 10
```


## 5. Profile설정
일단 Profile설정은 보통은 Maven에 들어간다.  
근데 여기선 spring.profiles.active을 이용해보려한다.  
Maven Profile설정도 필요하게된다면 아마도 Maven Profile로 합치게 되지않을까싶다.  
  
spring.profiles.active를 사용한다면 local, alpha등 환경과 상관없이 빌드하고 run하는 시점에 -Dspring.profiles.active={profile}을 넣어주면 알아서 실행할 수있다는 장점이있다!  
  
resources폴더아래 아래와 같이 만들어줬다.  
![application.yml](https://github.com/kmjoo91/kmjoo91.github.io/blob/master/images/application.png?raw=true)
  
또한 IntelliJ에서 SpringBoot를 실행시킬땐 아래화면처럼 해준다.  
![intelliJActiveProfile](https://github.com/kmjoo91/kmjoo91.github.io/blob/master/images/intellijActivProfile.jpg?raw=true)