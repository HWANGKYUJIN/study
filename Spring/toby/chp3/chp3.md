# 템플릿

## 목차

1. 템플릿이란 무엇인가?
1. 템플릿의 활용
	1. 템플릿 메소드 패턴
	1. 전략 패턴
	1. 템플릿/콜백 패턴
1. 스프링에 적용된 템플릿
	1. JdbcTemplate

---

### 템플릿이란 무엇인가?
 우리는 개방 폐쇄 원칙(OCP)에 따라 변화의 특성이 다른 부분을 구분하고, 각각 다른 목적과 다른 이유에 의해 다른 시점에 독립적으로 변경될 수 있는 효율적인 구조를 만들어야한다.

 코드에서 어떤 부분은 변경을 통해 그 기능이 다양해지고 확장하려는 성질이 있고, 어떤 부분은 고정되어 있고 변하지 않으려는 성질이 있다. **템플릿이란 변하지 않으려는 성질이 있는 코드와 확장하려는 성질의 코드를 분리하여 효과적으로 활용할 수 있도록 하는 방법**이다.

---

### 템플릿의 활용
#### 템플릿 메소드 패턴

 **변하지 않는 부분을 상위 클래스의 템플릿 메소드로, 변하는 부분을 하위 클래스의 훅 메소드로 구현**한다. **상속을 통해 변하는 부분을 자유롭게 확장**할 수 있는 구조를 가질 수 있다. 그러나 확장 구조에 상속을 강제하여 **다른 상속을 활용할 수 없으며, 클래스 설계 시점에 확장 구조가 고정**되어 관계의 유연성이 떨어진다는 단점이 있다.

#### 전략 패턴

 **변하지 않는 부분을 컨텍스트로, 변하는 부분을 전략이 추상화된 인터페이스를 구현한 전략 클래스로 구현**한다. 또한, **클라이언트에서 알맞는 전략을 선택하여 컨텍스트에 주입**한다. 전략 인터페이스를 구현하여 확장할 수 있는 구조를 가질 수 있다. 

#### 템플릿/콜백 패턴
 전략 패턴에 익명 내부 클래스를 활용한 패턴이다. **변하지 않는 부분을 템플릿(컨텍스트)로 , 변하는 부분은 클라이언트에서 콜백(전략 객체)을 내부 익명 클래스로 구현**한다. 콜백 객체가 내부 익명 클래스로 구현되어 클라이언트의 정보를 직접 참조하는 특성을 갖는다.

---

### 스프링에 적용된 템플릿
#### JdbcTemplate
스프링이 제공하는 JdbcTemplate 클래스는 템플릿/콜백 패턴을 적극 활용한다.

JdbcTemplate 클래스에는 **몇 가지 핵심 콜백**이 존재한다. 그리고 이러한 콜백과 내장 콜백을 이용하여 **템플릿인 execute(...) 메소드를 통해 실질적인 작업**을 수행한다.

* public <T> T **execute**(PreparedStatementCreator psc, PreparedStatementCallback<T> action)
  * JdbcTemplate 객체의 DataSource로부터 Connection을 생성한다.
  * **psc에 Connection을 주입하여 PreparedStatement를 생성**한다.
  * **action에 PreparedStatement를 주입하여 결과(<T>)를 얻고 반환**한다.
* public interface **PreparedStatementCreator**
  * PreparedStatement createPreparedStatement(Connection con)
    * Connection으로부터 PreparedStatement를 생성
    * PreparedStatement의 close 및 SQLException 핸들링은 JdbcTemplate class가 담당
* public interface **PreparedStatementCallback**<T>
  * public T doInPreparedStatement(PreparedStatement ps)
  * PreparedStatement에 대한 실행과 결과 반환
* public interface **ResultSetExtractor**<T>
  * T extractData(ResultSet rs)
    * ResultSet에서 결과를 추출
    * ResultSet의 close 및 SQLException 핸들링은 JdbcTemplate class가 담당

> 참고 : JdbcTemplate의 queryForInt() 메소드는 스프링 4.2(?)부터 사라졌다. queryForObject()를 사용자가 직접 이용해야한다.