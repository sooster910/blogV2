---

layout: post
title:  "[DataStructure/Algorithm] JavaScript Recursive 재귀 알고리즘 제대로 이해하기 [1]"
tags:  JavaScript DataStructure/Algorithm resursion
---

* 오타나 설명이 잘 못되었다거나 보충설명이 필요하시다고 생각되시면 댓글 남겨 주세요 언제든지 환영입니다 😄


## 재귀함수란?

\
재귀함수의 아이디어는 어떤 함수가 있는데, 그 함수를 계속해서 호출하는 것이다 (자기자신을 재호출 하는 함수). 어떻게 호출하냐면 함수 내의 return값을 같은 함수로 정의 하여 다시 부르는 셈이다. 

```javascript
function recursion(){
            .
            .
            .
    return recursion();
}
```
 아래에서 재귀함수가 어떻게 동작하는지에 대해 좀 더 자세히 언급할 것이다.



<!-- ## 재귀 왜 중요하죠? 어디에 쓰이죠?

재귀함수는 어디에나 쓰인다. 많이 쓰이기 때문에 그만큼 중요하다고 생각한다. 심지어 우리가 재귀함수를 써놓고도 재귀함수인줄 모르고 쓰는 경우도 있다. 

자바스크립트에서 Ajax 통신 할 때, JSON.parse / JSON.stringfy 를 쓰는데  

JSON.parse 를 이루는 코드가 대부분 재귀를 이용한 함수가 많이 쓰여졌다.

document.getElementById,
DOM traversal algorithms
에서도 쓰인다.  -->

## 재귀함수는 콜스택에서 어떻게 동작하는가?

\
1부터 3까지의 합을 구하는 함수이다. 지금 이 재귀함수가 합을 구하는 일에서는 효율적인 알고리즘은 아니지만 쉽게 이해하기 위해 재귀함수를 써보았다. 

```javascript
function sum(num){
    
    if(num === 1) return 1;

    return num + sum(num-1) 
}

sum(3) // 3+2+1 =6

```
 

이 함수가 콜스택에서 어떻게 동작하는지 좀 더 쉽게 이해하기 위해 순서를 그려보았다.

![image](/assets/recursion.png)

콜스택은 함수의 호출을 기록하는 자료구조로써, 함수가 실행되면 이 호출 스택에 하나씩 쌓이고, 함수로부터 return 구문을 통해서 호출스택으로부터 함수가 마지막에 들어온 것 부터 반환된다.
자바스크립트에선 배열에서 push( ), pop( ) 이 스택의 동작과 일치한다.\
`function sum (3)` 이 호출되면, 콜스택에 `sum(3)`이 쌓이고, 아래로 코드를 실행시켜 나간다. 그 다음 라인인 
`if(num === 1) return 1` num은 3이기 때문에, 그 다음라인으로 이동한다. 
`return num + sum(num-1)` return문이 나왔다. 이제 호출스택에서 빠져 나갈 찬스가 생겼다. 하지만 return 구문에 `3+sum(2)`는 완전한 값이 아닌, 함수 `sum(2)` 을 포함 하고 있으므로, 콜스택에 있는 `sum(3)`은 반환되지 못하고, 그 상태에서 `sum(2)` 함수를 호출한다. 결국 return문에 값이 나올 때 까지 호출스택에 그대로 남게 된다. `sum(2)`함수를 호출하는 순간, 콜스택엔 `sum(2)`가 추가가 된다. 그 다음 라인인 `if(num === 1) return 1` false 이므로, 그 다음라인으로 이동한다.
`return num+sum(num-1)` return 구문이 나왔으므로, 호출스택에서 다시 빠져 나갈 찬스가 생겼다. 하지만 `return 2+sum(1)`은 완전한 값이 아니라 함수가 포함 되어있으므로, sum(1)을 호출 시킨다. 
콜스택엔 `sum(1)`이라는 새로운 함수가 추가로 쌓이게 되고, 다음 라인을 실행한다. `if(num === 1) return 1;` num=1 로써 true 이므로 `return 1`이 실행되면서 드디어 return을 하게 되었다. 콜스택에선 이제 맨위에 있는 `sum(1)`을 반환한다. 
남아있는 호출 스택 중 맨 위에 있는 스택은 `sum(2)` sum(2)의 return문 에는 num+sum(2)=1+2=3 이 리턴되고 콜스택에서도 `sum(2)`가 빠진다. 마지막 남은 `sum(3)`은 return값으로 num+sum(3)=3+3=6이 리턴되면서 콜스택에 있는 `sum(3)`도 빠진다. 마지막으로 빈 콜스택이 남게 된다.

이 과정이, `sum(3)` 재귀 함수가 호출되어 반환값을 내기 까지의 과정이다.
여기서 재귀함수를 구현 할 때 중요하게 봐야 할 두가지가 있다.  

* 1.기저 조건문 (base case)
* 2.새로운 input 

### 1.기저 조건문 (base case)

sum(3) 함수 안에 if 구문이 빠진 상태로 호출 하게 된다면?

```javascript
function sum(num){
    
    return num + sum(num-1) 
}

sum(3)

```

`sum(num-1)`이 끝도 없이 무한정으로 실행된다. 참고로 크롬브라우저는 최대 16000 프레임의 스택을 가지고 있는데, 이 최대 범위를 넘게 되면, 다음과 같이 콜스택 사이즈를 초과하였다는 에러를 만나게 된다. 

![image](/assets/recursion1.png)

우리가 어플리케이션을 실행할 때, 수많은 함수를 실행하는데, 위의 함수가 수많은 함수중에 하나라고 한다면, sum()는 계속 해서 호출하고 다음 동작(함수) 로 넘어갈 수가 없고 결국 어플리케이션은 freeze가 되는 현상을 겪게 된다. 

이런 무한 루프 상황을 방지 할 수 있는 방법은, 무한 루프를 멈추게 할 수 있는 if 조건문을 넣는 것이다. 기저조건, base case 라고도 한다. 그래서 기저 조건문를 넣는 것은 매우매우 중요하다.

### 2. 새로운 input
나머지 한가지는, 재귀함수를 호출 할 땐, 다른 parameter(input, argument)를 넣어야 한다. 만약 같은 값으로 넣게되면 리턴하는 값도 같아져서 결국 maximum callstack exceeded  에러를 만나게 될 것이다.

다음 포스트에선 재귀알고리즘을 푸는 접근법과 다양한 예제를 설명할 것이다. 

---

참고자료

* 도서 - 배세민, 2019, 자바 스크립트로 하는 자료 구조와 알고리즘, 에이콘출판 
* Udemy - Colt Steel, JavaScript Algorithms and Data Stucture Masterclass 

