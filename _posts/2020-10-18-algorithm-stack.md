---
layout: post
title:  "[DataStructure/Algorithm] JavaScript 스택(Stack)"
description:  Stack이 뭔가요? 
tags: JavaScript DataStructure/Algorithm Stack 
---

## Stack 

![image](/assets/stack.png)
스택은 접근성에 제한이 있는 선형 자료형이다. 즉, 데이터를 접근할 때 한방향에서만 일어난다. 
스택의 구조를 이용하면 스택 최상단의 항목을 쉽게 꺼낼 수 있다.
만약, 깊은 항목에 도달하려면 최상단부터 원하는 항목전까지 제거해야 한다.
이런 스택이 가득차 다음 항목을 넣을 만한 공간이 충분치 않은경우의 상태를 stack over flow (스택 오버 플로우) 라고 한다. 

스택이란 용어를 가장 쉽게 접근할 수 있는 곳은 가상 메모리의 한 영역인 스택이다. 가상메모리는 프로그램을 실행하면 정보들을 저장하게 되는데, 스택이란 영역에서는 함수의 호출
스택은 데이터를 넣을 수 있는 선형(linear) 자료형이다. 
위에 그림에서 스택에 1,2가 모두 있는 상태에서, 1에 접근 하기 위해선,맨 마지막에 추가한 2를 제거한 후에 접근할 수 있다. 그래서 LIFO(last in first out). 
>스택의 장점 : 속도가 빠르다 O(1) .
>스택의 한계 : 배열과 달리 마지막에 추가된 항목외에는 직접 접근할 수가 없다. (ex: 1이란 값을 바로 접근 하지못하고, 2를 제거한후에 1에 접근가능)

---
### 스택이 쓰이는 경우,
* Managing function invocations
* Undo/Redo
* Routing (the history object ) is treated like a stack.

---
### 구현 

```javascript

class Stack{
    
    constructor(array){
        this.array = array||[];
    }

    getBuffer = function(){
        return this.array.slice(); //shallow copy
    }

    isEmpty = function(){
        return this.array.length === 0;
    }

    push = function(item){
        return this.array.push(item);
    }

    pop = function(){
        return this.array.pop();
    }
    
    peek = function(){
        return this,array.peek();
    }
}


const myStack = new Stack();
        myStack.push(1);
        myStack.push(2);
        myStack.push(3);
        myStack.pop();   //3
        
        console.log(myStack) // [1,2]

```

