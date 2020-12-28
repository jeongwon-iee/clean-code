# 🧷 6장 | 객체와 자료구조

### 📘 자료 추상화

**변수 사이에 함수라는 계층을 넣는다고 구현이 저절로 감춰지지는 않는다. 구현을 감추려면 추상화가 필요하다!**

변수를 private으로 선언하더라도 각 값마다 *get*함수와 *set*함수를 제공한다면 구현을 외부로 노출하는 셈이다.

조회 함수와 설정함수로 변수를 다룬다고 클래스가 되지는 않는다.

*추상 인터페이스를 제공해 사용자가 구현을 모른 채 자료의 핵심을 조작할 수 있어야 진정한 클래스다.*

- 구체적인 Point 클래스

```java
public class Point {
  private double x;
  private double y;
}
```

- 추상적인 Point 클래스 (더 좋음)

```java
public interface Point {
  double getX();
  double getY();
  void setCatesian(double x, double y);
  double getR();
  double getTheta();
  void setPolar(double r, double theta);
}
```

- 구체적인 Vehicle 클래스

```java
public interface Vehicle {
  double getFuelTankCapacityInGallons();
  double getGallonsOfGasoline();
}
```

- 추상적인 Vehicle 클래스 (더 좋음)

```java
public interface Vehicle {
  double getPercentFuelRemaining();
}
```

자료를 세세하게 공개하기보다는 추상적인 개념으로 표현해야 한다.  
인터페이스나 조회/설정 함수만으로는 추상화가 이뤄지지 않는다.

##

### 📘 자료/객체 비대칭

- 객체는 추상화 뒤로 자료를 숨긴 채 자료를 다루는 함수만 공개한다.
- 자료 구조는 자료를 그대로 공개하며 별다른 함수는 제공하지 않는다.

*객체 지향적인 도형 클래스*

```java
public class Square implements Shape {
	public Point topLeft;
	public double side;

	public double area() {
		return side*side;
	}
}

public class Rectangle implements Shape {
	public Point topLeft;
	public double height;
	public double width;

	public double area() {
		return height*width;
	}
}

public class Circle implements Shape {
	public Point center;
	public double radius;
	public double width;

	public double area() {
		return PI*radius*radius;
	}
}
```

`area()`는 다형 메서드다. Geometry 클래스는 필요 없다.  
새 도형을 추가해도 기존 함수에 아무런 영향을 미치지 않는다!  
반면 새 함수를 추가하고 싶다면 도형 클래스를 전부 고쳐야 한다!

*절차적인 도형 클래스*

```java
public class Square {
	public Point topLeft;
	public double side;
}

public class Rectangle {
	public Point topLeft;
	public double height;
	public double width;
}

public class Circle {
	public Point center;
	public double radius;
	public double width;
}

public class Geometry {
	public final double PI = 3.141592653585793;

	public double area(Object shape) throws NoSuchShapeException {
		if(shape instanceOf Square) {
			Square s = (Square)shape;
			return s.side * s.side;
		}
	else if(shape instanceOf Rectangle) {
			Rectangle r = (Rectangle)shape;
			return r.height * r.width;
		}
	else if(shape instanceOf Circle) {
			Circle c = (Circle)shape;
			return PI * c.radius * c.radius
		}
	}
}
```

Geometry 클래스는 세 가지 도형 클래스를 다룬다. 각 도형 클래스는 간단한 자료 구조다.  
아무 메서드도 제공하지 않고, 도형이 동작하는 방식은 Geometry 클래스에서 구현한다.  
만약 Geometry 클래스에 함수를 추가하고 싶다면 도형 클래스는 아무 영향도 받지 않는다!  
새 도형을 추가하고 싶다면 Geometry 클래스에 속한 함수를 모두 고쳐야 한다!

> 자료 구조를 사용하는 절차적인 코드는 기존 자료 구조를 변경하지 않으면서 새 함수를 추가하기 쉽다. 반면, 객체 지향 코드는 기존 함수를 변경하지 않으면서 새 클래스를 추가하기 쉽다.

> 절차적인 코드는 새로운 자료 구조를 추가하기 어렵다. 그러려면 모든 함수를 고쳐야 한다.
객체 지향 코드는 새로운 함수를 추가하기 어렵다. 그러려면 모든 클래스를 고쳐야 한다.

객체 지향 코드에서 어려운 변경은 절차적인 코드에서 쉬우며, 반대는 객체 지향 코드에서 쉽다!

##

### 📘 디미터 법칙

> 메소드가 반환하는 객체의 메소드를 사용하면 안 된다.

모듈은 자신이 조작하는 객체의 속사정을 몰라야 한다. 객체는 자료를 숨기고 함수를 공개한다.  
→ 객체는 조회 함수로 내부를 공개하면 안 된다는 의미다. 내부구조를 숨기지 않고 노출하는 셈이다.

**기차 충돌**

여러 객체가 한 줄로 이어진 기차처럼 보인다. 조잡하다.

`String outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();`

→ 메소드가 반환하는 객체의 메소드를 사용해서 디미터 법칙 위반  
→ 아래 코드로 변환하는 것이 바람직함.

```java
Options opts = ctxt.getOptions();
File scratchDir = opts.getScratchDir();
String outputDir = scratchDir.getAbsolutePath();
```

→ ctxt, opts, scratchDir이 객체라면 디미터 법칙 위반  
→ ctxt, opts, scratchDir이 자료 구조라면 내부 구조를 노출하므로 디미터 법칙이 적용되지 않음.
