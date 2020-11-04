---
layout: post
title:  "[JS DataStructure] Stack"
description:  Stack이 뭔가요? 
tags: JS DataStructure Stack 
---

## Stack 

![image](/assets/stack.png)

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

