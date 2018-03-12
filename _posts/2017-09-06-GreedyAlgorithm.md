---
layout: post
title: 알고리즘스터디] Greedy Algorithm
---

### Greedy Algorithm이란?

Greedy = 탐욕 -> 욕심을 부리다 -> 눈 앞의 상황만으로 결정을 내린다 -> 근시안적으로 택하다.<br>
<br>
탐욕알고리즘의 경우 그 순간 최적의 해를 선택하는 알고리즘으로 많이 알고있습니다.<br>
하지만 더 정확한 탐욕알고리즘의 정의는 한번 선택한 결정을 바꾸지 않는 알고리즘입니다.

### 탐욕 알고리즘의 패턴
아무것도 가지지않은 집합으로 시작해 반복문을 돌며 순간순간 결정을 집합에 추가해 나가고 마지막에 나온 집합을 해로 반환한다.

#### 탐욕 알고리즘 패턴 예시
- C : 문제의 조건
- S : 문제의 답
- U : S를 구성할 수 있는 모든 원소의 집합

```java
S = {};
for (u in U) {
	if (u is solution) {
		S = S + {u}
	}
	
	if (is complete) {
		return S;
	}
}
```
### 탐욕 알고리즘의 성질
- 한번 선택한 결정을 바꾸지 않으므로 성능이 뛰어난 알고리즘이 많다.<br>
- 그 순간순간 답을 찾기때문에 항상 최적의 답을 찾는 알고리즘은 아닐 수 있다.<br>

## 탐욕알고리즘의 예시들

### 동전 거스름돈 문제 (Coin Change Problem)
최소한의 동전을 이용하여 거스름돈을 만드는 방법을 찾는 문제를 의미한다.<br>
이때 거스름돈을 만들때 가능한 한 동전의 개수가 적으면 적을수록 좋다.<br>

#### 예시
거스름돈 : 170원<br>
<br>

|       | 10원 | 50원 | 100원 | 500원 |
|:-----:|:----:|:----:|:-----:|:-----:|
| 후보1 |   17 |    0 |     0 |     0 |
| 후보2 |   12 |    1 |     0 |     0 |
| 후보3 |    7 |    2 |     0 |     0 |
| 후보4 |    2 |    3 |     0 |     0 |
| 후보5 |    2 |    1 |     1 |     0 |

170원이란 돈을 거스름돈으로 만드는 방법은 위의 표와 같이 5가지가 있다.<br>
그 중 동전의 개수가 가장 적은 후보5가 최적해라고 할 수 있다.<br>
<br>
이처럼 적은 수의 동전으로 거스름돈을 구하려면 어떻게 해야할까?<br>
<br>
먼저 어떤 '기준'을 사용하여 동전을 선택할 수 있을까?<br>
일상생활에서 거스름돈을 어떻게 계산하는지 생각해보면 간단히 알 수 있다.<br>
그 기준은 큰 단위의 동전부터 차례로 사용하는 것이다.<br>
큰 동전의 개수를 구하고 큰 동전을 더이상 쓸 수 없을 때 그다음으로 큰 단위 동전을 이용하면 됩니다.<br>
<br>
### Pseudo Code
```java
동전들을 정렬
for (동전 : 동전들) {
	동전의 최대 갯수 선택
	개수저장
	정답일 경우 알고리즘종료
}
```

### 구현 코드
```java
public class CoinChangeProblem {
	Integer[] conins;

	public CoinChangeProblem(Integer[] conins) {
		this.conins = conins;
		Arrays.sort(conins, Collections.reverseOrder()); // 이 문제에서 소팅까지 구현할 필요가 없으므로 구현된걸 사용하기 위해 int가 아닌 Integer사용
	}

	public void printCoinChange(int totalChange) {
		for (Integer coin : conins) {
			int coinNum = totalChange / coin;
			totalChange = totalChange - (coin * coinNum);
			System.out.println(coin + "원의 개수 : " + coinNum);
		}
	}
}
```

#### TestCode

