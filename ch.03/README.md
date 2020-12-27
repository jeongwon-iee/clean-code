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
Ex. `isPayday(Employee e, Date date)`, `deliverPay(Employee e, Money pay)`

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

### 📘 서술적인 이름을 사용하라!

> "코드를 읽으면서 짐작했던 기능을 각 루틴이 그대로 수행한다면 깨끗한 코드라 불러도 되겠다."


