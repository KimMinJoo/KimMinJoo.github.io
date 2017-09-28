---
layout: post
title: 기계학습스터디] Naive Bayes
---

### 확률 이론으로 분류하기

### 베이즈 정리(Bayes' theorem)
확률론과 통계학에서, 베이즈 정리는 두 확률변수의 사전 확률과 사후 확률사이의 관계를 나타내는 정리이다.<br>
식으로 표현할 경우 아래와 같다.<br>
<br>
<img src="https://github.com/KimMinJoo/KimMinJoo.github.io/blob/master/images/BayesTheorem.jpg?raw=true"/>

식에 대해 알아보기전에 용어 정리를 해보자.<br>

- 사전확률 : 이미 알고있는 확률로 위의 식에서 P(A) 들을 의미한다.
- 우도 : 조건부확률의 일종으로 이미 사전확률을 알고 있는 사건들이 발생했을때 다른 사건이 발생하는것을 의미한다.
- 사후확률 : 사전확률과 우도를 통해서 알게되는 조건부 확률로 P(A|B)를 의미한다.

이제 식에대해 알아보자.<br>
기본 P(A|B)에서 식.1로 넘어가는것은 조건부확률에 의해서 넘어가게된다.<br>
그 뒤 식.1에서 식.2로 넘어가는것은 표본공간 S가 A들의 합집합으로 이루어졌고 B가 표본공간S위에서 정의된 사건이기 때문에 가능하다.<br>
<br>
그리고 식의 마지막부분을 보게되면 사후확률이 되는데 사전확률과 우도로 이루어져있다.<br>
따라서 사후확률의 경우 사전확률과 우도의 조합으로 구할 수 있다.<br>
<br>
베이즈정리는 사후확률을 식.1로 구할 수 없을때 사전확률과 우도로 구할수 있다는 정리이다.<br>

#### 베이즈정리 예시 (Bayes' theorem example)
<hr>
크기와 모양이 같은 공이 상자 A에는 검은공2개, 흰공2개가 들어있고, 상자 B에는 검은공1개, 흰공2개가 들어있다고하자.<br>
이때 임의의 한 상자에서 공을 1개 꺼냇더니 검은공이었고 임의의 한 상자에 남은공이 흰공2개일 확률은?
<hr>
문제 해석<br>

- 구해야 하는값 : 두 상자 A, B중 임의로 한 상자를 골라서 검은공을 한개 꺼냈는데 남은공이 흰공 2개일 확률<br>
- 해석 : 검은공을 뽑았을 때, B상자일 확률 

문제 해석을 다 했으니 저 식에 대입해보자.

<img src="https://github.com/KimMinJoo/KimMinJoo.github.io/blob/master/images/BayesTheoremExample.jpg?raw=true"/>


### Naive Bayes란?
특성들 사이의 독립을 가정하는 베이즈 정리를 적용한 확률 분류기의 일종<br>
<br>
간단하게 얘기하자면 속성 x,y가 주어졌을때, <br>
p1(x,y) > p2(x,y)이면, 분류항목 1에 속하고,<br>
p1(x,y) < p2(x,y)이면, 분류항목 2에 속한다.<br>
(여기서 p1,p2함수는 속성x,y가 주어졌을 때 속성 x,y가 분류항목1, 2에 속할 확률을 반환하는 함수이다.)<br>

### Naive Bayes의 특징
- 장점 : 소량의 데이터를 가지고 작업이 이루어지며, 여러 개의 분류 항목을 다룰 수 있다.
- 단점 : 입력 데이터를 어떻게 준비하느냐에 따라 민감하게 작용한다.
- 적용 : 명목형 값. 
<br>
### Naive Bayes와 Bayes Theorem과의 관계
위의 설명에서 Naive Bayes에 대해서 간단하게 얘기한다면,
p1(x,y) > p2(x,y)이면, 분류항목 1에 속하고,<br>
p1(x,y) < p2(x,y)이면, 분류항목 2에 속한다.<br>
라고 얘기했다. 하지만 이러한 두가지 규칙으로는 전체 내용을 설명할 수 없다.<br>
<br>
사실 p1(x,y)는 p(c1|x,y)이며 p2(x,y)는 p(c2|x,y)이다.<br>
즉 풀어서 얘기하면 어떤조건 x,y가 들어왔을때 어떤 분류항목일지, 그 조건부 확률의 비교인것이다.<br>
식으로 표현하면 아래와 같다.<br>
<img src="https://github.com/KimMinJoo/KimMinJoo.github.io/blob/master/images/NaiveBayes%EA%B7%9C%EC%B9%99.jpg?raw=true"/>
<br>
결국 p(c1|x,y)과 p(c2|x,y)를 비교할 때, p(x,y|c1), p(c1), p(x,y|c2), p(c2)만 안다면 대소비교가 가능한것입니다.<br>
따라서 p(c|x,y)를 모르더라도 p(x,y|c)와 p(c)만 알 수 있는 상황에서 나이브베이즈를 사용하면 된다.<br>
<br>
즉 어떤 조건이 들어왔을때 그게 어느 분류인지는 모르지만, 어느 분류에서 어떤 조건들이 많이 나타나는 경우에 사용할 수 있다.

### Naive Bayes예제 코드