```java
public class CoinChangeProblemTest {
	/**
	 * 최적해인 경우 테스트
	 */
	@Test
	public void CoinChangeProblemTest1() {
		Integer[] coins = {10, 100, 500, 50};
		CoinChangeProblem coinChangeProblem = new CoinChangeProblem(coins);
		coinChangeProblem.printCoinChange(170);
	}
}
```

|  동전 | 동전개수 |
|:-----:|:--------:|
|  10원 |        2 |
|  50원 |        1 |
| 100원 |        1 |
| 500원 |        0 |


```java
public class CoinChangeProblemTest {
    /**
	 * 최적해가 아닌 경우 테스트
	 * 120원 2개가 최적해.
	 */
	@Test
	public void CoinChangeProblemTest2() {
		Integer[] coins = {10, 120, 100, 200, 50};
		CoinChangeProblem coinChangeProblem = new CoinChangeProblem(coins);
		coinChangeProblem.printCoinChange(240);
	}
}
```

|  동전 | 동전개수 |
|:-----:|:--------:|
|  10원 |        4 |
|  50원 |        0 |
| 100원 |        0 |
| 120원 |        0 |
| 200원 |        1 |

<br>

***

### 부분배낭문제(Kanpsack Problem)
배낭문제의 경우 어떤 물건을 효율적으로 담을지 결정하는 문제를 말한다. 따라서 배낭에 담긴 물건의 가격 총액이 가능한 높도록 물건을 담아야 한다.<br>
예를들어 설명하면 아래와 같다.

|  이름 |  무게  |  가격  |
|:-----:|:------:|:------:|
|   A   |      1 |     500|
|   B   |      2 |     300|
|   C   |      3 |     900|
|   D   |      4 |     800|

위 표와 같은 물건이 존재하고 배낭의 무게가 총 5Kg이라 할때 A와 C를 담는게 최적의 방법이다.<br>
<br>
배낭문제가 어떤 문제인지는 알아봤고, '부분'배낭문제에 대해 알아보자.
부분배낭문제의 경우 짐을 쪼갤 수 있는 경우를 뜻한다.<br>
위의 예시를 그대로 사용한다고하면 A와 C를 담았을 경우 무게는 4kg이고 배낭의 총 무게는 5kg이므로 1kg이 남는다.
따라서 아래와 같이 담을 때 최적이라고 볼 수 있다.

```text
KnapsackSimpleExample
Item : A num : 1.0
Item : C num : 1.0
Item : D num : 0.25
Item : B num : 0
```

#### Pseudo Code
```java
아이템을 효율(무게당 가격) 순서로 정렬
for (item : itemList) {
	if (남은무게가 0이 넘는가?){
		item의 갯수 구하기.
		남은무게 다시계산.
	}
}
```
#### 구현 코드
```java
public class Knapsack {
	private List<Item> itemList;
	private double weight;

	public Knapsack(List<Item> itemList, double weight) {
		this.itemList = itemList;
		this.weight = weight;
	}

	public void printFractionalKnapsackProblem() {
		Collections.sort(itemList, new UnitValueComparator());

		double remainnigWeight = weight;
		for (Item item : itemList) {
			if (remainnigWeight > 0) {
				double number = remainnigWeight / item.getWeight();
				number = number > (double)item.getNumber() ? item.getNumber() : number;
				System.out.println("Item : " + item.getName() + " num : " + number);
				remainnigWeight -= item.getWeight() * number;
			} else {
				System.out.println("Item : " + item.getName() + " num : " + 0);
			}
		}
	}
}
```

#### Test Code

