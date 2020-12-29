# 🧷 7장 | 오류 처리

깨끗한 코드와 오류 처리는 확실히 연관성이 있다. 

> 상당 수 코드 기반은 여기저기 흩어진 오류 처리 코드 때문에 실제 코드가 하는 일을 파악하기가 거의 불가능하다.

오류 처리는 중요하지만, 오류 처리 코드로 인해 프로그램 논리를 이해하기 어려워진다면 깨끗한 코드가 아니다.

##

### 📘 오류 코드보다 예외를 사용하라

*잘못된 방법 ) **오류 플래그**를 설정하거나 호출자에게 **오류 코드**를 반환하는 방법*

```java
public class DeviceController {

	...

	DeviceHandle handle = getHandle(DEV1);
	if (handle != DeviceHandle.INVALID) {
		retrieveDeviceRecord(handle);
		if (record.getStatus() != DEVICE_SUSPENDED) {
			closeDevice(handle);
		} else {
			logger.log("Device suspended. Unable to shut down");
		}
	} else {
		logger.log("Invalid handle");
	}

	...
}
```

→ 호출자 코드가 복잡해 진다. 함수를 호출한 즉시 오류를 확인해야 하기 때문이다. 예외를 던지는 게 낫다.

*해결 - 예외 사용*

```java
public class DeviceController {

	...

	public void sendShutDown() {
		try {
			tryToShutDown();
		}
		catch (DeviceShutDownError e) {
			logger.log(e);
		}
	}

	private void tryToShutDown() {
		DeviceHandle handle = getHandle(DEV1);
		DeviceRecord record = retrieveDeviceRecord(handle);

		pauseDevice(handle);
		clearDeviceWorkQueue(handle);
		closeDevice(handle);
	}

	private DeviceHandle getHandle(DeviceId id) {
		...
		throw new DeviceShutDownError("Invalid handle for: " + id.toString());
		...
	}
	
	...

}
```

→ 보기 좋아졌을 뿐 아니라, 코드 품질도 나아졌다. 
디바이스를 종료하는 알고리즘과 오류를 처리하는 알고리즘을 분리했기 때문이다.

##

### 📘 Try-Catch-Finally 문부터 작성하라

try문에서 무슨 일이 생기든 catch 블록은 프로그램 상태를 일관성 있게 유지해야 한다.  
→ 예외가 발생할 코드를 짤 때는 try-catch-finally 문으로 시작하는 편이 낫다.

- 파일이 없으면 예외를 던지는지 알아보는 단위 테스트

```java
@Test(expected = StorageException.class)
public void retrieveSectionShouldThrowOnInvalidFileName() {
	sectionStore.retrieveSection("invalid - file");
}
```

- 단위 테스트에 맞춰 구현한 코드

```java
public List<RecordedGrip> retrieveSection(String sectionName) {
  try{
    FileInputStream stream = new FileInputStream(sectionName);
    stream.close();
  } catch (FileNotFoundException e) {
    throw new StorageException("retrieval error", e);
  }
  return new ArrayList<RecordedGrip>();
}
```

1. 강제로 예외를 일으키는 테스트 케이스를 작성한 후 테스트를 통과하게 코드를 작성한다.
2. try-catch 구조로 범위를 정의하고, TDD를 사용해 필요한 나머지 논리를 추가한다.

→ try 블록의 트랜잭션 범위부터 구현하게 되므로 트랜잭션 본질을 유지하기 쉬워진다.

##

### 📘 미확인 예외(Unchecked Exception)를 사용하라

> Checked Exception은 선언부의 수정을 필요로 하기 때문에 모듈의 캡슐화를 깨버린다

안정적인 소프트웨어를 제작하는 요소로 확인된 예외가 반드시 필요하지 않다는 사실이 분명해졌다.

| 	| Checked Exception |	 UnChecked Exception
|:---:|:---:|:---:|
| 확인 시점 |	컴파일 시점 |	 런타임 시점
| 처리 여부 |	반드시 처리 |	 명시적으로 처리하지 않아도 됨
| 트랜잭션 처리 |	roll-back 하지 않음 |	 roll-back 함
| 예 |	IOException, ClassNotFoundException |	 NullPointerException, ArithmeticException

최상위 함수가 아래 함수를 호출한다. 아래 함수는 그 아래 함수를 호출한다. 단계를 내려갈수록 호출하는 함수 수는 늘어난다.  
최하위 함수가 오류를 확인된 오류를 던진다면 함수는 선언부에 `throws`절을 추가해야 한다.  
그러면 해당 함수를 호출하는 모든 함수가 1) catch 블록에서 새로운 예외를 처리하거나 2) 선언부에 `throws`절을 추가해야 한다.  
결과적으로 최하위 단계에서 최상위 단계까지 연쇄적인 수정이 일어난다!  
`throws` 경로에 위치하는 모든 함수가 최하위 함수에서 던지는 예외를 알아야 하므로 캡슐화도 깨진다.

##


