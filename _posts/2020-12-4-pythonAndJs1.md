---
layout: post
title:  "[ JS vs Python] Reverse string"
tags: Python JavaScript
---
* Algorithm을 공부하며 남긴 글입니다. 오타나 설명이 잘 못되었다거나 조금 더 보충설명이 필요하시다고 생각되시면 댓글 남겨 주세요. 칭찬도 좋습니다. 언제든지 환영입니다 😄

## 목차
 [1.puppeteer + Jest](#puppeteerjest)\
 [2. npm run test 실행환경 구축 ](#npm-run-test-실행환경-구축)\
 [3. headless chromium 테스트 코드로 오픈하기](#headless-chromium-테스트-코드로-오픈하기)\
 [4. beforeEach( ), afterEach( ) 이용하여 리팩토링](#drydont-repeat-yourself)\
 [5. Jest test case 작성](#test-case-작성)

## Python & JavaScript  

파이썬을 처음 접하게 된 계기는 몇 달 전, jp Morgran에서 virtual internship을 했었는데, traders들을 위해  data visulaization dashborad 를 빌드하는 하는 프로젝트 였는데, 자바스크립트를 해서 그런지 if 문이나 for문 그리고 데이터 자료형들을 다루는데 나름 비슷한 부분들이 있어 접근하기가 쉬웠다. 특히나 차이점들에 대해 오~ 신기하다! 자바스크립트에선 이런게 될까? 하며 혼자 한껏 흥미에 취했었다. 하지만 프로젝트가 끝난 후 회사일과 나름 내가 하던 사이드프로젝트를 하고 있던 지라, 파이썬은.. 음음.. 그렇게 되었다. 그치만 알고리즘을 공부할 때 다른 언어와 나의 주언어와 함께 풀었더니 시간은 좀 더 오래걸렸지만 뭔가 습득력이 더 좋은것 같다. 꼭 한가지만 파지 않아도 .. 가끔은 새로운 것과 비교해보면서 익히는 것이 뇌에 프레쉬한 자극을 주잖네! 😁

## Reverse string 
조건 : 주어진 

### 1. 파이썬, JS 모두 reverse()라는 내장함수가 적임자로 주어진다. 


```javascript
    
    //JavaScript

    function reverse(str){
        return str.reverse();
    }
    
    console.log(reverse(str));
    
```

```python
  #python

    def reverse(strArr):
        strArr.reverse()
        return strArr

    print(reverse(['h','e','l','l','o']))
```

이렇게 쉽게 알아서 해준다. 

### 2. 내장함수를 이용하지 않고 접근해보자.  
일단 어떻게 주어진 list or array 를 접근하여 각각의 문자들을 바꿀것인가?   
loop을 한다 생각했을 때, h와 o를 접근 하는 방법은 
array/list의 접근 방법 : str[0],str[str.length-1]  
그럼 매번 iterate 할 때마다 index의 값을 바꿔주면 되겠다. 시작점을 두개의 index를 두고 이동 하는 방법이 있다.  

#### 2.1 투 포인터를 이용하여 접근하기.  

말 그대로 두개의 포인터를 조작하여 logic 을 처리하는 접근법.  

![image](/assets/twopointer.png)
 이미지 출처:[LeetCode](https://leetcode.com/problems/reverse-string/solution/){: target="_blank"}


step1: 주어진 List or Array의 맨 첫 index와 맨 끝 index를 loop의 시작점으로 initialize를 한다.loop이 시작되면, 인덱스 0과 마지막 인덱스를 swap 한다.  
  

step2: 인덱스를 swap하니, h와 o의 자리가 바뀌었다. 그 다음 포인터를 한칸씩 움직여야한다.
어떻게? left pointer는 index++, right pointer 는 index-- 로.  
     
step3: 그럼 언제까지 iterate 을 하는가에 대한 조건문이 필요하다. left와 right의 index가 동일하지 않는 동안 또는 right 인덱스가 left index 보다 크기 때문에, right>left 되는 동안 iterate 하기.

```javascript
//javascript
    function reverse(strArr){
        let left= 0;
        let right = strArr.length-1; 
        let temp;

        while(left < right){
            //여기 3줄이 자바스크립트에서 swap 과정
            temp = strArr[left]; 
            strArr[left] = strArr[right];
            strArr[right] = temp;

            left++;  // 인덱스를 이동하여 다음 loop 을 진행
            right--;
        }        

    }


```

```python
#python
def reverse2(strArr):
    left,right=0,len(strArr)-1

    while left<right:
            strArr[left],strArr[right] = strArr[right],strArr[left]
            left +=1
            right -=1
            
    return strArr

print(reverse2(['h','e','l','l','o']))
```
Time Complexity = O(N)

### 마무리
python의 swap 로직부분에서 너무 쉬운 처리에 조금 놀랬다. JS의 경우 변수 swap을 위해 하나의 변수를 temp라는 임시 변수에 저장해 두고 옮기는데 반해, 파이썬의 경우는 임시 변수를 쓸 필요 없이 바로 인덱스를 이용해서 바꿀 수 있다. 