테스트 코드의 경우 Junit5를 활용했다. Junit5는 다른 포스트에서 다룰 예정이다.
```java
@ExtendWith(MockitoExtension.class)
public class KnapsackTest {
	@BeforeEach
	public void init() {
		System.out.println("=====테스트 시작====");
	}

	@AfterEach
	public void done() {
		System.out.println("=====테스트 종료====");
	}


	@DisplayName("Junit5를 활용한 Knapsack테스트")
	@ParameterizedTest
	@MethodSource("knapsackParam")
	public void KanpsackTest(Parameters<List> parameters) {
		System.out.println(parameters.getDisplayName());
		//when
		Knapsack knapsack = new Knapsack(parameters.getParam(), parameters.getWeight());

		//then
		knapsack.printFractionalKnapsackProblem();
	}

	public static Stream<Parameters> knapsackParam() {
		Item item1 = new Item("A", 500, 1, 1);
		Item item2 = new Item("B", 300, 2, 1);
		Item item3 = new Item("C", 900, 3, 1);
		Item item4 = new Item("D", 800, 4, 1);
		List<Item> itemList1 = new ArrayList<>();
		itemList1.add(item1);
		itemList1.add(item2);
		itemList1.add(item3);
		itemList1.add(item4);
		Parameters<List> parameter1 = new Parameters<List>("KnapsackSimpleExample", itemList1, 5);

		Item item5 = new Item("A", 500, 1, 1);
		Item item6 = new Item("B", 300, 2, 1);
		Item item7 = new Item("C", 900, 3, 1);
		Item item8 = new Item("D", 800, 4, 1);
		Item item9 = new Item("E", 1000000, 10, 1);
		List<Item> itemList2 = new ArrayList<>();
		itemList2.add(item5);
		itemList2.add(item6);
		itemList2.add(item7);
		itemList2.add(item8);
		itemList2.add(item9);
		Parameters<List> parameter2 = new Parameters<List>("효율엄청좋은데_배낭무게넘는아이템", itemList2,5);

		Item item = new Item("A", 500, 2, 5);
		List<Item> itemList3 = new ArrayList<>();
		itemList3.add(item);
		Parameters<List> parameter3 = new Parameters<List>("부분배낭문제", itemList3, 9);

		List<Parameters> parmetersList = new ArrayList<>();
		parmetersList.add(parameter1);
		parmetersList.add(parameter2);
		parmetersList.add(parameter3);

		return parmetersList.stream();
	}
}
class Parameters<T> {
	private String displayName;
	private T param;
	private int weight;

	public Parameters(String displayName, T param, int weight) {
		this.displayName = displayName;
		this.param = param;
		this.weight = weight;
	}

	public String getDisplayName() {
		return displayName;
	}

	public void setDisplayName(String displayName) {
		this.displayName = displayName;
	}

	public T getParam() {
		return param;
	}

	public void setParam(T param) {
		this.param = param;
	}

	public int getWeight() {
		return weight;
	}

	public void setWeight(int weight) {
		this.weight = weight;
	}

	@Override
	public String toString() {
		return displayName
				+ " {"
				+ "param=" + param
				+ '}';
	}
}
```

