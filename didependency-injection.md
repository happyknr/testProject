# DI\(Dependency Injection\)

설명

### 의존 객체 직접 제어의 단점

설명

### DI 사용 방식

설명

##### 1. 생성자 방식

* 장점 : 객체를 생성하는 시점에 의존하는 객체를 모두 전달받을 수 있음
* 단점 : 생성자에 전달되는 파라미터의 이름만으로는 실제 타입을 알아내기 힘듦, 생성자에 전달되는 파라미터의 개수가 증가할수록 코드 가독성이 떨어짐

##### 2. 프로퍼티 설정 방식

* 장점 : 어떤 의존 객체를 설정하는지 메서드 이름으로 알 수 있음
* 단점 : 객체를 생성한 뒤에 의존 객체가 모두 설정되었다고 보장할 수 없어서 사용 가능하지 않은 상태일 수 있음

### 스프링

* 객체를 생성하고 연결해주는 ID 컨테이너
* 객체 컨테이너\(object container\)
* **스프링 빈\(Spring bean\) 객체** : 스프링 컨테이너가 생성해서 보관하는 객체
* 스프링 설정으로부터 스프링 컨테이너를 만들고, 컨테이너는 설정에서 명시한 스프링 빈 객체를 생성해서 지정한 이름으로 보관

##### 스프링 컨테이너 종류

* BeanFactory : 단순히 컨테이너에서 객체를 생성하고 DI를 처리해주는 기능만 제공
* ApplicationContext : 다양한 부가 기능 제공\(어노테이션을 사용한 빈 설정, 스프링을 이용한 웹 개발 등등\)

  * GenericXmlApplicationContext

  * AnnotationConfigApplicationContext

  * GenericGroovyApplicationContext

  * XmlWebApplicationContext

  * AnnotationConfigWebApplicationContext



### XML을 이용한 DI 설정

#### &lt;bean&gt; 태그 : 생성할 객체 지정

* 스프링 컨테이너가 생성할 객체에 대한 정보를 지정할 때 사용

* 주요 속성 : id, class


```
<bean id="authFailLogger" class="net.madvirus.spring4.chap02.AuthFailLogger">
</bean>
```

* id를 설정하지 않을 경우에는 net.madvirus.spring4.chap02.AuthFailLogger**\#0**과 같이 **'\#' 뒤에 숫자**를 부여하여 생성됨

#### &lt;constructor-arg&gt; 태그 : 생성자 방식 설정

```
public class User {
    ...
    public User(String id, String password) {
        this.id = id;
        this.password = password;
    }
    ...
}
```

```
<bean id="user1" class="net.madvirus.spring4.chap02.User">
    <constructor-arg value="knr"/>
    <constructor-arg value="1234"/>
</bean
```

* 객체를 생성할 때 생성자에 전달할 파라미터 값을 설정해주어야 할 경우 &lt;constructor-arg&gt; 태그 사용
* 파라미터 개수만큼 태그를 지정해주어야 함
* 위 코드는 new User\("knr", "1234"\)와 같음
* &lt;value&gt; 태그도 사용 가능 ex\) &lt;constructor-arg&gt;**&lt;value&gt;**knr**&lt;/value&gt;**&lt;/constructor-arg&gt;

* 다른 빈\(bean\) 객체를 사용해야 하는 경우에는


```
<bean id="userRepository" class="net.madvirus.spring4.chap02.UserRepository">
</bean>

<bean id="pwChangeSvc" class="net.madvirus.spring4.chap02.PasswordChangeService">
    <constructor-arg ref="userRepository"/>
    <!-- <constructor-arg><ref bean="userRepository"/></constructor-arg> 도 가능함 -->
</bean>
```

#### &lt;property&gt; 태그 : 프로퍼티 방식 설정

```
<bean id="authFailLogger" class="net.madvirus.spring4.chap02.AuthFailLogger">
    <property name="threshold" value="5"/> <!-- sertThreshold(5) -->
</bean>

<bean id="authService" class="net.madvirus.spring4.chap02.AuthenticationService">
    <property name="failLogger" ref="authfailLogger" /> <!-- setFailLogger(authFailLogger) -->
    <property name="userRepository">
        <ref bean="userRepository" />
    </property>
</bean>
```

