# 🧷 8장 | 경계

시스템에 들어가는 모든 소프트웨어를 직접 개발하는 경우는 드물다. 때로는 패키지를 사고, 때로는 오픈 소스를 이용한다. 때로는 사내 다른 팀이 제공하는 컴포넌트를  사용한다. 어떤 식으로든 이 외부 코드를 우리 코드에 깔끔하게 통합해야만 한다.

이 장에서는 *소프트웨어 경계를 깔끔하게 처리하는 기법과 기교를 살펴본다.*

##

### 📘 외부 코드 사용하기

**`java.util.Map`**

- Generics를 사용하여 코드 가독성을 높일 수 있다.
- 하지만 인터페이스가 언제든 변할 수 있기 때문에 Generics에 의존하는 것도 위험하다.
- Map(인터페이스)를 사용할 클래스 안에서 관리하고 변환하면 설계 규칙과 비즈니스 규칙을 강제할 수 있다.

*before)*

Sensor라는 객체를 담는 Map을 만드려면 다음과 같이 Map을 생성한다.

```java
Map sensors = new HashMap();
```

Sensor 객체가 필요한 코드는 다음과 같이 Sensor 객체를 가져온다.

```java
Sensor s = (Sensor)sensors.get(sensorId);
```

Map이 반환하는 Object를 올바른 유형으로 변환할 책임은 클라이언트에 있다.

*after) 경계 인터페이스인 Map을 Sensors 안으로 숨긴다.*

```java
public class Sensors {
  private Map sensors = new HashMap();
  
  public Sensor getById(String id) {
    return (Sensor) sensors.get(id);
  }
  ...
}
```

Map 인터페이스가 변하더라도 나머지 프로그램에는 영향을 미치지 않는다.  
Sensors 클래스 안에서 객체 유형을 관리하고 변환하기 때문이다.

→ Map은 여기저기 넘기지 않는다.

> Map과 같은 경계 인터페이스를 이용할 때는 이를 이용하는 클래스나 클래스 계열 밖으로 노출되지 않도록 주의한다.  
공개 API의 인수로 넘기거나 반환값으로 사용하지 않는다.

##

### 📘 경계 살피고 익히기