### 허프만 코딩 알고리즘(Huffman coding Algorithm)
허프만 코딩을 제대로 이해하기 위해서는 이 알고리즘이 적용되는 분야에 대한 약간의 지식이 필요하다.<br>
이 알고리즘은 압축알고리즘중 가장 대표적인 알고리즘으로 압축 분야에 대해알아보자.<br>
<br>
압축 방식에도 다양한 방식이 존재한다. 그 중 허프만코딩은 무손실 압축(Lossless compression)방식이다. (zip방식도 허프만 코딩 기반)<br>
무손실 압축이란 원래 자료의 훼손 없이 압축된다는 뜻이다.<br>
예를들어 우리가 zip으로 압축을 하고, 압축을 풀었을 때 손실없이 원본파일 그대로 사용하는 것과 같다.<br>
당연히 손실 압축도 존재한다. 손실압축은 압축을 풀었을 때 원본 파일의 일부가 훼손된 압축 방식이다.<br>
정확성이 중요한 경우 무손실 압축을 사용하고, 속도가 중요한 경우 손실 압축을 사용한다.<br>
<br>
그리고 허프만 코딩 알고리즘에서 특이한 점은 이름에 코딩(coding)이 들어간다는 점이다.
이것은 압축분야의 특징때문인데 압축 분야에서 코딩이란 코드(code)로 바꾼단 뜻이다.
허프만 코딩 알고리즘은 간단히 설명하면 특정 문자를 어떠한 코드로 바꾸고 그 코드를 다시 특정문자로 치환하는 방식이다.
그림으로 보자면 아래와 같다.<br>
<img src="https://github.com/KimMinJoo/KimMinJoo.github.io/blob/master/images/HuffmanCodingAlgorithm.png?raw=true"/>
<br>
### 가변 길이 코딩
허프만 코딩의 핵심 개념은 빈도수에 따른 가변 길이 코딩이다.
가변 길이 코딩이란 가변 길이 코드로 변환하는 작업을 의미한다. 가변 길이 코드란 각 문자마다 코드의 길이가 다른 것을 의미한다.
(반대로 고정 길이 코드도 존재하며, 대표적인 고정 길이 코드로는 아스키코드가 존재한다.)<br>
<br>
가변 길이 코드는 고정 길이 코드에 비해 복호화가 불편하다는 단점이 있다.
고정 길이 코드의 경우 고정된 길이만큼 잘라서 읽으면 되지만, 가변 길이 코드의 경우 길이가 변하므로 코드집합의 한 글자마다 탐색을 해야한다.
하지만 가변 길이 코드의 경우 자주 사용된 문자의 코드 길이를 줄이면 고정 길이 코드에 비해 저장 공간을 줄일 수 있다.<br>
<br>

### 접두어 코드(Prefix Code)

지금까지 허프만 코딩은 파라미터를 받아 가변길이코드로 바꾸는 것이라고했다. 또한 자주사용하는 글자의 코드는 짧아야하 한다고 했다.
여기서 주의할 점은 가변길이코드는 중복되지 않아야 한다는 것이다. 중복이 된다면 당연히 복호화가 불가능하기 때문이다.<br>
<br>
그럼 접두어 코드란 뭘까?<br>
접두어 코드란 '한 코드가 다른 코드의 접두어가 되지않는 코드'를 의미한다.
이런 접두어 코드가 필요한 이유를 보자.<br>
Ex)
A : 0
B : 01
이라는 코드값을 가진다고 치자.<br>
<br>
010...이라는 숫자를 복호화 할 때 A10...인지 B0...인지 복호화 과정에선 알 수가 없다. 즉 복호화가 불가능하다.<br>
<br>
아래는 접두어코드와 비 접두어코드 예시이다.

|                접두어코드                |       비 접두어코드      |
|:----------------------------------------:|:------------------------:|
| A : 0                                    | A : 0                    |
| B : 11 -> 0으로 시작하지 않음            | B : 11                   |
| C : 100 -> 0, 11으로 시작하지 않음       | C : 100                  |
| D : 1010 -> 0, 11, 100으로 시작하지 않음 | D : 1001 -> 100으로 시작 |

위의 표를 예시로 A AB ABC ABCD를 코딩해보면 0 011 011100 0111001010 으로 20비트가 필요하다. (아스키코드를 사용할 경우 80비트가 필요하다.)


## 그래프를 활용한 탐욕알고리즘
* * *
### 그래프(Graph)
그래프는 노드와 간선을 이용한 복잡한 현실 세계를 손쉽게 나타낼 수 있어 여러 자료구조중 표현능력이 가장 뛰어나다.<br>

### 용어 설명

- 노드(Node) : 정점을 의미하며 Vertex라고도 한다.<br>
- 에지(Edge) : 정점간의 관계를 나타낸다.<br>

아래의 그림1은 노드와 에지를 표현한 그림이다.<br>
<img src="https://github.com/KimMinJoo/KimMinJoo.github.io/blob/master/images/graph.jpg?raw=true"/>
그림1. 노드와 에지

