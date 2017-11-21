---
layout: post
title: 알고리즘스터디] 분할정복알고리즘(Divide and Conquer)
---

분할정복알고리즘은 이름에 모든게 들어있다.<br>
'분할(Divide)'은 나눈다는 뜻이고, '정복(Conquer)' 푼다는 뜻이다. 즉 큰 문제를 정복가능한 작은 문제들로 나누어 푼는 방식이다.<br>

## 이진 검색(Binary Search)
이진검색은 분할 정복 알고리즘의 가장 대표적인 예이다. 이 알고리즘도 이름에서 알 수 있듯이 문제를 2개의 부분 문제로 나누어 검색 문제를 해결한다.<br>
이진 검색의 분할은 '검색 범위를 반으로 줄이는 것'이다. 그리고 마지막에 범위가 1개로 줄었을 때 정복하게 된다.<br>
아래의 그림은 1~8까지 정렬된 배열이 존재할때 이진검색으로 6을 찾는 예시이다.
<img src="https://github.com/KimMinJoo/KimMinJoo.github.io/blob/master/images/BinarySearch.png?raw=true"/>
<br>
<br>
이진검색이 어떤방식으로 돌아가는지 자세히 알아보자.<br>
일단 이진검색의 경우 검색범위를 반씩 줄여가야하기 때문에 자료들이 정렬되어 있어야한다. (여기서는 오름차순으로 정렬되어있다고 가정.)
일단 검색범위를 반씩 줄여가기위해선 중간값을 찾아야한다. 그리고 그 중간값을 기준으로 검색범위를 반으로 줄인다.<br>
위의 예시에서 보면 중간값은 4가 된다. 이때 내가 찾으려는 값이 중간값보다 크다면 중간값부터 더 작은 값들을 버리면되고, 중간값보다 작다면 더 큰 값들을 버리면 된다.
<br>
이런식으로 절반씩 계속 버리다보면 1개의 값이 남는 경우가 생기는데 이 때 그 값이 내가 찾으려는 값이면 검색에 성공하는 것이며, 그 값이 내가 찾으려는 값이 아니라면 검색이 불가능한 값이다.

### Pseudo Code

```java
binarySearchRescursive(list, start, end, key) {
	if (start와 end차이가 1) {
        if (리스트의 값이 내가 찾는값) {
             return start;
         } else {
             return -1;
         }
     }
    	
	중간인덱스 찾기 (중간인덱스 = (start + end) / 2)
	
	if (key <= list.get(중간인덱스)) {
		return binarySearchRescursive(list, start, 중간인덱스, key);
	} else {
		return binarySearchRescursive(list, 중간인덱스 + 1, end, key);
	}
	
}
```


### Java Code

```java
public class BinarySearch {
	public <T extends Comparable> int searchRecursive(List<T> list, T key) {
		return searchRecursive(list, 0, list.size() - 1, key);
	}

	public <T extends Comparable> int searchRecursive(List<T> list, int start, int end, T key) {
		if (start == end) {
			if (list.get(start).equals(key)) {
				return start;
			} else {
				return -1;
			}
		}

		int middle = (start + end) / 2;

		if (list.get(middle).compareTo(key) >= 0) {
			return searchRecursive(list, start, middle, key);
		} else {
			return searchRecursive(list, middle + 1, end, key);
		}
	}
}
```

## 팩토리얼 문제 (Factorial)
팩토리얼 문제 또한 분할정복의 대표적인 예중 하나입니다. 팩토리얼이란 어떤 숫자 n에서 1까지의 범위를 모두 곱하는 식입니다.
<img src="https://github.com/KimMinJoo/KimMinJoo.github.io/blob/master/images/factorial.jpg?raw=true"/>
분할정복에 더 알맞게 팩토리얼식을 고쳐보면 아래와 같아집니다.
<img src="https://github.com/KimMinJoo/KimMinJoo.github.io/blob/master/images/factorialRecursive.jpg?raw=true"/>
이진검색에서는 범위를 반씩 줄였다면 팩토리얼에서는 범위를 1씩 줄이는 것입니다. 아래의 그림으로 설명을 대체하겠습니다.
<img src="https://github.com/KimMinJoo/KimMinJoo.github.io/blob/master/images/showFactorial.png?raw=true"/>

### Pseudo Code

의사코드에선 num은 항상 1보다 크거나 같다고 가정한다.
```java
int factorial(int num) {
	if (num이 1) {
		return 1;
	}
	
	return num 곱하기 num-1의 팩토리얼.
}
```


### Java Code
```java
public class Factorial {
	public int factorial(int num) {
		if (num < 1) {
			throw new IllegalArgumentException("The parameters of factorial method are greater than one, parameter : " + num);
		}

		if (num == 1) {
			return 1;
		}

		return num * factorial(num - 1);
	}
}
```