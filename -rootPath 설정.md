## spring 처음 시작할때

pom.xml에서
<java-version>11</java-version>
		<org.springframework-version>5.2.21.RELEASE</org.springframework-version>

으로 자바 버전 11로 스프링버전 5.2.21로 바꾸기

     <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>2.5.1</version>
                <configuration>
                    <source>${java-version}</source>
                    <target>${java-version}</target>

메이븐 플러그인에서 
  <source>${java-version}</source>
                    <target>${java-version}</target>

${java-version}설정하기

메이븐에서 slf4j 최신버전 릴리즈





## -rootPath 설정

preferences - Web - JSP Files - Editor - Templates - html5 선택

<%@ taglib uri="http://java.sun.com/jsp/jstl/core"  prefix="c" %>
<c:set value="${pageContext.request.contextPath}" var="rootPath" />
넣기 

앞에 $를 하나 더 붙여야 문자열로 인식한다.
$${pageContext.request.contextPath}




## 파일 왼쪽 클릭했을때 뜨게 하는거 설정
perspective - customise perspective - file - new 선택





## <!--  DBMS 연동 프로젝트 -->
		<!-- spring jdbc -->
		<!--  스프링 프로젝트에서 DBMS와 연결하는 첫번째 파이프-->
		<!-- https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.3.20</version>
</dependency>


메이븐 Apache Commons DBCP
		<!-- common-dbcp2 -->
		<!-- db connection pool을 만들고 db와의 연결을 원활히 도와주는 도구 -->
		<!-- https://mvnrepository.com/artifact/org.apache.commons/commons-dbcp2 -->
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-dbcp2</artifactId>
    <version>2.9.0</version>
</dependency>


		<!-- mybatis -->
		<!-- mybatis-spring -->
		<!-- db와 연결할때 SQL을 변환하여 DB로 보내는 데이터로 자동 변환 -->
		<!-- Dao 클래스를 자동으로 생성해주는 도구 -->
		<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis</artifactId>
			<version>3.5.10</version>
		</dependency>
		<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis-spring -->
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis-spring</artifactId>
			<version>2.0.7</version>
		</dependency>

		<!-- ojdbc -->
		<!-- oracle과 연결할때 Project에서 DBMS로 연결하는 도구 -->
		<!-- https://mvnrepository.com/artifact/com.oracle.database.jdbc/ojdbc8 -->
		<dependency>
			<groupId>com.oracle.database.jdbc</groupId>
			<artifactId>ojdbc8</artifactId>
			<version>18.15.0.0</version>
		</dependency>


## appServlet 에서 mybatis-context
finish 누르지 말고 next 눌러서 선택
-beans   -context -mybatis-spring  -tx 선택하고 제일 마지막꺼 다 선택 후 finish



## mybatis-context.xml 안에 설정
<!-- mybatis 3.x의 장점-->
	<!-- 
		context 설정만 잘 해 두면 DBMS 연결하여 사용하는데
		작성하는 코드가 매우 간소해진다.
		-->	
		
	<!-- dataSource -->
	<!-- DBMS를 연결하는데 필요한 기본정보 설정-->
	<!--  BasicDataSource ds = new BasicDataSource()와 같은말-->
	<!-- property : setter method -->
	<bean id="ds" class="org.apache.commons.dbcp2.BasicDataSource">
		<property name="driverClassName" value="oracle.jdbc.driver.OracleDriver"/>
		<property name="url" value="jdbc:oracle:thin:@localhost:1520:xe"/>
		<property name="username" value="user3"/>
		<property name="password" value="12341234"/>

	</bean>
	<!-- SqlSessionFactory -->
	<!-- Dao, Mapper 등을 사용하여 DB 연결하는 bean 생산기지 설정 -->
	<bean id="sqlSession" class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="ds"/>	
		<property name="typeAliasesPackage" value="com.callor.school.model"/>	
	</bean>
	<!-- SqlSessionTemplate -->
	<!-- SqlSessionFatory를 도와주는 도구 -->
	<bean class="org.mybatis.spring.SqlSessionTemplate">
		<constructor-arg ref="sqlSession"/>	
	</bean>
	
	<!--  
		Dao interface 가 저장된 package를 scan 하여
		SqlSessionFatory에게 알려주는 역할 수행
	-->
	<mybatis-spring:scan base-package="com.callor.school.pesistance"/>




## web.xml 안에 수정

<param-value>/WEB-INF/spring/appServlet/servlet-context.xml</param-value> 을 아래처럼 수정