- 그래프(Graph) : 그래프란 노드집합과 에지집합으로 이루어진 자료구조를 뜻한다. 둘 중 공집합이 존재하더라도 그래프이다.<br>
간단히 표현한다면 (V,E)로 표현할 수 있다.
- 숲(Forest) : 순환이 존재하지 않는 그래프를 의미한다.(Acyclic Graph)
- 나무(Tree) : 순환이 존재하지않으며 모든 노드가 연결된 그래프를 의미한다.(Connected Acyclic Graph)
- 신장 트리(Spanning Tree) : 어떤 그래프의 서브 그래프중 모든 노드를 포함하는 Tree를 의미한다.<br>
아래는 신장트리의 예이다.

<img src="https://github.com/KimMinJoo/KimMinJoo.github.io/blob/master/images/minimum_spanning_trees.svg.png?raw=true"/>
그림2. 그래프와 신장트리

### 다익스트라 알고리즘(Dijkstra Algorithm)
다익스트라 알고리즘은 최단거리를 찾는 알고리즘중에 가장 널리 알려진 알고리즘이다.<br>
다익스트라 알고리즘의 아이디어는 어느 한 노드 V에서 가장 가까운 노드 K개를 알고 있을때,<br>
K+1번째 가까운 노드는 내가 알고있는 K개의 노드 혹은 V와 직접 연결되어있다는 것이다.

#### 다익스트라 알고리즘의 사용 조건
- 모든 간선의 비용이 양수이어야 한다.
- Direct Graph 이어야 한다. UnDirected Graph일 경우 Directed Graph로 변환 후 사용해야 한다.

#### 용어설명
코드 설명전에 설명에 쓰일 용어
- 거리 : 간선의 비용
- G : 입력받은 Graph
- V : 입력받은 Graph의 모든 노드 집합
- v0 : 입력받은 시작노드
- g(v1, v2) : v1에서부터 v2까지의 거리, 연결되어 있지않다면 ∞를 반환
- dmin(v) : v0에서부터 v까지의 현재 알고있는 최소 거리를 저장한 집합
- T : v0에서부터 최소거리를 아는 노드들의 집합
- min(param1, param2) : param1, param2중 작은 값을 반환


### Pseudo Code
```java
for (v in V) { 
	dmin(v) = g(V0,v)
} 
T = {v0}
while (T의 원소 개수 != n) {
	T에 속하지 않는 노드중 최소 dmin값을 가진 노드 u 선택.
	 T = T + {u} 
	 for T에 속하지 않는 노드 w { 
		dmin(w) = min(dmin(w),dmin(u) + g(u,w))
	} 
}
```