#### GenericXmlApplicationContext : 설정 파일 지정

```
GenericXmlApplicationContext ctx = new GenericXmlApplicationContext("classpth:/spring-member.xml", 
                                "spring:/spring-board.xml", "spring:/datasource.xml"); //여러개 지정 가능

//클래스패스 루트가 아닌 다른 곳에 위치할 경우에는 루트 기준으로 경로 형식 입력
ctx = new GenericXmlApplicationContext("classpth:/conf/spring/conf.xml");

//파일 시스템에서 설정 파일 읽어올 경우 : file:접두어
ctx = new GenericXmlApplicationContext("file:src/main/resources/conf.xml");
ctx = new GenericXmlApplicationContext("file:/conf/local/conf2.xml"); //상대 경로

//특정 경로에 있는 모든 xml 파일
ctx = new GenericXmlApplicationContext("classpath:/conf/spring-*.xml");
```

#### List, Map, Set 타입의 콜렌션 설정

* List : &lt;list&gt;

```
...
<list>
    <!-- 객체 형태로 전달시 -->
    <ref bean="user1"/>
    <ref bean="user2"/>
    <!-- Integer, String 등의 타입 -->
    <value>10.0.1</value>
    <value>12.5.1</value>
</list>
...


<!-- 위 코드를 자바 코드로 실행시 -->
List<User> refs = new List<>();
refs.add(user1);
refs.add(user2);
userRepository.setUsers(refs);
```

* Map : &lt;map&gt;
* &lt;map&gt;태그는 &lt;entry&gt; 태그를 이용해 \(키,값\) 쌍 목록을 전달함

```
...
<map>
    <entry>
        <key>
            <value>frontDoor</value>
        </key>
        <ref bean="sensor1" />
    </entry>
    <entry key="backDoor" value-ref="sensor2"/>
</map>
...
```

* Set : &lt;set&gt;

```
...
<set>
    <value>200</value>
    <value>300</value>
</set>
...
```

#### Properties 타입 값 설정

```
<bean id="sensor1" class="net.madvirus.spring4.chap02.sensor.Sensor">
    <property name="additionalInfo"> <!-- 형식1 -->
        <props>
            <prop key="threshold">1500</prop>
            <prop key="retry">3</prop>
        </props>
    </property>
    <property name="additionalInfo2"> <!-- 형식2 -->
        <value>
            threshold=3000
            retry=5
        </value>
    </property>
</bean>


<!-- 자바 형식 -->
Properties prop = new Properties();
prop.setProperty("threshold", "1500");
prop.setProperty("retry", "3");
sensor1.setAdditionalInfo(prop);
```

#### c 네임스페이스와 p 네임스페이스를 생성자 방식/프로퍼티 방식 설정

* &lt;property&gt; 태그의 &lt;constructor-arg&gt; 태그를 사용하면, 작성해야 할 XML 문서 내용이 증가함. 이를 좀 더 짧게 설정할 때 사용.

```
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:c="http://www.springframework.org/schema/c" <!-- c 네임스페이스 -->
    xmlns:p="http://www.springframework.org/schema/p" <!-- p 네임스페이스 -->
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="user1" class="net.madvirus.spring4.chap02.User" c:id="knr" c:password="1234" />
    <bean id="user2" class="net.madvirus.spring4.chap02.User" c:_0="test" c:_1="qwer" />
    <bean id="pwChangeSvc" class="net.madvirus.spring4.chap02.PasswordChangeService" c:userRepository-ref="userRepository />
    <bean id="authFailLogger" class="net.madvirus.spring4.chap02.AuthFailLogger" p:threshold="2" />
    <bean id="authenticationService" class="net.madvirus.spring4.chap02.AuthenticationService" 
                        p:failLogger-ref="authFailLogger" p:userRepository-ref="userRepository" />
    <bean id="userRepository" class="net.madvirus.spring4.chap02.UserRepository">
     ...
    </bean>
</beans>
```

