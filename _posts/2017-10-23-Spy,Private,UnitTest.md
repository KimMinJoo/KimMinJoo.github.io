---
layout: post
title: JAVA] @Spy, private method and UnitTest
---

@Spy를 이용해 제대로된 UnitTest작성하기!!<br>
이 포스트의 경우 Java, JUnit, Mockito등을 이용해 예시를 작성하였다.

## Unit Test란?
- 단위 : 하나의 조직 따위를 구성하는 기본적인 한 덩어리
- 테스트 : 사람의 학력, 지능, 능력이나 제품의 성능 따위를 알아보기 위하여 검사하거나 시험함. 또는 그런 검사나 시험.
단위테스트란 글자그대로의 뜻을보면 한 덩어리의 성능 따위를 알아보기 위하여 검사하거나 시험하는 행위를 의미한다.<br>
프로그래밍적인 관점에서 본다면 특정 모듈(혹은 메소드등)이 의도한 성능으로 작동하는지 검사하는 행위이다.<br>

### Unit Test를 하는 이유?
UnitTest를 하는 이유는 Unit별로 자신이 진 책임만 검사하는것에 의의가 있다.<br>
자신이 진 책임만 검사한다는것은 Unit이 자신과 관련된 Unit(ex - 메소드)들과의 연관성을 끊고 자신이 해야하는 일에 관해서만 TestCase를 짠다는 것을 의미한다.<br>
이렇게 UnitTest를 잘 짰을때의 장점은 소스코드를 변경하고 TestCase가 실패했을 때 관련된 메소드들은 볼 필요없고 Unit자신 혹은 TestCase가 잘못되었다는것을 확신할 수 있다.<br>
즉 TestCase가 실패했을 때 살펴볼 범위가 굉장히 줄어든다는 장점이 생긴다.<br>
아래와 같은 클래스가 있다고 할 때를 예시로 설명하겠다.
```java
public class 테스트클래스 {
	private 연관클래스 연관클래스 = new 연관클래스();
	/**
	 * 숫자1, 숫자2를 더한 뒤 더해진 숫자에 숫자3을 곱하는 함수.
	 */
	public int 더한뒤곱하기(int 숫자1, int 숫자2, int 숫자3) {
		int 더한숫자 = 연관클래스.덧셈(숫자1, 숫자2);
		return 연관클래스.곱셈(더한숫자, 숫자3);
	}
}
```
위와 같은 코드가 존재할 때 '더한뒤곱하기'라는 함수에 대한 테스트를 할 때, 그 테스트는 더하기가 잘 되었는지 혹은 곱하기가 잘되었는지는 확인할 필요가 없다.<br>
그 이유는 올바른 더하기를 하는것은 '덧셈'라는 함수의 책임이고, 올바른 곱하기를 하는것은 '곱셈'라는 함수의 책임이기 때문에 '덧셈', '곱셈' TestCase에서 확인을 하면 된다.<br>
'더한뒤곱하기'라는 함수는 파라미터 숫자1, 숫자2, 숫자3이 들어왔을 때, '덧셈'함수에 숫자1, 숫자2를 더한 '더한숫자'와 숫자3을 잘 넘겨주는지만 확인하면 된다.<br>
따라서 '더한뒤곱하기'라는 함수의 TestCase에서는 아래의 두가지를 확인해봐야 한다.
- '덧셈'함수에 파라미터 숫자1, 숫자2를 넣어 호출했는가?
- '곱셈'함수에 파라미터 더한숫자, 숫자3을 넣어 호출했는가?

위의 두가지를 확인할 TestCase코드를 보면 아래와 같다.
```java
@RunWith(MockitoJUnitRunner.class)
public class 테스트클래스Test {
	@InjectMocks
	private 테스트클래스 테스트클래스 = new 테스트클래스();

	@Mock
	private 연관클래스 연관클래스;

	@Test
	public void 더한뒤곱하기_test() {
		//given
		int 숫자1 = 1;
		int 숫자2 = 2;
		int 숫자3 = 3;
		int 더한숫자 = 4;  // 이게 제대로된 값인지는 더한뒤곱하기 함수에서는 알 필요가 없다.
		
		//when
		when(연관클래스.덧셈(숫자1, 숫자2)).thenReturn(더한숫자);
		when(연관클래스.곱셈(더한숫자, 숫자3)).thenReturn(더한숫자 * 숫자3);
		테스트클래스.더한뒤곱하기(숫자1, 숫자2, 숫자3);

		//then
		verify(연관클래스).덧셈(숫자1, 숫자2);
		verify(연관클래스).곱셈(더한숫자, 숫자3);
	}
}
```

