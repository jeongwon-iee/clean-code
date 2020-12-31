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

