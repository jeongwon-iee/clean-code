# 🧷 2장 | 의미 있는 이름

### 📘 의도를 분명히 밝혀라

**맥락이 코드 자체에 명시적으로 드러나도록 이름에 정보를 함축하라.**

이름은 다음 정보를 모두 담아야 한다. *변수(혹은 함수나 클래스)의 존재 이유는? 수행 기능은? 사용 방법은?*

```java
int d; // 경과 시간(단위: 날짜)
```

이름 d는 아무 의미도 드러나지 않는다. 주석이 필요하다면 의도를 분명히 드러내지 못했다는 말이다.

```java
int elapsedTimeInDays;
int daysSinceCreation;
int daysSinceModification;
int fileAgeInDays;
```

의도가 드러나는 이름을 사용하면 코드 이해와 변경이 쉬워진다.

```java
public List<int[]> getThem() {
	List<int[]> list1 = new ArrayList<int[]>();
	for (int[] x: theList)
		if(x[0] == 4)
			list1.add(x);
	return list1;
}
```

문제는 코드의 단순성이 아니라 코드의 ***함축성*** 이다.

위 코드는 암암리에 독자가 다음의 정보를 안다고 가정한다.

1. theList에 무엇이 들어있는가?
2. theList의 0번째 값이 어째서 중요한가?
3. 값 4는 무슨 의미인가?
4. 함수가 반환하는 list1을 어떻게 사용할 것인가?

```java
public List<int[]> getFlaggedCells() {
	List<int[]> flaggedCells = new ArrayList<int[]>();
	for(int[] cell: gameBoard) {
		if(cell[STATUS_VALUE] == FLAGGED) 
			flaggedCells.add(cell);
	return flaggedCells;
}
```

정보 제공은 충분히 가능하다. 지뢰찾기 게임을 만든다고 가정하면, `theList`는 게임판이란 사실을 안다.

`theList`를 `gameBoard`로 바꾼다. 

게임판에서 각 칸은 단순 배열로 표현한다. 배열에서 0번째 값은 칸 상태를 뜻한다. 값 4는 깃발이 꽂힌 상태를 가리킨다. 코드는 더욱 명확해졌다.

```java
public List<Cell> getFlaggedCells() {
	List<Cell> flaggedCells = new ArrayList<Cell>();
	for(Cell cell: gameBoard) {
		if(cell.isFlagged()) 
			flaggedCells.add(cell);
	return flaggedCells;
}
```

더 나아가, `int` 배열 대신 칸을 간단한 클래스로 만들어도 되겠다.

`isFlagged()`라는 좀 더 명시적인 함수를 사용해 `FLAGGED`라는 상수를 감춰도 좋겠다.

단순히 이름만 고쳤는데도 함수가 하는 일을 이해하기 쉬워졌다.

##

### 📘 그릇된 정보를 피하라

**코드의 의미를 흐리지 않도록 하라.**

- 널리 쓰이는 의미가 있는 단어를 사용하지 마라.

ex) `hp`, `aix`, `sco`

- 그룹 이름에 List를 사용하지 마라.

ex) `accountList` → `accountGroup`, `accounts`

- 서로 흡사한 이름을 사용하지 마라.

ex) `XYZControllerForEfficientHandlingOfStrings`, `XYZControllerForEfficientStorageOfStrings`

##

### 📘 의미 있게 구분하라

**읽는 사람이 차이를 알도록 이름을 지어라.**

- 연속된 숫자로 이름을 구분하지 마라.

ex) `copyChars(char a1[], char a2[])` → `copyChars(char source[], char destination[])`

- 불용어로 이름을 구분하지 마라.

ex) `ProductInfo`나 `ProductData`는 개념과 의미가 불분명하다.

`Customer`와 `customerInfo`, `moneyAmount`와 `money`, `account`와 `accountData`는 구분이 안 된다.

##

### 📘 발음하기 쉬운 이름을 사용하라

**발음하기 어려운 이름은 토론하기도 어렵다. 이미 알고 있는 단어를 효율적으로 활용하라.**

ex) `genymdhms` → `generationTimestamp`, `modymdhms` → `modificationTimestamp`

##

### 📘 검색하기 쉬운 이름을 사용하라

변수나 상수를 여러 곳에서 사용한다면 검색하기 쉬운 이름을 짓는게 바람직하다. 숫자보단 이름!

```java
for (int j=0; j<34; j++) {
	s += (t[j]*4)/5;
}
```

```java
int realDaysPerIdalDay = 4;
const int WORK_DAYS_PER_WEEK = 5;
int sum = 0;
for(int j=0; j < NUMBER_OF_TASKS; j++) {
	int realTaskDays = taskEstimate[j] * realDaysPerIdalDay;
	int realTaskWeeks = (realTaskDays / WORK_DAYS_PER_WEEK);
		sum += realTaskWeeks;
}
```

이름을 의미있게 지으면 함수가 길어지나, `WORK_DAYS_PER_WEEK`는 찾기가 쉽다. 그냥 5를 사용한다면 5가 들어가는 이름을 모두 찾은 후 의미를 분석해 원하는 상수를 가려내야 한다.

##

### 📘 인코딩을 피하라

- 변수 이름에 타입을 인코딩 하지 않는다.
- 멤버 변수 접두어(`m_`)를 사용하지 않는다.
- 인터페이스 클래스를 구현한 클래스엔 `Impl` 접미어 인코딩은 가능하다.

##

### 📘 자신의 기억력을 자랑하지 마라