* c 네임스페이스

  * 기본 데이터 타입 : "c:파라미터이름"

  * 빈\(bean\) 객체 : "c:파라미터이름-ref"

  * 인덱스 순서로 전달 : "c:\_인덱스" 또는 "c:\_인덱스-ref"



#### &lt;import&gt; 태그를 이용한 설정 파일 조합

```
<import resource="classpath:/domain/item/*.xml"/>
<import resource="classpath:/domain/order/*.xml"/>
```

* property 파일이 각기다른 경로에 존재할 경우 사용

### 자바 코드를 이용한 DI 설정

#### @Configuration과 @Bean을 이용한 빈 객체 설정

```
/* 설정 */
@Configuration //클래스를 스프링 설정으로 사용함을 의미
public class Config {
    @Bean //메서드의 리턴 값을 빈 객체로 사용함을 의미
    public User user1() { //메서드의 이름을 빈 객체 식별자로 사용
        return new User("knr", "1234");
    }

    @Bean(name="user2") //메서드 이름 대신 다른 식별자 값을 주고 싶을 때 사용
    public User user() {
        return new User("test", "1234");
    }
}


/* 사용 */
AnnotationConfigapplicationContext ctx = new AnnotationConfigApplicationContext(Config.class);
User user1 = ctx.getBean("user1", User.class);
```

#### 의존 설정하기

* 생성자나 프로퍼티에 값을 설정할 때에는 직접 설정

```
@Bean
public User user {
    return new User("knr", "1234"); //생성자에 값 직접 전달
}

@Bean
public AuthFailLogger authFailLogger() {
    AuthFailLogger logger = new AuthFailLogger();
    logger.setThreshold(2); //프로퍼티에 값 직접 전달
    return logger;
}
```

* ★메소드를 호출해서 의존을 설정할 때 새로운 객체가 생성되지 않음!!★
* 이유 : 스프링이 설정 클래스를 상속받아 새로운 클래스를 만들어내기 때문!

```
@Bean
public PasswordChangeService pwChangeSvc() {
    return new PasswordChangeService(userRepository()); //여기서 생성된 객체와
}

@Bean
public AuthenticationService authenticationService() {
    AuthenticationService authSvc = new AuthenticationService();
    authSvc.setFailLogger(authFailLogger());
    authSvc.setUserRepository(userRepository()); //여기서 생성된 객체는 일치함!!
    return authSvc;
}


//이유 : 아래와 같이 userRepository()메서드는 상위 클래스에 정의된 userRepository() 메서드를 재정의하고 있는데, 재정의한
     메서드는 userRepository 필드가 null이면 상위 클래스의 userRepository() 메서드를 호출해서 userRepository 필드에 
     할당하고 userRepository 필드를 리턴한다. 
     따라서 메서드를 여러번 호출해도 userRepository 필드에 할당된 동일한 객체를 리턴한다.
public class SpringGenConfig extends Config {
    private UserRepository userRepository;

    @Override
    public UserRepository userRepository() {
        if(userRepository == null) {
            userRepository = super.userRepository();
        }
        return userRepository;
    }
}
```

* Config 클래스를 스프링 설정으로 사용하려면, AnnotationConfigApplicationContext 클래스를 사용

```
AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(Config.class);

//두개 이상의 설정 정보도 가능
AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(Config.class, configSensor.class);

//특정 패키지 내에 존재하는 설정 정보들 모두 사용할 경우 : "패키지.conf"
//패키지 경로를 전달하면, 해당 패키지 및 그 하위 패키지에 위치한 @Configuration 어노테이션이 적용된 모든 클래스를 설정함
AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext("net.madvirus.spring4.chap02.conf");
```

* @Import 어노테이션을 이용한 조합

```
@Configuration
@Import(ConfigSensor.class) //하나
@Import({ConfigSensor.class, ConfigDashboard.class}) //두개 이상
public class Config {
...
}
```

