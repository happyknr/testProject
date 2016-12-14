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


&lt;code&gt;

**&lt;bean id**="authFailLogger" **class**="net.madvirus.spring4.chap02.AuthFailLogger"&gt;

**&lt;/bean&gt;**

&lt;/code&gt;

* id를 설정하지 않을 경우에는 net.madvirus.spring4.chap02.AuthFailLogger**\#0**과 같이 **'\#' 뒤에 숫자**를 부여하여 생성됨

#### &lt;constructor-arg&gt; 태그 : 생성자 방식 설정

&lt;code&gt;

&lt;bean id="user1" class="net.madvirus.spring4.chap02.User"&gt;

&lt;**constructor-arg value="knr"** /&gt;

&lt;**constructor-arg value="1234"** /&gt;

&lt;/code&gt;

* 객체를 생성할 때 생성자에 전달할 파라미터 값을 설정해주어야 할 경우 &lt;constructor-arg&gt; 태그 사용
* 파라미터 개수만큼 태그를 지정해주어야 함
* 위 코드는 new User\("knr", "1234"\)와 같음



