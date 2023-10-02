

SpringBoot REST+Mybatis+H2

1. 개요
스프링부트를 사용하여 RESTful 웹서비스를 위한 기본 구조 작성
(h2의 경우 내장 DB로 pom.xml 추가 외 별도 설정 불필요)
2. 프로젝트 구조(이클립스 JAVA EE perspective의 Project Explorer 기준)
/MyProject
/src/main/java
/com.example.demo
MyProjectApplication.java
/com.example.demo.controller
TestController.java
/com.example.demo.mapper
TestMapper.java
/src/main/resource
/mapper
TestMapper.xml
/static
/templates
application.properties
mybatis-config.xml
data.sql
schema.sql
pom.xml
3. pom.xml 설정
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="[http://maven.apache.org/POM/4.0.0](http://maven.apache.org/POM/4.0.0)" xmlns:xsi="[http://www.w3.org/2001/XMLSchema-instance](http://www.w3.org/2001/XMLSchema-instance)"
xsi:schemaLocation="[http://maven.apache.org/POM/4.0.0](http://maven.apache.org/POM/4.0.0) [http://maven.apache.org/xsd/maven-4.0.0.xsd](http://maven.apache.org/xsd/maven-4.0.0.xsd)">
<modelVersion>4.0.0</modelVersion>
    
    ```
    <groupId>com.example</groupId>
     <artifactId>MyProject</artifactId>
     <version>0.0.1-SNAPSHOT</version>
     <packaging>jar</packaging>
    
     <name>MyProject</name>
     <description>Demo project for Spring Boot</description>
    
     <parent>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter-parent</artifactId>
         <version>2.0.3.RELEASE</version>
         <relativePath/> <!-- lookup parent from repository -->
     </parent>
    
     <properties>
         <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
         <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
         <java.version>1.8</java.version>
     </properties>
    
     <dependencies>
         <dependency>
             <groupId>org.springframework.boot</groupId>
             <artifactId>spring-boot-starter-web</artifactId>
         </dependency>
         <dependency>
             <groupId>org.mybatis.spring.boot</groupId>
             <artifactId>mybatis-spring-boot-starter</artifactId>
             <version>1.3.2</version>
         </dependency>
         <dependency>
             <groupId>org.springframework.boot</groupId>
             <artifactId>spring-boot-starter-tomcat</artifactId>
             <scope>provided</scope>
         </dependency>
         <dependency>
             <groupId>com.h2database</groupId>
             <artifactId>h2</artifactId>
             <scope>runtime</scope>
         </dependency>
         <dependency>
             <groupId>org.springframework.boot</groupId>
             <artifactId>spring-boot-starter-test</artifactId>
             <scope>test</scope>
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
    ```
    
    </project>
    
4. application.properties 설정
spring.h2.console.enabled=true
mybatis.config-location=classpath:mybatis-config.xml
mybatis.mapper-locations=classpath:mappers/*.xml
5. import 데이터 작성
<schema.sql>
create table student(
id integer not null,
name varchar(255) not null,
passport_number varchar(255) not null,
primary key(id)
);
<data.sql>
insert into student values(10001,'Ranga', 'E1234567');
insert into student values(10002,'Ravi', 'A1234568');
6. mybatis-config.xml 설정
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-[//mybatis.org//DTD](https://mybatis.org//DTD) Config 3.0//EN"
"[http://mybatis.org/dtd/mybatis-3-config.dtd](http://mybatis.org/dtd/mybatis-3-config.dtd)">
<configuration>
<mappers>
<mapper resource="mapper/TestMapper.xml"/>
</mappers>
</configuration>
7. TestController.java 작성
@RestController
@RequestMapping("/test/*")
public class Test {
    
    ```
    @Autowired
     TestMapper testMapper;        
    
     @GetMapping("/info")
     public List<Map<String,Object>> test() {            
         return testMapper.test();
     }
    ```
    
    }
    
8. TestMapper.java 작성
@Mapper
public interface TestMapper {
List<Map<String,Object>> test();
}
9. TestMapper.xml 작성
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-[//mybatis.org//DTD](https://mybatis.org//DTD) Mapper 3.0//EN"
"[http://mybatis.org/dtd/mybatis-3-mapper.dtd](http://mybatis.org/dtd/mybatis-3-mapper.dtd)">
<mapper namespace="com.example.demo.mapper.TestMapper">
<select id="test" resultType="java.util.Map">
select * from student
</select>
</mapper>
10. 테스트
가. 스프링부트 프로젝트 기동
나. 브라우저 실행
다. h2 콘솔 접속([http://localhost:8080/h2-console](http://localhost:8080/h2-console) > JDBC URL 칸에 jdbc:h2:mem:testdb 입력 > Connect 클릭) 후 Student 테이블 유무 확인
라. 주소창에 요청 입력 및 리턴 값 확인
요청 - [http://localhost:8080/test/info](http://localhost:8080/test/info)
응답 - [{"PASSPORT_NUMBER":"E1234567","ID":10001,"NAME":"Ranga"},{"PASSPORT_NUMBER":"A1234568","ID":10002,"NAME":"Ravi"}]