문자 하나만 사용하는 변수 이름은 문제가 있다. (루프에서 반복 횟수를 세는 변수 i,j,k는 괜찮다. *l은 절대 No!* )

##

### 📘 클래스 이름

**클래스, 객체 이름은 명사나 명사구가 적합하다.** 

ex) `Customer`, `Account`, `AddressParser`, `Manager`(x), `Processor`(x), `Info`(x), `Data`(x) 

##

### 📘 메서드 이름

**메서드 이름은 동사나 동사구가 적합하다. 메서드 이름은 독자적이고 일관적이어야 한다.**

- 접근자, 변경자, 조건자는 javabean 표준에따라 값 앞에 get, set, is를 붙인다.

ex) `postPayment`, `save`, `deletePage`

```java
string name = employ.getName();
customer.setName("mike");
if(paycheck.isPayed()) ...
```

- 생성자를 overload 할 때는 ***정적 팩토리 메서드*** 를 사용한다. 메서드는 인수를 설명하는 이름을 사용한다.

`Complex fulcrumPoint = Complex.**FromRealNumber**(23, 0);`

##

### 📘 기발한 이름은 피하라

**재미난 이름보다 명료하고 분명한 이름을 선택하라.**

##

### 📘 한 개념에 한 단어를 사용하라

**추상적인 개념 하나에 단어 하나를 선택해 이를 고수하라.**

ex) 똑같은 메서드를 클래스마다 `fetch`, `retrive`, `get` 등으로 제각각 부르지 마라.

ex) 동일 코드 기반에 `controller`, `manager`, `driver`를 섞어 쓰지 마라.

##

### 📘 한 단어는 한 가지 목적으로만 사용하라

**'한 개념에 한 단어를 사용하라'는 규칙을 따르려고 '일관성'을 고려해 같은 이름을 선택하지 마라.**

ex) 지금까지 구현한 `add`메서드는 기존 값 두 개를 더하거나 이어서 새로운 값을 만드는 메서드일 때, 집합에 값 하나를 추가하는 새로운 메서드는 일관성을 고려한 `add`가 아닌 `append`나 `insert`가 적당하다.

##

### 📘 해법 영역에서 가져온 이름을 사용하라

**우선 프로그래머가 통용적으로 사용하는 기술 이름을 사용하라.**

**문제영역에서 모든 이름을 가져오지 마라. 기술 개념에는 기술 이름이 가장 적합한 선택이다.**

ex) VISITOR 패턴을 구현한 `AccountVisitor`, `JobQueue`

##

### 📘 문제 영역에서 가져온 이름을 사용하라

**문제 영역 개념과 관련이 깊은 코드라면 문제 영역에서 이름을 가져와라**

##

### 📘 의미 있는 맥락을 추가하라

**알고리즘, 내용을 읽어야 겨우 유추할 수 있는 이름은 피하라.**

ex) `firstName`, `lastName`, `street`, `houseNumber`, `city`, `state`, `zipcode` 라는 변수들을 보면 주소라는 사실을 알아차릴 수 있다.

하지만 어느 메서드가 `state`라는 변수 하나만 사용한다면? 주소 일부라는 사실을 알아차리기 쉽지 않다.

`addr` 접두어를 추가해 `addrFirstName`, `addrLastName`, ... `addrState`라고 쓰면 맥락이 분명해진다.

물론 `Address`라는 클래스를 생성하면 더 좋다. 그러면 변수가 좀 더 큰 개념에 속한다는 사실이 컴파일러에게도 분명해진다.

```java
private void printGuessStatistics(char candidate, int count) {
	String number;
	String verb;
	String pluralModifier;
	if (count == 0) {
		number="no";
		verb="are";
		pluralModifer="s";
	} else if (count == 1) {
		number="1";
		verb="is";
		pluralModifer="";
	} else {
		number=Integer.toString(count);
		verb="are";
		pluralModifer="s";
	}
	String guessMessage = String.format("There %s %s %s%s", verb, number, candidate,
pluralModifier);
	print(guessMessage);
}
```

독자가 알고리즘을 끝까지 읽어보고 나서야 `number`, `verb`, `pluralModifier`라는 변수 세 개가 '통계 추측' 메시지에 사용된다는 사실이 드러난다. 독자가 맥락을 유추해야만 하는 불분명한 코드다. 함수도 길다.

```java
private class GuessStatisticsMessage {
	String number;
	String verb;
	String pluralModifier;

	public String make(char candidate, int count) {
		return String.format(
						"There %s %s %s%s", 
						verb, number, candidate, pluralModifier);
	}

	private void createPluralDependentMessageParts(int count) {
		if (count == 0) {
		thereAreNoLetters();
		} else if (count == 1) {
			thereIsOneLetter();
		} else {
			thereAreManyLetters(count);
		}
	}
	
	private void thereAreManyLetters(int count) {
		number=Integer.toString(count);
		verb="are";
		pluralModifer="s";
	}

	private void thereIsOneLetter() {
		number="1";
		verb="is";
		pluralModifer="";
	}

	private void thereAreNoLetters() {
		number="no";
		verb="are";
		pluralModifer="s";
	}
}
```

함수를 작은 조각으로 쪼개고자 `GuessStatisticsMessage`라는 ***클래스를 만든 후 세 변수를 클래스에 넣었다.***

이렇게 맥락을 개선하면 함수를 쪼개기가 쉬워지므로 알고리즘도 좀 더 명확해진다.

##

### 📘 불필요한 맥락을 없애라

**의미가 분명한 긴 이름을 추구하되, 불필요한 정보를 중복 추가하지 마라.**