### private method의 UnitTest
위에서 설명한 예시에서는 연관클래스라는것이 존재하면서 Mockito를 이용해 연관성을 끊고 '더한뒤곱하기'함수의 단위테스트를 할 수 있었다.<br>
하지만 위와 같은 코드가 아니라 '덧셈', '곱셈'함수가 테스트클래스의 private method라면 어떤식으로 테스트를 해야할까?<br>
일단 기본적으로 private메소드의 경우 간단한경우 인라인함수로 생각하고 호출되는 함수의 TestCase에서 같이 Test할 수 있다.<br>
간단히 정리하자면 '덧셈', '곱셈'함수는 책임이 없고 '더한뒤곱하기'함수에서 모든 책임을 지는것이다.<br>
인라인 함수로 생각할 경우의 소스코드와 TestCase코드
```java
public class 테스트클래스 {
	/**
	 * 숫자1, 숫자2를 더한 뒤 더해진 숫자에 숫자3을 곱하는 함수.
	 */
	public int 더한뒤곱하기(int 숫자1, int 숫자2, int 숫자3) {
		int 더한숫자 = 덧셈(숫자1, 숫자2);
		return 곱셈(더한숫자, 숫자3);
	}

	private int 덧셈(int 숫자1, int 숫자2) {
		return 숫자1 + 숫자2;
	}

	private int 곱셈(int 숫자1, int 숫자2) {
		return 숫자1 * 숫자2;
	}
}

//############테스트케이스###############

@RunWith(MockitoJUnitRunner.class)
public class 테스트클래스Test {
	@InjectMocks
	private 테스트클래스 테스트클래스 = new 테스트클래스();
	@Test
	public void 더한뒤곱하기_test() {
		//given
		int 숫자1 = 1;
		int 숫자2 = 2;
		int 숫자3 = 3;
		int 더한숫자 = 숫자1 + 숫자2;  
		int 기댓값 = 더한숫자 * 숫자3;

		//when
		int 실제값 = 테스트클래스.더한뒤곱하기(숫자1, 숫자2, 숫자3);

		//then
		assertEquals(기댓값, 실제값);
	}
}
```

하지만 private함수 즉 '덧셈', '곱셈'함수가 엄청나게 복잡한 로직을 가져서 '덧셈', '곱셈'함수에 대한 TestCase가 필요한경우가 있을 수 있다.<br>
즉 '덧셈', '곱셈'함수가 각자 책임을 가지고 '더한뒤곱하기'함수는 맨처음 예시였던 연관클래스때와 같은 두가지 책임만을 가진다.<br>
- '덧셈'함수에 파라미터 숫자1, 숫자2를 넣어 호출했는가?
- '곱셈'함수에 파라미터 더한숫자, 숫자3을 넣어 호출했는가?

private함수에 대한 TestCase가 필요한 경우의 소스코드와 TestCase코드
```java
public class 테스트클래스 {
	/**
	 * 숫자1, 숫자2를 더한 뒤 더해진 숫자에 숫자3을 곱하는 함수.
	 */
	public int 더한뒤곱하기(int 숫자1, int 숫자2, int 숫자3) {
		int 더한숫자 = 덧셈(숫자1, 숫자2);
		return 곱셈(더한숫자, 숫자3);
	}

	@VisibleForTesting
	int 덧셈(int 숫자1, int 숫자2) {
		return 숫자1 + 숫자2;
	}

	@VisibleForTesting
	int 곱셈(int 숫자1, int 숫자2) {
		return 숫자1 * 숫자2;
	}
}

//############테스트케이스###############

@RunWith(MockitoJUnitRunner.class)
public class 테스트클래스Test {
	@InjectMocks
	private 테스트클래스 테스트클래스 = new 테스트클래스();
	@Test
	public void 더한뒤곱하기_test() {
		//given
		int 숫자1 = 1;
		int 숫자2 = 2;
		int 숫자3 = 3;
		int 더한숫자 = 숫자1 + 숫자2;
		int 기댓값 = 더한숫자 * 숫자3;

		//when
		int 실제값 = 테스트클래스.더한뒤곱하기(숫자1, 숫자2, 숫자3);

		//then
		assertEquals(기댓값, 실제값);
	}

	@Test
	public void 덧셈_test() {
		//given
		int 숫자1 = 1;
		int 숫자2 = 2;
		int 기댓값 = 숫자1 + 숫자2;  // 이게 제대로된 값인지는 더한뒤곱하기 함수에서는 알 필요가 없다.

		//when
		int 실제값 = 테스트클래스.덧셈(숫자1, 숫자2);

		//then
		assertEquals(기댓값, 실제값);
	}

	@Test
	public void 곱셈_test() {
		//given
		int 숫자1 = 1;
		int 숫자2 = 2;
		int 기댓값 = 숫자1 * 숫자2;  // 이게 제대로된 값인지는 더한뒤곱하기 함수에서는 알 필요가 없다.

		//when
		int 실제값 = 테스트클래스.곱셈(숫자1, 숫자2);

		//then
		assertEquals(기댓값, 실제값);
	}
}
```
위의 테스트코드를보면 이상한점이 한가지 존재한다.<br>
분명 '덧셈', '곱셈'함수에 책임을 나눠주었고 따라서 '더한뒤곱하기'함수에서는 두가지책임만 확인을 한다고 했다.<br>
'덧셈', '곱셈'함수의 경우 자신의 책임을 잘 테스트했다. 하지만 '더한뒤곱하기'함수에서는 자신의 책임이 아니라 아직도 인라인함수때와 같은 TestCase를 가지고있다.<br>
그래서 그부분을 수정하기위해 테스트클래스에 stub을 주고 다시 TestCase를 수정했다.

