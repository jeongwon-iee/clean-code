# 🧷 9장 | 단위 테스트

### 📘 TDD 법칙 세 가지

1. 실패하는 단위 테스트를 작성할 때까지 실제 코드를 작성하지 않는다.
2. 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다.
3. 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.

##

### 📘 깨끗한 테스트 코드 유지하기

> 테스트 코드는 실제 코드 못지 않게 중요하다. 깨끗한 단위 테스트가 성공한다.

- '지저분해도 빨리' 작성한 테스트 코드는 결국 오히려 테스트를 안하느니만 못한 결과를 내놓는다.
- 테스트 코드는 실제 코드 못지 않게 중요하다. 실제 코드 못지 않게 깨끗하게 짜야 한다.
- 테스트는 유연성, 유지보수성, 재사용성을 제공한다.
- 테스트 케이스가 있으면 잠정적인 버그가 없을 것이라는 확신 하에 코드 변경이 쉬워진다.

##

### 📘 깨끗한 테스트 코드

> 가독성은 실제 코드보다 테스트 코드에 더더욱 중요하다.

**테스트 코드는 진짜 필요한 자료 유형과 함수만 사용한다.**

1. 테스트 자료 만들기
2. 테스트 자료 조작하기
3. 조작한 결과가 올바른지 확인하기

```java
public void testGetPageHierarchyAsWml() throws Exception {
	makePages("PageOne", "PageOne.ChildOne", "PageTwo");

	submitRequest("root", "type:pages");

	assertResponseIsXML();
	assertResponseContains(
		"<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>"
	);
}
```

*BUILD-OPERATE-CHECK* 패턴이 적합한 테스트 구조다.  
각 테스트는 명확히 세 부분으로 나눠진다.  
첫 부분은 테스트 자료를 만든다. 두 번째 부분은 테스트 자료를 조작하며, 세 번째 부분은 조작한 결과가 올바른지 확인한다.  
> *API 위에다 함수와 유틸리티를 구현* 한 후 그 함수와 유틸리티를 사용하므로 테스트 코드를 짜기도, 읽기도 쉽다.

- **이중 표준**

> 테스트 API에 적용하는 표준은 실제 코드에 적용하는 표준과 다르다.

단순하고, 간결하고, 표현력이 풍부해야 하지만, 실제 코드만큼 효율적일 필요는 없다. 

실제 환경과 테스트 환경은 요구사항이 다르기 때문이다.

```java
@Test
	public void turnOnCoolerAndBlowerIfTooHot() throws Exception {
		tooHot();
		assertEquals("hBChl", hw.getState();
	}

@Test
	public void turnOnHeaterAndBlowerIfTooCold() throws Exception {
		wayTooCold();
		assertEquals("HBchl", hw.getState();
	}

@Test
	public void turnOnHiTempAlarmAtThreshold() throws Exception {
		wayTooHot();
		assertEquals("hBCHl", hw.getState();
	}

@Test
	public void turnOnLoTempAlarmAtThreshold() throws Exception {
		wayTooCold();
		assertEquals("HBchL", hw.getState();
	}
```

이상한 문자열에서 대문자는 켜짐, 소문자는 꺼짐을 나타낸다.   
'그릇된 정보를 피하라' 규칙의 위반에 가깝지만, **의미만 안다면 결과를 재빨리 판단할 수 있다.**  
테스트 코드를 이해하기 너무도 쉽다는 사실이 드러난다.

```java
public String getState() {
	String state = "";
	state += heater ? "H" : "h";
	state += blower ? "B" : "b";
	state += cooler ? "C" : "c";
	state += hiTempAlarm ? "H" : "h";
	state += loTempAlarm ? "L" : "l";
	return state;
}
```

효율을 높이려면 StringBuffer가 더 적합하지만, *StringBuffer는 보기 흉하다.*

테스트 환경은 컴퓨터 자원과 메모리가 제한적일 가능성이 낮다.  
실제 환경에선 안 되지만 테스트 환경에선 문제 없는 방식이 있다. 대개 메모리나 CPU 효율과 관련 있는 경우다.  
코드의 깨끗함과는 철저히 무관하다.

##

### 📘 테스트 당 assert 하나

> assert 문이 단 하나인 함수는 결론이 하나라서 코드를 이해하기 쉽고 빠르다.

테스트를 여러 개로 쪼개 각자가 assert를 수행해도 좋다.

```java
public void testGetPageHierarchyAsWml() throws Exception {
	givenPages("PageOne", "PageOne.ChildOne", "PageTwo");

	whenRequestIsIssued("root", "type:pages");

	thenResponseShouldBeXML();
}
```  
위에선 함수 이름을 바꿔 *given-when-then* 관례를 사용했다. 그러면 테스트 코드를 읽기가 쉬워진다.  

##

### 📘 테스트 당 개념 하나

> 테스트 함수마다 한 개념만 테스트 하라

- 개념 당 assert문을 최소로 줄여라
- 테스트 함수 하나는 개념 하나만 테스트 하라

##

### 📘 F.I.R.S.T

깨끗한 테스트는 다음 다섯 가지 규칙을 따른다.

> *빠르게 Fast*

테스트는 빨라야 한다. 
테스트가 느리면 자주 돌릴 엄두를 못 내고, 자주 돌리지 않으면 초반에 문제를 찾아내 고치지 못한다.

> *독립적으로 Independent*

각 테스트는 서로 의존하면 안 된다.
한 테스트가 다음 테스트가 실행될 환경을 준비해서는 안 된다.
각 테스트는 독립적으로 그리고 어떤 순서로 실행해도 괜찮아야 한다.

> *반복 가능하게 Repeatable*

테스트는 어떤 환경에서도 반복 가능해야 한다.
테스트가 돌아가지 않는 환경이 하나라도 있다면 테스트가 실패한 이유를 둘러댈 변명이 생긴다.

> *자가검증하는 Self-Validating*

테스트는 *bool* 값으로 결과를 내야 한다. 성공 아니면 실패다.
통과 여부를 알려고 로그 파일을 읽거나 수작업으로 비교하게 만들어서도 안 된다.

> *적시에 Timely*

테스트는 적시에 작성해야 한다. 단위 테스트는 테스트 하려는 실제 코드를 구현하기 직전에 구현한다.
실제 코드를 구현한 다음에 테스트 코드를 만들면 실제 코드가 테스트 하기 어려워질지도 모른다.

