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

## 가장 가까운 두 점 찾기 문제(Closest Pair Problem)

가장 가까운 두 점을 찾는 가장 간단한 방법은 모든 경우를 다 계산해보는 것이다.
예를들면 (점1, 점2)사이의 거리를 구하고 (점1, 점3)사이의 거리를 구하고 비교하면서 나아가는 방법이다.<br>

하지만 이경우에 시간복잡도를 구해보면 O(n^2)이 나오게 된다.
n^2이 나오게되는 경우는 점1 ~ 점n까지 존재한다고 가정할 때, 점1은 2 ~ n까지 n-1개의 점에대해서 경우를 구해야하고, 
점2는 3 ~ n까지 n-2개의 점에 대해서 경우를 구해야하며 이런식으로 쭉 갔을때 결국 1 ~ (n-1)까지 전부 더한 숫자만큼의 경우가 나오게된다.
이를 식으로 풀면 n(n-1)/2 이므로 O(n^2)이 시간복잡도가 된다.<br>

### 분할 정복 알고리즘을 이용한 가장 가까운 두 점 찾기

분할 정복 알고리즘을 적용하는 방법은 간단하다. 
영역을 두 부분으로 나누어 각 부분에서 가장 가까운 거리를 가진 점을 찾는다. 
그 뒤 영역사이에서 가장 가까운 거리를 가진 점들을 찾아 그 거리를 비교하면 된다.
아래의 그림과 같은 상황이다.<br>
<img src="https://raw.githubusercontent.com/KimMinJoo/KimMinJoo.github.io/2bae125894cf8dfa586bd9545a16a2049fc0b5fc/images/ClosestPairProblem.png"/>
이증 가장 가까운 두점은 Min(Dleft, Dcenter, Dright)이다.<br>
<br>
이때 Dleft, Dright를 찾는 방법은 재귀를 이용한 방법으로 아래의 그림과 같으며 이는 분할 단계이다.<br>
<img src="https://raw.githubusercontent.com/KimMinJoo/KimMinJoo.github.io/2bae125894cf8dfa586bd9545a16a2049fc0b5fc/images/ClosestPairProblem2.png"/>

분할단계가 끝난 뒤 각 영역에서 구한 최소값(Min(Dleft, Dcenter, Dright))을 계속해서 합쳐서 다시 그중 최소값을 또 구해나가면 된다.
이는 정복단계이다.<br>
<img src="https://raw.githubusercontent.com/KimMinJoo/KimMinJoo.github.io/2bae125894cf8dfa586bd9545a16a2049fc0b5fc/images/ClosestPairProblem3.png"/>

### Pseudo Code
```java
점들은 x좌표를 기준으로 정렬되어있다고 가정한다.
public Point[] closestPairRecursive(점들) {
	if (점들의 개수가 3개 이하일 경우) {
		return 모든 경우의 대한 최소 거리;
	}
	
	가운데점을 기준으로 점들은 L, R영역으로 분할.
	Dleft = closestPairRecursive(L영역);
	Dright = closestPairRecursive(R영역);
	Dcenter = 중간 영역의 점들에서 가장 가까운 거리 구하기;

    return Min(Dleft, Dright, Dcenter);
}
```

가장 핵심이 되는 코드만 이곳에 올리며 모든 코드가 보고싶을 시 아래의 주소에서 볼 수 있다.
https://github.com/KimMinJoo/AlgorithmStudy/tree/DivideAndConquer
### Java Code
```java
public Pair calculate(Point[] points, int start, int end) {
    if (end - start < 3) {
        if (end - start == 2) {
            return calculateAllCaseClosestPair(Arrays.copyOfRange(points, start, end));
        }

        Pair pair = new Pair(points[start], points[end], calculateDistance(points[start], points[end]));
        return pair;
    }

    int mid = (start + end) / 2;
    Pair leftPir = calculate(points, start, mid);
    Pair rigthPair = calculate(points, mid, end);

    Pair minPair = leftPir.getDistance() < rigthPair.getDistance() ? leftPir : rigthPair;

    Pair centerClosestPair = getCenterClosestPair(Arrays.copyOfRange(points, start, end), minPair.getDistance());

    if (centerClosestPair != null) {
        return centerClosestPair.getDistance() < minPair.getDistance() ? centerClosestPair : minPair;
    }

    return minPair;
}
```