##### XML설정 vs 자바 설정

* XML 설정 방식

  * 설정 정보 변경 시 XML만 변경하면 됨

  * 많은 프레임워크/라이브러리가 XML 스키마를 이용한 설정의 편리함을 지원함

  * IDE의 코드 자동 완성 기능이 빈약하면 XML 작성 과정이 다소 번거로움

  * 코드를 실행해야 설정 정보 오류를 확인할 수 있음



* 자바 설정 방식

  * 컴파일러의 도움을 받기 때문에, 오타 등의 설정 정보 오류를 미리 알 수 있음

  * 자바 코드이기 때문에 IDE가 제공하는 코드 자동 완성 기능의 도움을 받을 수 있음

  * 설정 정보를 변경하려면 자바 코드를 재컴파일해야 함

  * XML 스키마 기반의 설정을 지원하는 프레임워크/라이브러리 중 아직 자바 기반의 편리한 설정을 지원하지 않는 경우가 있음



### XML 설정과 자바 코드 설정 함께 사용하기

#### XML 설정에서 자바 코드 설정 조합하기

* &lt;context:annotation-config /&gt; 설정 추가
* @Configuration 어노테이션이 적용된 클래스를 &lt;bean&gt; 태그를 이용해서 스프링 빈으로 등록함

```
// 클래스 파일
@Configuration
public class ConfigSensor {
    @Bean
    public Sensor sensor1() {
        ...
    }
}
...

<!-- xml 파일 설정 -->
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans   
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">

<!-- @Configuration이 적용된 빈을 스프링 설정으로 사용하기 위함 -->
<context:annotation-config /> <!-- 어노테이션을 인식할 수 있도록 만들어주는 태그 -->

<!-- @Configuration 적용 클래스를 빈으로 등록 -->
<bean class="net.madvirus.spring4.chap02.conf.ConfigSensor" />
...
```

#### 자바 코드 설정에서 XML 설정 조합하기

* @ImportantResource 어노테이션 사용

```
@Configuration
@ImportantResource("classpath:config-sensor.xml")
public class ConfigWithXmlImport {
    @Bean
    public User user1() {
        return new User("knr", "1234");
    }
    ...
}
```

### 팩토리 방식의 스프링 빈 설정

* 객체 생성에 사용되는 static 메서드 지정
* FactoryBean 인터페이스를 이용한 객체 생성 처리

#### 객체 생성을 위한 정적 메서드 설정

```
<bean id="factory" class="net.madvirus.spring4.chap02.erp.ErpClientFactory" factory-method="instance">
```

* static 메서드를 이용해서 객체를 생성해야 할 경우 &lt;bean&gt; 태그에 factory-method 속성을 설정해야 함

```
public abstract class ErpClientFactory {
    public static ErpClientFactory instance(Properties props) {
        return new DefaltErpClientFactory(props);
    }
}


<bean id="factory" class="net.madvirus.spring4.chap02.erp.ErpClientFactory" factory-method="instance">
    <constructor-arg> <!-- static 메서드의 파라미터로 전달 -->
        <props>
            <prop key="server">10.50.0.101</prop>
        </props>
    </constructor-arg>
</bean>
```

#### FactoryBean 인터페이스를 이용한 객체 생성 처리

* 스프링 빈으로 정의하고 싶은 타입이 XML 설정으로 빈을 정의하기에는 다소 복잡한 경우 FactoryBean 인터페이스 사용

```
public interface FactoryBean<T> {
    T getObject() throws Exception; //실제 스프링 빈으로 사용될 객체를 리턴
    Class<?> getObjectType(); //스프링 빈으로 사용될 객체의 타입을 리턴
    boolean isSingleton(); //getObject() 메서드가 매번 동일한 객체를 리턴하면 true, 매번 새로운 객체를 리턴하면 false를 리턴
}
```

~~~~~~~~~~~~~~~~~~~~ 추가 내용 더 있음. 이해가 안돼서 정리 못함.

### 어노테이션을 이용한 객체간 의존 자동 연결



