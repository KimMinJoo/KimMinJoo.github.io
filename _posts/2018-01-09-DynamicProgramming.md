---
layout: post
title: 알고리즘스터디] 동적프로그래밍(DynamicProgramming)
---

동적프로그래밍은 Divide and Conquer처럼 큰 문제를 여러개의 작은 문제로 나누어 해결하지만, 
Divide and Conquer와 다르게 작은문제가 재사용이 가능하다면 재사용한다는 점이 다른점이다.<br>
<br>

재사용을 위해서는 메모이제이션(Memoization)이 필요하다.
메모이제이션이란 어떤 부분문제가 해결되었을 때 어떤 부분문제일때 어떤 답이 도출되었는지 저장을 해둔다. 
그리고 다시 같은 부분문제를 만나면 계산을 하는것이아닌 저장된 답을 사용하는것이다.<br>
<br>
즉 동적계획법이란 큰 문제를 작은 문제로 나누어 해결하지만, 메모이제이션을 통해 반복작업을 줄이는 방식의 알고리즘이다.

## 피보나치 수열
먼저 피보나치수열을 통해 동적계획법을 알아보자.<br>
피보나치 수열의 정의란 아래의 수식과 같다.<br>
<img src ="https://raw.githubusercontent.com/KimMinJoo/KimMinJoo.github.io/02bff0ebd34d657022bd46814bcdaeea9220a150/images/fibonacci.jpg"/><br>
수열을 나열해보자면 0, 1, 1, 2, 3, 5, 8, 13, 21, ...이 된다.<br>
<br>
피보나치 수열은 정의 자체가 재귀이기 때문에 쉽게 구현이 가능하다.<br>
```java
public class Fibonacci {
	public static int calculate(int n) {
		if (n < 0) {
			throw new IllegalArgumentException("n must be greater than 0");
		}

		if (n <= 1) {
			return n;
		}
		
		return Fibonacci.calculate(n - 1) + Fibonacci.calculate(n - 2);
	}
}
```
위의 코드는 이해하기 아주 쉬운 코드이다. 하지만 그리 효율적인 코드는 아니다.<br>
<br>
그 이유를 살펴보면 계산과정을 쫒아가보면 알 수 있다.
먼저 fibonacci(n)을 위해선 fibonacci(n-1)과 fibonacci(n-2)를 계산해야한다.
이때 또 fibonacci(n-1)을 계산하기 위해선 fibonacci(n-2)와 fibonacci(n-3)을 계산해야한다.
여기까지만 보더라도 fibonacci(n-2)가 중복계산이 된다는 것을 파악할 수 있다. 
fibonacci(n-3)도 중복계산이 될것이며 이는 n이 크면클수록 중복계산이 많아짐을 의미한다.
Big-O표기법으로 표현하자면 O(2^n)이 된다.<br>
<br>
이 중복계산의 해결은 메모이제이션으로 해결이 가능하다.
일단 프로그래밍적으로 생각을 해보면 계산결과를 저장하기 위해선 공간을 확보해야한다.
피보나치수열의 경우 자기 자신보다 작은 피보나치수열의 결과를 이용하므로, 
fibonacci(n)을 계산하기 위해선 fibonacci(0) ~ fibonacci(n-1)의 계산결과가 필요하다.
즉 n개의 저장공간이 필요하다.<br>
<br>
그 뒤의 방법은 간단하다. 저장공간에 값이 있을경우 그 값을 활용하고 아닐 경우 계산을 진행하면된다.
코드는 아래와 같다.
```java
public class Fibonacci {
	public static int calculate(int n) {
		int[] memo = new int[n];
		for (int i = 0; i < n; i++) {
			memo[i] = -1;
		}
		return calculate(n, memo);
	}

	private static int calculate(int n, int[] memo) {
		if (n < 0) {
			throw new IllegalArgumentException("n must be greater than 0");
		}

		if (n <= 1) {
			return n;
		}

		int prev = memo[n - 1];
		if (prev == -1) {
			prev = calculate(n - 1, memo);
			memo[n - 1] = prev;
		}
		
		int beforePrev = memo[n - 2];
		if (beforePrev == -1) {
			beforePrev = calculate(n - 1, memo);
			memo[n - 2] = beforePrev;
		}

		return prev + beforePrev;
	}
}
```

## 동전 거스름돈 문제
두번째로 동전 거스름돈 문제를 DP를 이용하여 풀어보자.
일단 기본적인 방향은 피보나치와 같다. 
Divide And Conquer를 이용해 풀고 거기서 중복문제를 메모이제이션을 통해 해결하는 것이다.<br>
<br>
일단 분할정복방법으로 문제를 바꾸자면 아래와 같은 정의들이 필요하다.<br>
<img src="https://github.com/KimMinJoo/KimMinJoo.github.io/blob/master/images/%EB%8F%99%EC%A0%84%EA%B1%B0%EC%8A%A4%EB%A6%84%EB%8F%88.jpg?raw=true"/>

분할정복방법을 코드로 보면 아래와 같다.<br>
```java
public class Coin {
	public static int calculate(int money, int[] coins) {
		if (money == 0) {
			return 0;
		}

		int min = Integer.MAX_VALUE;

		for (int coin : coins) {
			if (money < coin) {
				continue;
			}

			int current = calculate(money - coin, coins) + 1;

			if (current < min) {
				min = current;
			}
		}

		return min;
	}
}
```

이제 이 방법을 DynamicProgramming으로 변경하면되는데 위에서 봤던 피보나치와 똑같다. 
먼저 저장해야하는 공간을 만들어야하는데 n원일 때는 그냥 리턴을 하면되고 n원보다 클순 없으니 0 ~ n-1인 n개의 배열을 만들면된다.
그 뒤 저장공간에 값이 있을경우 그 값을 활용하고 아닐 경우 계산을 진행하면된다.
코드는 아래와 같다.
```java
public class Coin {
	public static int calculate(int money, int[] coins) {
		int[] memo = new int[money];
		for (int i = 0; i < money; i++) {
			memo[i] = -1;
		}
		return calculate(money, coins, memo);
	}

	private static int calculate(int money, int[] coins, int[] memo) {
		if (money == 0) {
			return 0;
		}

		int min = Integer.MAX_VALUE;

		for (int coin : coins) {
			if (money < coin) {
				continue;
			}

			int currentMoney = money - coin;
			int current = memo[currentMoney];

			if (current == -1) {
				current = calculate(currentMoney, coins);
				memo[currentMoney] = current;
			}

			if (current < min) {
				min = current;
			}
		}

		return min;
	}
}
```