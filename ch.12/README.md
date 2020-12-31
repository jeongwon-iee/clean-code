# 🧷 12장 | 창발성

### 📘 창발적 설계로 깔끔한 코드를 구현하자

우수한 설계의 창발성을 촉진하는 **단순한 설계 규칙 네 가지**

1. 모든 테스트를 실행한다.
2. 중복을 없앤다.
3. 프로그래머 의도를 표현한다.
4. 클래스와 메서드 수를 최소로 줄인다.

위의 네 가지를 착실하게 따르기만 우수한 설계가 나오며, 코드 구조와 설계를 파악하기 쉬워진다.

##

### 📘 원칙 1. 모든 테스트를 실행하라

> 테스트 케이스를 만들고 계속 돌려라.  
코드를 정리하면서 시스템이 깨질까 걱정할 필요가 없다. 테스트 케이스가 있으니까!

**테스트가 가능한 시스템을 만들려고 하면 설계 품질이 더불어 높아진다.  
테스트 케이스를 많이 작성할수록 개발자는 DIP와 같은 원칙을 적용하고, 의존성 주입, 인터페이스, 추상화 등과 같은 도구를 사용해 결합도를 낮춘다.**  
→ 크기가 작고 목적 하나만 수행하는 클래스가 나온다.  
SRP(Single Responsibility Principle)를 준수하는 클래스는 테스트가 훨씬 더 쉽다.

- 시스템이 의도한 대로 작동하는지 검증이 필요하다.
- 테스트가 가능한 시스템을 만들려는 노력은 결국, **낮은 결합도**와 **높은 응집력**이라는 결과를 낳는다.  
→ *객체 지향 방법론이 지향하는 목표*
- 테스트 케이스를 작성함으로써 설계 품질을 자연스럽게 높일 수 있다.

테스트 케이스를 모두 작성했다면 이제 코드와 클래스를 정리해도 괜찮다.  
코드를 점진적으로 리팩터링 해나간다. 코드를 몇 줄 추가할 때마다 잠시 멈추고 설계를 조감한다.

- ***리팩터링***  
  - 응집도를 높인다.  
  - 결합도를 낮춘다.  
  - 관심사를 분리한다.  
  - 시스템 관심사를 모듈로 나눈다.  
  - 함수와 클래스 크기를 줄인다.  
  - 더 나은 이름을 선택한다.

##

### 📘 원칙 2. 중복을 없애라

> 중복은 추가 작업, 추가 위험, 불필요한 복잡도를 뜻한다.  
깔끔한 시스템을 만들려면 단 몇 줄이라도 중복을 제거하겠다는 의지가 필요하다.

_before)_
```java
public void scaleToOneDimension(
		float desiredDimension, float imageDimension) {
	if (Math.abs(desiredDimension - imageDimension) < errorThreshold)
		return;

	float scalingFactor = desiredDimension / imageDimension;
	scalingFactor = (float)(Math.floor(scalingFactor * 100) * 0.01f);
	
	RendoredOp newImage = ImageUtilities.getScaledImage(
			image, scalingFactor, scalingFactor);
	image.dispose();
	System.gc();
	image = newImage;
}

public synchronized void rotate(int degrees) {
	RenderedOp newImage = ImageUtilities.getRotatedImage(image, degrees);
	image.dispose();
	System.gc();
	image = newImage;
}
```

`scaleToOneDimension` 메서드와 `rotate` 메서드는 일부 코드가 동일하다.  
아래와 같이 코드를 정리해 중복을 제거한다.

_after)_
```java
public void scaleToOneDimension(
		float desiredDimension, float imageDimension) {
	if (Math.abs(desiredDimension - imageDimension) < errorThreshold)
		return;

	float scalingFactor = desiredDimension / imageDimension;
	scalingFactor = (float)(Math.floor(scalingFactor * 100) * 0.01f);
	
	RendoredOp newImage = ImageUtilities.getScaledImage(
			image, scalingFactor, scalingFactor);
	replaceImage(newImage);
}

public synchronized void rotate(int degrees) {
	RenderedOp newImage = ImageUtilities.getRotatedImage(image, degrees);
	replaceImage(newImage);
}

private void replaceImage(RenderedOp newImage) {
	image.dispose();
	System.gc();
	image = newImage;
}
```

아주 적은 양이지만 공통적인 코드를 새 메서드로 뽑고 보니 클래스가 SRP를 위반한다.  
그러므로 새로 만든 `replaceImage` 메서드를 다른 클래스로 옮겨도 좋겠다.
그러면 새 메서드의 가시성이 높아진다.

이런 '소규모 재사용'은 시스템 복잡도를 극적으로 줄여준다.
  
***TEMPLATE METHOD 패턴***