예제 코드로는 얘기했던 문서 분류를 진행하겠다.<br>
<br>
문서분류에서 각 단어를 속성처럼 사용해서 해당 문서에 존재하는지 아닌지를 확인해 볼 수 있다.<br>
어휘 목록에 1000개의 단어가 있다고 가정해보자.<br>
<br>
하나의 속성에 대해 N개의 표본이 필요하다면 어휘 목록에 있는 1000개의 속성을 위해서는 N^1000개의 표본이 필요하게된다.<br>
단어 하나가 증가할 때마다 표본의 숫자가 기하급수적으로 늘어나게 된다.<br>
<br>
하지만 속성들이 독립적이라고 가정을 하게 된다면 1000 x N으로 줄게된다.<br>
즉, 하나의 속성(단어)가 다른 속성 옆에도 있을 가능성이 있다는 것이다.<br>
이 가정은 아래의 두가지때문에 사실 틀린것이다.<br>
- 각 단어들은 연관성을 가지고 연관성이 높은 단어들이 같이 등장할 확률이 높다.
- 각 단어들이 모두 독립적이라면 모든 단어들의 중요도가 똑같다는것을 의미한다. 하지만 문장에서 중요한 단어는 존재할 수 있다.

<br>
하지만 이런 가정의 결함에도 불구하고 Naive Bayes는 일을 잘 수행한다.<br>
<br>
이제 코드를 보도록 하겠다.<br>
먼저 문서분류예제이므로 문서를 읽어오는 코드를 작성하겠다.<br> 

#### 문서 분류 코드
```python
def loadDataSet(fileName):
    #파일을 읽는다.
    fr = open(fileName, 'rt', encoding='UTF8')
    #파일의 각 줄로 배열을 만든다.
    arrayOfLines = fr.readlines()
    #0으로된 (파일줄수, k)의 matrix를 만든다.
    classVec = []
    #단어를 저장할 배열을 만든다.
    wordList = [];
    #각 라인의 데이터를 매트릭스와 라벨베열에 넣어준다.
    for line in arrayOfLines:
        #문자 양끝 공백제거. (필요에 따라 생략가능)
        line = line.strip()
        #라인을 space기준으로 스플릿하여 리스트로 만든다.
        listFromLine = line.split(' ')
        #단어리스트에 추가시켜준다.
        wordList.append(listFromLine[0:-1])
        #데이터 마지막에 있는 구분값을 구분벡터에 넣어준다..
        classVec.append(int(listFromLine[-1]))
    return wordList, classVec
```

위의 코드는 파일을 읽어서 단어별 분류와 속성의 분류를 하는 코드이다.<br>
파일의 형태는 각라인이 문장 속성 과 같은 형태로 구성되어있다.
Ex)
```text
my dog has flea problems help pleas 0
maybe not take him to dog park stupid 1
my dalmation is so cute I love hime 0
stop posting stupid worthless garbage 1
mr licks ate my steak how to stop him 0
quit buying worthless dog food stupid 1
```

단어별 분류와 속성 분류가 끝났으면 일단 우리가 Naive Bayes에서 쓸 수 있는 확률로 변환시켜야한다.<br>
아래는 단어들의 중복제거와 중복제거를 통한 각 문장을 벡터로 만드는 함수이다.
#### 단어들의 중복제거
```python
def createVocabList(dataSet):
    vocabSet = set([])
    for document in dataSet:
        vocabSet = vocabSet | set(document)
    return list(vocabSet)
```

#### 문장을 벡터로 변환하는 함수
```python
def setOfWords2Vec(vocabList, sentence):
    returnVec = [0]*len(vocabList)
    for word in sentence:
        if word in vocabList:
            returnVec[vocabList.index(word)] += 1
        else:
            print("the word: %s is not in my vocabulary" % word)
    return returnVec
```


```python
def trainNaiveBayes(trainMatrix, trainCategory):
    numTrainDocs = len(trainMatrix)
    numWords = len(trainMatrix[0])

    # 카테고리수 / 총문장갯수 => 문장이 들어왔을때 어느 카테고리일지 나타내는 확률
    pAbusive = sum(trainCategory) / float(numTrainDocs)
    p0Num = ones(numWords)
    p1Num = ones(numWords)
    p0Denom = 2.0
    p1Denom = 2.0
    for i in range(numTrainDocs):
        if trainCategory[i] == 1:
            p1Num += trainMatrix[i]
            p1Denom += sum(trainMatrix[i])
        else:
            p0Num += trainMatrix[i]
            p0Denom += sum(trainMatrix[i])
    p1Vect = log(p1Num / p1Denom)
    p0Vect = log(p0Num/ p0Denom)

    return p0Vect, p1Vect, pAbusive
```

```python
def classifyNaiveBayes(vec2Classify, p0Vec, p1Vec, pClass1):
    p1 = sum(vec2Classify * p1Vec) + log(pClass1)
    p0 = sum(vec2Classify * p0Vec) + log(1 - pClass1)
    if p1 > p0:
        return 1
    else:
        return 0
```

```python
def executeNaiveBayesTraining(fileName):
    #데이터 로드하고
    listOfPosts, listClasses = loadDataSet(fileName)
    postCount = len(listOfPosts)
    #트레이닝 데이터 뽑고
    trainStartNum = int(postCount * 0.1)
    trainPost = listOfPosts[trainStartNum:]
    #검증 데이터 뽑고
    verificationPost = listOfPosts[:trainStartNum]
    #단어 리스트만들고
    myVocabList = createVocabList(listOfPosts)
    #트레이닝 시키고
    trainMat = []
    for postInDoc in trainPost:
        trainMat.append(setOfWords2Vec(myVocabList, postInDoc))

    p0V, p1V, pAb = trainNaiveBayes(trainMat, listClasses)

    answerCount = 0
    #검증단계.
    for i in range(trainStartNum):
        print(verificationPost[i])
        verificationDoc = array(setOfWords2Vec(myVocabList, verificationPost[i]))
        verificationClass = classifyNaiveBayes(verificationDoc, p0V, p1V, pAb)
        print(verificationClass)
        if verificationClass == listClasses[i]:
            answerCount += 1

    answerPercent = answerCount / postCount * 0.1;
    print("잘맞았나 확률 : %f" % answerPercent)
```