### 증명
다익스트라 알고리즘을 증명할 땐 아래의 두 사실을 전제조건으로 한다.<br>
- 어떤 노드 u가 T에 속할 때 dmin(u)는 실제 최단경로 길이이다.
- 어떤 노드 u가 T에 속하지 않을 때 dmin(u)는 T에 속한 노드들만 거쳐서 u까지 가는 최단 경로의 길이이다.
<br>
이 두가지를 전제조건으로 하는 이유는 첫번째 전제를 만족한다면 이 알고리즘이 증명되기 때문이다.<br>
Pseudo Code를 보면 알고리즘이 끝나는 경우는 T의 원소개수가 n개일 때이다.<br>
즉 T에 모든 노드가 속했고 그 dmin(u)가 최단경로들의 집합이므로 최단경로를 모두 구한 상태가된다.<br>
두번째 전제의 경우 첫번째 전제를 증명하는 과정에 필요하다.<br>
<br>
다익스트라 알고리즘의 증명은 수학적 귀납법을 이용한다.<br>
수학적 귀납법이란 처음에 어떠한 상태를 만족하고 특정할 수 없는 어떤 시점에 그 상태를 또 만족한다면 항상 그 상태를 만족한다는것입니다.<br>
<br>
수학적 귀납법이므로 시작을 보겠습니다.<br>
시작 : T = {v0}<br>
dmin(v0)는 g(v0,v0)이다. 다익스트라 알고리즘의 경우 모든 에지의 비용이 양수이므로 g(v0,v0)는 최단경로이다.<br>
따라서 1번 가정을 만족한다.<br>
v0가 아닌 노드들을 w라고 한다.<br>
dmin(w)는 초기화 과정에 따라 g(v0, w)이다.<br> 즉 T에 속한 v0를 거쳐서 가는 최단경로의 길이이다.<br>
따라서 2번 가정도 만족한다.<br>
<br>
스텝 : T = T + {u}<br>
어떠한 시점에 T에 어떤 노드 u가 추가되는 경우이다.<br>
이때 위의 전제조건을 만족해야한다.<br>
먼저 첫번째 전제조건이 맞는지 보겠다.<br>
두번제 전제조건에 따라 dmin(u)는 T에 속한 노드만을 거쳐서 u까지 가는 최단 경로이다.<br>
그리고 예를 들어 u까지가는 실제 최단경로가 dmin(u)가아니라 T에 속하는 노드만을 거쳐서 가는게 아니라 T에 속하지 않는 노드들을 거친다고해보자.<br>
이때 실제최단경로에 T에 속하지 않으면서 T와 가장 가까이 있는 노드를 x라고 해보자.<br>
그럼 실제최단경로는 v0 -> ... -> x -> ... -> u와 같은 형태를 띄게 된다.<br>
이경우 dmin(u) > 실제최단경로 -> dmin(x)이다.<br>
그럼 '알고리즘의 T에 속하지 않는 노드중 최소 dmin값을 가진 노드 u선택'에 모순이 생긴다.<br>
따라서 어떤 시점에 T에 새로 속하게 되는 u의 dmin(u)는 실제최단경로이다.<br>
<br>
두번째 전제는 첫번째 전제를 증명할 때 dmin(u) > dmin(x) 라고 장담할 수 있게 해준다.<br>
두번째 전제의 증명은 아래의 단계로 나뉜다.<br>
1. dmin()은 T에 속한 노드들만 거쳐가는 경로이다.<br>
dmin()의 경우 dmin(w) = min(dmin(w), dmin(u) + g(u, w))에서 변한다.<br>
dmin()이 변하는 경우는 dmin(w)가 dmin(u) + g(u, w)보다 작을 경우인데 이때도 결국 T에 있는 노드들을 통한 경로로 변경된다.<br>
따라서 항상 T에 속한 노드들만 거쳐서 가는 경로이다.<br>
2. dmin()은 T에 속한 노드들만 거쳐가는 '최단경로'이다.<br>
dmin()이 업데이트 될 때 T에 추가된 노드는 u뿐이다.<br>
따라서 모든 dmin()값을 갱신할 때 u를 고려해야한다.<br>
u를 거쳐 T에 속한 다른 노드 x를 거쳐 w로 갈 경우가 존재할 수 있다.<br>
이경우 경로를 적어보면 v0 -> ... -> u -> ... -> x -> w와 같이 나타낼 수 있다.<br>
하지만 이때 x는 u보다 먼저 T에 추가된 노드이므로 dmin(x)는 dmin(u)보다 작다.<br>
또한 dmin(x), v0 -> ... -> x에는 u는 존재하지 않는다.<br>
즉 v0 -> ... -> u -> ... -> x -> w는 dmin(x) + g(x,w)보다 작고 dmin(x) + g(x,w)의 경우 x가 T에 추가될 당시 고려되었다.<br>
따라서 저런 경우의 수는 고려할 필요가 없다.<br>
따라서 dmi()을 T에 속한 노드들만 거쳐가는 '최단경로'로 고려할때 새로 추가된 노드 u만 고려해주면된다.<br>
<br>
이렇게 전제를 모두 만족하므로 다익스트라 알고리즘은 최단경로를 구하는 알고리즘이다.<br>

### 최소 신장 트리(Minimum Spanning Tree)
최소신장트리(이하 MST)는 신장트리중 에지들의 비용이 가장 적은 신장트리를 의미한다.

### 프림 알고리즘(Prim Algorithm)
프림 알고리즘은 Tree를 유지하면서 조건에 맞는 에지를 하나씩 추가해나가며 MST를 완성해나가는 방식이다.