고차원 중복을 제거할 목적으로 자주 사용하는 기법

*before)*

```java
public class VacationPolicy {
	public void accrueUSDivisionVacation() {
		// 지금까지 근무한 시간을 바탕으로 휴가 일수를 계산하는 코드
		// ...
		// 휴가 일수가 미국 최소 법정 일수를 만족하는지 확인하는 코드
		// ...
		// 휴가 일수를 급여 대장에 적용하는 코드
		// ...
	}

	public void accrueEUDivisionVacation() {
		// 지금까지 근무한 시간을 바탕으로 휴가 일수를 계산하는 코드
		// ...
		// 휴가 일수가 유럽연합 최소 법정 일수를 만족하는지 확인하는 코드
		// ...
		// 휴가 일수를 급여 대장에 적용하는 코드
		// ...
	}
```

최소 법정 일수를 계산하는 코드만 제외하면 두 메서드는 거의 동일하다.  
여기에 *TEMPLATE METHOD* 패턴을 적용해 눈에 들어오는 중복을 제거한다.

*after)*

```java
abstract public class VacationPolicy {
	public void accrueVacation() {
		calculateBaseVacationHours();
		afterForLegalMinimums();
		applyToPayroll();
	}

	private void calculateBaseVacationHours() { /* ... */ };
	abstract protected void afterForLegalMinimums();
	private void applyToPayroll() { /* ... */ };
}

public class USVacationPolicy extends VacationPolicy {
	@Override protected void afterForLegalMinimums() {
		// 미국 최소 법정 일수를 사용한다.
	}
}

public class EUVacationPolicy extends VacationPolicy {
	@Override protected void afterForLegalMinimums() {
		// 유럽연합 최소 법정 일수를 사용한다.
	}
}
```

하위 클래스는 중복되지 않는 정보만 제공한다.

##

### 📘 원칙 3. 프로그래머 의도를 표현하라

> 코드는 개발자의 의도를 분명히 표현해야 한다.  
개발자가 코드를 명백하게 짤수록 다른 사람이 그 코드를 이해하기 쉬워진다.  
그래야 결함이 줄어들고 유지보수 비용이 적게 든다.

**자신이** 이해하는 코드는 짜기 쉽다. 하지만 나중에 코드를 유지보수할 사람이 코드를 짜는 사람만큼이나 문제를 깊이 이해할 가능성은 희박하다.

SW 프로젝트 비용 중 대다수는 장기적인 유지보수에 들어간다.  
코드를 변경하면서 버그의 싹을 심지 않으려면 유지보수 개발자가 시스템을 제대로 이해해야 한다.  
하지만 시스템이 점차 복잡해지며 유지보수 개발자가 시스템을 이해하느라 보내는 시간은 늘어난다.  

1. **좋은 이름을 선택한다.**
이름과 기능이 완전히 딴판인 클래스나 함수로 유지보수 담당자를 놀라게 해선 안 된다.
2. **함수와 클래스 크기를 가능한 줄인다.**
작은 클래스와 작은 함수는 이름짓기도 쉽고, 구현하기도 쉽고, 이해하기도 쉽다.
3. **표준 명칭을 사용한다.**
COMMAND나 VISITOR와 같은 디자인 패턴을 사용해 구현한다면 클래스 이름에 패턴 이름을 넣어준다.
4. **단위 테스트 케이스를 꼼꼼히 작성한다.**
테스트 케이스는 '예제로 보여주는 문서'다. 잘 만든 테스트 케이스를 읽어보면 클래스 기능이 한눈에 보인다.

##

### 📘 원칙 4. 클래스와 메서드 수를 최소로 줄여라

> 목표는 함수와 클래스 크기를 작게 유지하면서 동시에 시스템 크기도 작게 유지하는데 있다.  
하지만 테스트 케이스를 만들고 중복을 제거하고 의도를 표현하는 작업이 더 중요하다.

- 원칙과 정책을 너무 극단적으로 적용하는 것도 비효율적일 수 있으니 주의해야 한다.
- 수 자체에 매몰되는 것보다는, 실용적인 방식으로 가능한 선에서 규칙을 적용하는 것이 중요하다.

##

### 📘 결론

경험을 대신할 개발 기법은 없다. 표현력을 높이는 가장 중요한 방법은 노력이다.  
코드만 돌린 후 다음 문제로 직행하는 사례가 너무 흔하다.  
나중에 읽을 사람을 고려해 조금이라도 읽기 쉽게 만들려는 충분한 고민을 해야 한다.  
함수와 클래스에 조금 더 시간을 투자하자.  
더 나은 이름을 선택하고, 큰 함수를 작은 함수 여럿으로 나누고, 자신의 작품에 조금만 더 주의를 기울이자.

  
