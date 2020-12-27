# 🧷 3장 | 함수

### 📘 작게 만들어라
> "함수를 만드는 첫째 규칙은 '작게!'다. 함수를 만드는 둘째 규칙은 '더 작게!'다." 

각 함수가 너무나도 명백하게 이야기 하나를 표현하고, 다음 무대를 준비 해야 한다.

#### 블록과 들여쓰기

- **if문, else문, while문 등에 들어가는 블록은 한 줄**이어야 한다. ⇒ *여기서 함수 호출*
- 중첩 구조가 생길만큼 함수가 커져서는 안 된다.
- 함수에서 들여쓰는 수준은 1단이나 2단을 넘어서면 안 된다.

: 바깥을 감싸는 함수가 작아질 뿐 아니라, 블록 안에서 호출하는 함수 이름이 적절하면, 가독성도 좋아진다.

##

### 📘 한 가지만 해라
> "함수는 한 가지를 해야 한다. 그 한 가지를 잘 해야 한다. 그 한 가지만을 해야 한다." 

- 함수는 추상화 수준이 하나인 단계만 수행해야 한다.
- 의미 있는 이름으로 다른 함수를 추출할 수 있다면 그 함수는 여러 작업을 하는 셈이다.

```java
public static String renderPageWithSetupsAndTeardowns(
	PageData pageData, boolean isSute) throws Exception {
	if(isTestPage(pageData)
		includeSetupAndTeardownPages(pageData, isSuite);
	return pageData.getHtml();
}
```

: 한 가지 작업만 하는 함수는 자연스럽게 섹션으로 나누기 어렵다.

##

### 📘 함수 당 추상화 수준은 하나로!

> "함수가 확실히 '한 가지' 작업만 하려면 함수 내 모든 문장의 추상화 수준이 동일 해야 한다."

- **위에서 아래로 코드 읽기: *내려가기* 규칙**

: 한 함수 다음에는 추상화 수준이 한 단계 낮은 함수가 온다. 위에서 아래로 프로그램을 읽으면 함수 추상화 수준이 한 번에 한 단계씩 낮아진다.

- **Switch문**

: 본질적으로 switch문은 N가지를 처리하지만, 각 switch문을 저차원 클래스에 숨기고 절대로 반복하지 않는 방법이 있다. 다형성(polymorphism)을 이용한 방법이다.

```java
public Money calculatePay(Employee e) thorws InvalidEmployeeType {
	switch(e.type) {
		case COMMISIONED:
			return calculateCommissionedPay(e);
		case HOURLY:
			return calculateHourlyPay(e);
		case SALARIED:
			return calculateSalariedPay(e);
		default:
			throw new InvalidEmployeeType(e.type);
	}
}
```

위 함수의 문제점

1. 함수가 너무 길다. (새 직원 유형을 추가하면 더 길어진다.)
2. '한 가지' 작업만 수행하지 않는다.
3. SRP(Single Responsibility Principle)을 위반한다.
4. OCP(Open Closed Principle)을 위반한다. (새 직원 유형을 추가할 때마다 코드를 변경 해야 한다.)
5. *위 함수와 구조가 동일한 함수가 무한정 존재한다.*
ex) `isPayday(Employee e, Date date)`, `deliverPay(Employee e, Money pay)`

*해결 - 추상 팩터리에 switch문 꽁꽁 숨기기*

추상 팩토리에 switch문을 숨기고, 팩토리는 switch 문을 사용해 적절한 Employee 파생 클래스의 인스턴스를 생성한다. `calculatePay`, `isPayday`, `deliverPay`등과 같은 함수는 Employee 인터페이스를 거쳐 호출 된다. 그러면 다형성으로 인해 실제 파생 클래스의 함수가 실행 된다.

```java
public abstract class Employee {
	public abstract boolean isPayday();
	public abstract Money calculatePay();
	public abstract void deliverPay(Money pay);
}
-----
public interface EmployeeFactory {
	public Employee make Employee(EmployeeRecord r) throws InvalidEmployeeType;
}
-----
public class EmployeeFactoryImpl implements EmployeeFactory {
	public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType {
		switch(r.type) {
			case COMMISIONED:
				return new CommissionedEmployee(r);
			case HOURLY:
				return new HourlyEmployee(r);
			case SALARIED:
				return new SalariedEmployee(r);
			default:
				throw new InvalidEmployeeType(r.type); 
		}
	}
}
```

##

### 📘 서술적인 이름을 사용하라!

> "코드를 읽으면서 짐작했던 기능을 각 루틴이 그대로 수행한다면 깨끗한 코드라 불러도 되겠다."

**한 가지만 하는 작은 함수에 이런저런 이름을 시도한 후 최대한 서술적인 이름을 골라라.**

- 모듈 내에서 함수 이름은 같은 문구, 명사, 동사를 사용한다.

ex) `includeSetupAndTeardownPages`, `includeSetupPages`, `includeTeardownPages`

##

### 📘 함수 인수

> "함수에서 이상적인 인수 개수는 0개다. 다음은 1개고, 다음은 2개다. 3개는 가능한 피하는 편이 좋다. 4개 이상은 특별한 이유가 필요하다."

- 함수 이름과 인수 사이에 추상화 수준이 다르다.
- 읽는 사람은 현 시점에서 중요하지 않은 세부사항까지 알아야 한다.
- 인수 3개가 넘어가면 인수마다 유효한 값으로 모든 조합을 구성해 테스트하기가 부담스러워 진다.

최선은 인수가 없는 경우이며, 차선은 입력 인수가 1개뿐인 경우다.

**단항 형식**

함수에 인수 1개를 넘기는 이유로 가장 흔한 경우는 두 가지이다.

1. 인수에게 질문을 던지는 경우  
`boolean fileExists("MyFile")`
2. 인수를 뭔가로 변환해 결과를 반환하는 경우  
`InputStream fileOpen("MyFile")`
3. 이벤트 (입력 인수로 시스템 상태를 바꾸는 경우)  
`passwordAttemptFailedNtimes(int attempts)`

위의 경우가 아니라면 단항 함수는 가급적 피한다.

ex) `void includeSetupPageInto(StringBuffer pateText)` (X) 변환 함수에서 출력 인수를 사용

**플래그 인수**

함수로 `boolean`값을 넘기는 것은 함수가 한꺼번에 여러 가지를 처리한다고 대놓고 공표하는 셈이다.

**이항 함수**

나쁜 예) `writeFile(outputStream, name)`

> 잠시 주춤하며 첫 인수를 무시해야 한다는 사실을 깨닫는다. 그리고 바로 그 사실이 결국 문제를 일으킨다.  
왜냐고? 어떤 코드든 절대로 무시하면 안 된다. 무시한 코드에 오류가 숨어드니까.

*해결* `writeField` 메서드를 outputStream 클래스 구성원으로 만들어 `ouputstream.writeFiled(name)`으로 호출한다. 아니면 outputStream을 현재 클래스 구성원 변수로 만들어 인수로 넘기지 않는다.

좋은 예) `Point p = new Point(0,0);`
인수 2개가 한 값을 표현하는 두 요소이고, 그 순서가 자연적인 경우 이항 인수는 적절하다.


