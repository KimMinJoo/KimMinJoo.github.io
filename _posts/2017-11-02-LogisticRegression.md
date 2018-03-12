---
layout: post
title: 기계학습스터디] 로지스틱회귀(Logistic Regression)
---

## 로지스틱회귀란?
독립변수의 선형 결합을 이용하여 사건의 발생 가능성을 예측하는데 사용되는 통계 기법이다.<br>
로지스틱회귀의경우 입력값에 따라 출력값이 무한이 나올 수 있는 선형함수를 사용하는 것이 아닌, 특정 분류로 분류할 수 있는 시그모이드함수를 사용한다.

- 장점 : 계산 비용이 적고, 구현하기 쉬우며, 결과 해석을 위한 지식 표현이 쉽다.
- 단점 : 언더피팅(underfitting)경향이 있어, 정확도가 낮게 나올 수도 있다.
- 활용 : 수치형 값, 명목형 값

### 시그모이드 함수(Sigmoid Function)
우리가 필요한 속성을 모두 얻을 수 있으며 분류 항목이 두 개인 경우 함수는 0 혹은 1을 출력해야한다.
이럴때 시그모이드함수가 사용된다.<br>
시그모이드 함수의 경우 아래의 식과 같다.<br>
<img src="https://github.com/KimMinJoo/KimMinJoo.github.io/blob/master/images/SigmoidFunction.jpg?raw=true"/>

x가 0일때 시그모이드함수의 값은 0이된다. x의 값이 증가하면 증가할수록 1에 수렴하며, x의 값이 감소하면 감소할수록 0에 수렴하게 된다.<br>
또한 x의 범위 조절을 통해 기울기를 조절할 수 있다.
<img src="https://github.com/KimMinJoo/KimMinJoo.github.io/blob/master/images/SigmoidGraph.png?raw=true"/>

## 로지스틱회귀에서 시그모이드 사용하기.
위의 식에서 봤듯이 시그모이드함수는 x라는 입력값 하나만 받도록 되어있다.<br>
로지스틱회귀에선 이 입력값을 만들기 위해 각각의 속성에 가중치를 곱한다음 서로 더한값을 x라고 한다.<br>
수식으로 표현하자면 아래와 같다. (w는 가중치이며 p는 속성이다.)<br>
<img src="https://github.com/KimMinJoo/KimMinJoo.github.io/blob/master/images/SigmoidInput.jpg?raw=true"/>
백터로 표현하자면 아래와 같다.<br>
<img src="https://github.com/KimMinJoo/KimMinJoo.github.io/blob/master/images/SigmoidVector.jpg?raw=true"/>

최적값을 구하기 위해선 위의 식들을 통해 인풋값을 구하고, 기울기상승을 이용하여 인풋값의 최적화를 알아본다.

### 기울기 상승
기울기 상승이란 함수에서 최대 지점을 찾고자 할 때, 이 지점으로 이동하는 가장 좋은 방법이 기울기의 방향에 있다는 아이디어를 기반으로한다.<br>
기울기느 ▽기호를 사용하며 아래의 식과 같이 쓸 수 있다.<br>
<img src="https://github.com/KimMinJoo/KimMinJoo.github.io/blob/master/images/GradientDescent.jpg?raw=true"/>
이 수식에 대해서 알기 힘들다면 편미분에 대해서 알아보면 된다.(<a href="https://ko.wikipedia.org/wiki/%ED%8E%B8%EB%AF%B8%EB%B6%84">편미분 - wikipedia</a>)<br>
<br>
기울기 상승 알고리즘은 아래와 같이 표현 가능하다.<br>
<img src="https://github.com/KimMinJoo/KimMinJoo.github.io/blob/master/images/GradientDescent2.jpg?raw=true"/>
이때 알파를 조절하면서 움직임의 범위를 조절할 수 있으며 멈춤 조건이 만족할 때까지 되풀이된다.<br>
기울기 하강의 경우 +기호를 -기호로 바꾸어주면 되며 최대화가 아닌 최소화를 할 경우 사용한다.<br>

#### 기울기 상승 Pseudo Code
```text
모두 1로 설정된 가중치를 가지고 시작.
R번 반복
    입력데이터 집합의 기울기를 계산
    알파 * 기울기로 가중치 벡터를 변경
    가중치 벡터 반환
```

#### 기울기 상승 Code
```python
from numpy import *

'''
fileName을 지정해주면 그 파일의 데이터를 읽어오는 함수이다.
데이터 포맷은 DATA1 DATA2 LABEL이다.
반환값은 읽은 데이터 매트릭스와 라벨 매트릭스이다.
'''
def loadDataSet(fileName):
    dataMat = [];
    labelMat = [];
    fr = open(fileName)
    for line in fr.readlines():
        lineArr = line.strip().split()
        dataMat.append([1.0, float(lineArr[0]), float(lineArr[1])])
        labelMat.append(int(lineArr[2]))
    return dataMat, labelMat
'''
Sigmoid를 간단히 구현한 함수.
'''
def sigmoid(inX):
    return 1.0 / (1 + exp(-inX))

'''
기울기 상승 함수.
dataMatIn의 경우 두개의 서로다른 속성을 두개의 컬럼으로 가진 배열이며 loadDataSet함수에서 0번째 인자를 1.0으로 넣어주므로 100 x 3의 행렬이다.
classLabels의 경우100 x 1 행렬이다.
'''
def gradAscent(dataMatIn, classLabels):
    #들어온 배열을 매트릭스로 변환시킨다.
    dataMatrix = mat(dataMatIn)
    #들어온 배열을 매트릭스로 변환시키고 트랜스포즈시켜 100 x 1에서 1 x 100 로우백터로 변환시킨다.
    labelMat = mat(classLabels).transpose()
    # row 수와 column 수를 구한다.
    rowCount , columnCount = shape(dataMatrix)
    #이 변수는 목적을 향하도록 하기 위한 단계의 크기.
    alpha = 0.001
    #반복 횟수
    maxCycles = 500
    #3 x 1로 초기화
    weights = ones((columnCount, 1))

    for k in range(maxCycles):
        h = sigmoid(dataMatrix * weights)
        error = (labelMat - h)
        weights = weights + alpha * dataMatrix.transpose() * error
    return weights
```
### 확률적인 기울기 상승
지금까지 알아본 기울기 상승은 모든 데이터 집합을 사용한다.<br>
따라서 데이터가 수천개의 속성을 가지고 매우 많은 수의 데이터를 가지고 있다면 성능이 나오지 않을 것이다.<br>
이러한 방법의 대안으로 한 번에 단 하나의 사례를 사용하여 가중치를 갱신하는 것이다.
이러한 방법을 확률 기울기 상승이라고한다.<br>
확률 기울기 상승은 온라인 학습 알고리즘중 하나이다. 온라인 학습 알고리즘의 반대로는 모든 데이터를 한번에 처리하는 일괄 처리 방법이 있다.<br>

#### 확률 기울기 상승 Pseudo COde
```text
모두 1로 설정된 가중치를 가지고 시작
데이터집합내 각각의 데이터를 반복
    데이터 중 하나의 기울기를 계산
    알파 * 기울기로 가중치 벡터를 변경
가중치 벡터 반환
```