#### 용어 설명
코드 설명전에 설명에 쓰일 용어
- G : 입력받을 Connected Graph
- S : 해를 가질 집합
- U : 조건 비교를 하기 위한 노드 집합
- V : G의 노드 집합

### Pseudo Code
```java
S = {}
U = {시작노드}
while (U.equals(V) == false) {
	U에 속한 어느 한 노드 u와 V에서 U에 속하지 않은 어느 한 노드 v를 잇는 에지중 최소의 비용을 가진 에지 uv를 구한다.
	S = S + {uv}
	U = U + {v}
}
```

### 증명
프림 알고리즘또한 수학적 귀납법을 사용해 증명합니다.<br>
프림 알고리즘을 증명할 땐 아래의 아래의 사실을 전제조건으로 한다.<br>
- 집합 S는 항상 적어도 하나의 MST의 서브트리이다.<br>
위의 사실이 증명되었을 때 MST를 구하는 알고리즘인 이유는 프림 알고리즘의 경우 끝나는 시점이 항상 같다.<br>
while문안에 조건이 false가 되었을 때 즉 U와 V가 같게 되었을 때이다.<br>
즉 위의 사실이 항상 만족된다면 종료시점 즉 U가 V와 같을때 S가 MST의 서브트리라면 S는 MST가 된다.<br>
<br>
시작 - S는 공집합이므로 정답MST(이하 T)의 서브트리이다.<br>
<br>
스텝 - 반복문을 한 번 실행하고 나면 S에 e(uv)라는 에지가 추가된다고하자.<br>
그리고 그 집합을 S'라고 하면 아래와 같은 두가지 경우가 나올 수 있다.<br>
- S'가 T의 서브트리일경우<br>
문제가 되지않으므로 계속 진행한다.<br>
- S'가 T의 서브트리가 아닐 경우.<br>
T에 e라는 에지를 추가하고 그 그래프를 T'라고 하자.<br>
T는 MST였는데 e라는 에지가 추가되었으므로 노드와 에지의 수가 같아 무조건 순환이 생기게 된다.<br>
여기선 다시 3가지 경우가 생길 수 있다.<br>
    - 순환의 에지중 하나가 e의 비용보다 클 경우.<br>
    이 경우 T는 MST인데 그 MST에서 e의 비용보다 큰 에지를 빼고 e를 넣을 경우 비용이 더 작은 Spanning Tree가 생기므로 모순이 생긴다.<br>
    따라서 이 경우는 존재할 수 없다.<br>
    - 순환의 에지 중 하나가 e의 비용과 같을 경우<br>
    T'에서 비용이 같은 에지를 빼고 e를 넣으면 기존의 T와는 다르지만 MST가 된다.<br>
    따라서 T를 교체해서 진행하면 된다.<br>
    - 순환의 모든 에지가 e의 비용보다 작은 경우<br>
    순환에 있는 모든 에지들중 S에 속하지 않는 에지는 e를 제외하고서 1개이상 무조건 존재한다.<br>
    순환에 있는 모든 에지들중 e를 제외하고 S에 속하지 않은게 없다면,<br>
    U에 속한 노드와 V에서 U에 속하지 않은 노드를 잇는 에지로서 e가 선택 될 수 없다.<br>
    따라서 순환에는 S에 속하지 않은 에지가 2개이상 존재하고 그 중 e가 제일 작지 않다면 e가 선택되지 않으므로 이 경우는 존재할 수 없다. <br>
결국 어느 경우에서든 S'는 어떤 MST의 서브트리가 되기때문에 전제조건에 만족한다.<br>
즉 프림알고리즘은 MST를 구하는 알고리즘이다.<br>

### 크루스칼 알고리즘 (Kruskal Algorithm)
크루스칼 알고리즘은 앞에서 얘기했던 탐욕알고리즘의 패턴과 정확히 일치한다.<br>
최소신장트리를 구하는 알고리즘 중에서 가장 직관적인 알고리즘이다.<br>
Tree를 유지해나가며 에지를 추가하는 프림알고리즘이랑 다르게 Forest를 유지하며 에지를 추가해나가는 방식이다.<br>