아래의 소스코드에는 TestCase소스코드중 '더한뒤곱하기'함수의 TestCase소스코드만 적었다.
```java
@Test
public void 더한뒤곱하기_test() {
    //given
    int 숫자1 = 1;
    int 숫자2 = 2;
    int 숫자3 = 3;
    int 더한숫자 = 4;  // 이게 제대로된 값인지는 더한뒤곱하기 함수에서는 알 필요가 없다.

    //when
    when(테스트클래스.덧셈(숫자1, 숫자2)).thenReturn(더한숫자);
    when(테스트클래스.곱셈(더한숫자, 숫자3)).thenReturn(더한숫자 * 숫자3);
    테스트클래스.더한뒤곱하기(숫자1, 숫자2, 숫자3);

    //then
    verify(테스트클래스).덧셈(숫자1, 숫자2);
    verify(테스트클래스).곱셈(더한숫자, 숫자3);
}
```
이렇게 변경하자 아래와 같은 오류가 났다.
```java
org.mockito.exceptions.misusing.MissingMethodInvocationException: 
when() requires an argument which has to be 'a method call on a mock'.
For example:
    when(mock.getArticles()).thenReturn(articles);
```
when()의 안에는 mock객체의 메소드만 올 수가 있다.<br>


### @Spy를 이용한 private method의 UnitTest
위와 같이 private method에 책임을 넘기고 private method에 대한 TC를 짰을 때, 그 private method를 호출하는 method를 테스트할 때 @Spy를 이용하는 것이다.<br>
@Spy의 경우 @Mock과 같이 Mock객체로 만들어주는 어노테이션이다.<br>
@Mock과 @Spy의 차이점은 stub를 지정하지 않은 method를 호출할 경우 오류가 나는 @Mock과 달리 @Spy의 경우 Mock객체로 취급되나 stub를 지정해주지 않은 경우 실제 로직과 동일하게 호출된다.<br>
private method에 책임을 주고 @Spy를 이용해 테스트한 경우의 TestCase코드
```java
@RunWith(MockitoJUnitRunner.class)
public class 테스트클래스Test {
	@Spy
	@InjectMocks
	private 테스트클래스 테스트클래스 = new 테스트클래스();
	@Test
	public void 더한뒤곱하기_test() {
		//given
		int 숫자1 = 1;
		int 숫자2 = 2;
		int 숫자3 = 3;
		int 더한숫자 = 4;  // 이게 제대로된 값인지는 더한뒤곱하기 함수에서는 알 필요가 없다.

		//when
		when(테스트클래스.덧셈(숫자1, 숫자2)).thenReturn(더한숫자);
		when(테스트클래스.곱셈(더한숫자, 숫자3)).thenReturn(더한숫자 * 숫자3);
		테스트클래스.더한뒤곱하기(숫자1, 숫자2, 숫자3);

		//then
		verify(테스트클래스).덧셈(숫자1, 숫자2);
		verify(테스트클래스).곱셈(더한숫자, 숫자3);
	}

	@Test
	public void 덧셈_test() {
		//given
		int 숫자1 = 1;
		int 숫자2 = 2;
		int 기댓값 = 숫자1 + 숫자2;  // 이게 제대로된 값인지는 더한뒤곱하기 함수에서는 알 필요가 없다.

		//when
		int 실제값 = 테스트클래스.덧셈(숫자1, 숫자2);

		//then
		assertEquals(기댓값, 실제값);
	}

	@Test
	public void 곱셈_test() {
		//given
		int 숫자1 = 1;
		int 숫자2 = 2;
		int 기댓값 = 숫자1 * 숫자2;  // 이게 제대로된 값인지는 더한뒤곱하기 함수에서는 알 필요가 없다.

		//when
		int 실제값 = 테스트클래스.곱셈(숫자1, 숫자2);

		//then
		assertEquals(기댓값, 실제값);
	}
}
```

## 결론
위의 예시는 간단하여 사실 private method를 인라인함수로 취급해도 큰 무리가 없다.(사실 함수로 빼는거 자체가 이상하다.)<br>
하지만 예시일뿐이고 실제로 private method가 큰 책임을 가지는 경우도 흔하게 볼 수 있다.<br>
따라서 그럴경우 @Spy를 이용한다면 자신의 책임만을 확인하는 잘만든 TestCase를 만들 수 있다.