appServlet/*-context.xml(안에 있는 context 파일을 모두 읽어라.)
<param-value>/WEB-INF/spring/appServlet/*-context.xml</param-value>





## spring 폴더 아래 mapper 폴더를 만든 후student-mapper.xml 생성


```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  

<!--  
	*mapper.xml 파일
	sql문을 xml 문 받식으로 작성하는 파일
	
	SQL문을 dao 클래스에 문자열로 작성을 하는데 이 방식을 mybatis 3.5x 이상에서만 사용할 수 있다.
	또한 문자열로 작성하는 SQL 문은 상대적으로 유락 발생할 확률이 높다.
	다양한 SQL문을 구현하기가 다소 불편하다
	
	xml 방식으로 SQL을 작성하는 것은
	전통적으로 mybatis에서 많이 사용하고 다양한 SQL 구현이 유리하다
	
	mybatis Dao interface와 mapper.xml 파일을 조합하여 내부에서 실제 클래스를 구현한다.
 -->
 <mapper namespace="com.callor.school.pesistance.StudentrDao">
 
 	<select id="selectAll">
 		SELECT * FROM tbl_student
 	</select>
 	
 	<select id="findById">
 		SELECT * FROM tbl_student WHERE st_num = #{st_num}
 	</select>
 
 	<insert id="insert">
 			INSERT INTO tbl_student
 			(
 				st_num,
 				st_name,
 				st_grade,
 				st_addr,
 				st_tel
 			)VALUES(
 				#{st_num},
 				#{st_name},
 				#{st_grade},
 				#{st_addr},
 				#{st_tel}
 				)
 	</insert>
 	
 	<update id="update">
 		UPDATE tbl_student SET
 				st_name =#{st_name},
 				st_grade = #{st_grade},
 				st_addr = #{st_addr},
 				st_tel = #{st_tel}
 				
 		WHERE st_num = #{st_num}
 	</update>
 	
 	<delete id="delete">
 		DELETE FROm tbl_student WHERE st_num = #{st_num}
 	</delete>
 </mapper>
 ```
## StudentDao 생성
* mapper랑 Dao랑 알아서 지지고 볶아서 sql언어로 짜잔 하고 연동해준다.



## favicon icon 설정
* static 폴더에 images 폴더 생성후 다운받은 파피콘 넣고
* servlet-context.xml에 아래 넣기
<resources mapping="/favicon.ico/**" location="/static/images/favicon.ico" />
* 위에만 해도 안되면 home.jsp head 부분에 아래 넣기
<link rel="icon" href="${rootPath}/static/images/favicon.ico" type="image/x-icon"/>


## 한글 엔코딩
* web.xml에 넣기
<filter>
		<filter-name>encKor</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>UTF-8</param-value>
		</init-param>
		<init-param>
			<param-name>forceEncoding</param-name>
			<param-value>true</param-value>
		</init-param>
	</filter>
	
	<filter-mapping>
		<filter-name>encKor</filter-name>
		<url-pattern>*</url-pattern>
	</filter-mapping>



## jackson databine
* pom.xml 안에 넣기
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
		<dependency>
			<groupId>com.fasterxml.jackson.core</groupId>
			<artifactId>jackson-databind</artifactId>
			<version>2.12.1</version>
		</dependency>



##  @ResponseBody
* Controller method에서 무자열을 return하면
* 문자열을 web clint에게 그래도 전송하는 역할을 하도록 지시하는 Annotation이다


## Spring sequrity 
<!-- https://mvnrepository.com/artifact/org.springframework.security/spring-security-core -->
		<dependency>
			<groupId>org.springframework.security</groupId>
			<artifactId>spring-security-core</artifactId>
			<version>5.3.13.RELEASE</version>
		</dependency>

* 회원가입을 하면 password를 데이터베이스에 문자열 그대로 저장을 한다
* 그래서 해킹을 당하면 비밀번호 노출의 위험이 있어서 
* Maven Dependensies - sequrity.crypto.bcrypto를 사용하여 암호화를 할것이다.

* 회원가입을 했을때 비밀번호를 암호화를 시키고 DB에 저장,
* DB에서 꺼내올때 다시 복호화를 한 후 가져오기

* 방법1. = 쌍방향 암호화: 평문+key를 붙여 암호화를 시킨후 다시 +key를 제거해 원문으로 복호화
* 방법2. = 단방향 암호화: 평문+key를 붙여 암호화를 시킨후 DB저장, 
* 로그인 할때 암호를 입력하면 암호화를 시켜 DB에 저장된 내용과 비교 (안전성이 더 높음)

* 평문 문자를 BCrypt를 사용하여 암호화를 할때 key 값에 따라 60자리의 문자열로 바뀐다.

# security-context.xml

	<bean id="passwordEncoder" class="org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder">
		<constructor-arg index="0" value="4"/>
	</bean>
* <constructor-arg index="0" value="4"/> 
value 값은 암호화를 몇번 할것인가 커지켠 커질수록 강력한 암호, 대신 시간이 오래걸림



# Jackson Dataformat XML


```
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.dataformat/jackson-dataformat-xml -->
<dependency>
    <groupId>com.fasterxml.jackson.dataformat</groupId>
    <artifactId>jackson-dataformat-xml</artifactId>
    <version>2.13.3</version>
</dependency>
```

* jackson-dataformat-xml와 jackson-databind는 버전이 같아야함