#### 용어 설명
코드 설명전에 설명에 쓰일 용어
- G : 입력으로 받을 Connected Graph
- S : 해를 가질 집합
- E : G의 모든 에지 집합
- n : G의 노드 개수
- U : G의 노드 집합

### Pseudo COde
```java
S = {}
while (S의 원소수 != n-1) {
	E에서 가장 비용이 작은 에지 e를 추출
	
	E = E - {e}
	if (S + {e} is acyclic) {
		S = S + {e}
	}
}
```
### 증명
크루스칼 알고리즘을 증명할 땐 아래의 두 사실을 전제조건으로 한다.<br>
- 알고리즘은 항상 종료된다.<br>
- 집합 S는 항상 적어도 하나의 MST의 서브트리이다.<br>
<br>
먼저 첫번째 전제조건이 프림알고리즘에는 없던 조건이다.<br>
이 조건이 들어간 이유는 크루스칼 알고리즘에서는 무한루프의 가능성이 있기때문이다.<br>
일단 무한루프가 생성될때는 E가 공집합이 될때까지 집합S의  원소수가 n-1이 되지 않았을 때이다.<br>
따라서 E는 공집합이 되지 않는 다는걸 보장하면 알고리즘은 항상 종료가 된다.<br>
반복문에서 가장 비용이 적은 에지 e를 추출하는 단계에서 G의 서브그래프인 G' = (U, S)가 있다고 치자.<br>
이때 G'의 에지수는 n-1개이므로 적어도 두개의 Forest를 가진다.<br>
그리고 G는 Connected Graph이다.<br>
즉 E는 G'에서 서로다른 Forest를 이어주는 에지를 가질수 밖에 없다.<br>
<br>
두번째 전제조건은 프림알고리즘과 같다.<br>
종료조건이 S의 원소수가 n-1이므로 종료가 되었을때 S가 MST의 서브트리라면 S는 서브트리이다.<br>
<br>
크루스칼 알고리즘도 마찬가지로 수학적 귀납법을 이용한다.<br>
시작 - S는 공집합이므로 정답MST(이하 T)중 적어도 어느 하나의 서브트리이다.<br>
<br>
스텝 - 반복문을 한 번 실행하면 S에 e라는 에지가 추가된다. 이 집합을 S'라 하자.<br>
이때 아래와 같은 두가지 경우가 나올 수 있다.
- S'가 T의 서브트리일 경우<br>
문제없이 계속 알고리즘이 진행된다.
- S'가 T의 서브트리가 아닐 경우<br>
T에 e라는 에지를 추가해보자.<br>
이 그래프를 T'라고 하자, T'는 T가 MST이므로 노드와 에지의 수가 같아져 항상 순환이 생기게 된다.<br>
여기서 3가지의 경우가 생긴다.<br>
    - 순환의 에지중 하나가 e보다 비용이 클 경우<br>
    이 경우는 T는 MST인데 그 MST에서 e의 비용보다 큰 에지를 빼고 e를 넣으면 비용의 합이 더 작은 Spanning Tree가 생기므로 모순이다.<br>
    즉 이 경우는 존재할 수 없다.<br>
    - 순환의 에지중 하나가 e의 비용과 같은 경우<br>
    T'에서 비용이 같은 에지를 빼고 e를 넣으면 입력받은 그래프의 다른 MST가 된다.<br>
    따라서 T를 교체해서 진행하면 된다.<br>
    - 순환의 모든 에지가 e의 비용보다 작을 경우
    e가 E에서 가장 비용이 작은 에지로 선택되기전 순환에 존재하는 다른 에지들이 선택 되었을 것이고 S에 추가 되었을 것이다.<br>
    따라서 이 경우는 발생할 수 없다.<br>
결국 어느 경우에서든 S'는 어떤 MST의 서브트리가 되기때문에 전제조건에 만족한다.<br>
즉 프림알고리즘은 MST를 구하는 알고리즘이다.<br>
