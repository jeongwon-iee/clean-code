# 🧷 14장 | 점진적인 개선

깨끗하고 우아한 프로그램을 한 방에 뚝딱 내놓을 수 없다. 프로그래밍은 과학보다 공예에 가깝다.  
깨끗한 코드를 짜려면 먼저 지저분한 코드를 **짠 뒤에 정리해야 한다**.

먼저 1차 초안을 쓰고, 그 초안을 고쳐 2차 초안을 만들고, 계속 고쳐 최종안을 만들어야 한다.  
깔끔한 작품을 내놓으려면 단계적으로 개선해야 한다.

대다수 신참 프로그래머는 (대다수 초딩과 마찬가지로) 이 충고를 충실히 따르지 않는다.  
그들은 무조건 돌아가는 프로그램을 목표로 잡는다. 일단 프로그램이 '돌아가면' 다음 업무로 넘어간다.  
경험이 풍부한 전문 프로그래머라면 이런 행동이 전문가로서 자살 행위라는 사실을 안다.

##

### 📘 간단한 Args 사용법

```java
public static void main(String[] args) {
	try {
		Args arg = new Args("l,p#,d*", args);
		boolean logging = arg.getBoolean('l');
		int port = arg.getInt('p');
		String directory = arg.getString('d');
		executeAppliocation(logging, port, directory);
	} catch (ArgsException e) {
		System.out.printf("Argument error: %s\n", e.errorMessage());
	}
}
```

Args 클래스의 첫째 매개변수는 형식 또는 스키마를 지정한다. 이 문자열은 명령행 인수 세 개를 정의한다.  
첫 번째 -l은 부울 인수다. 두 번째 -p는 정수 인수다. 세 번째 -d는 answkduf dlstnek.   
Args 생성자로 넘긴 둘째 매개변수는 main으로 넘어온 명령행 인수 배열 자체다.

생성자에서 ArgsException이 발생하지 않으면 명령행 인수의 구문을 성공적으로 분석했으며 Args 인스턴스에 질의를 던져도 좋다. 인수 값을 가져오려면 `getBoolean`, `getInteger`, `getString` 등을 이용한다.  

##

