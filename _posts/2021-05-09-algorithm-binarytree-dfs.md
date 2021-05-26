---
layout: post
title:  "[DataStructure/Algorithm] 이진트리순회(DFS)"
tags: JavaScript DataStructure/Algorithm DFS 
---
* 오타나 설명이 잘 못되었다거나 조금 더 보충설명이 필요하시다고 생각되시면 댓글 남겨 주세요 언제든지 환영입니다 😄

# 이진 트리 순회 - DFS

이진트리를 이해하기전 먼저 트리에 대해서 큰 그림을 보자면,  
트리는 만약 각 노드가 m 개 이하의 자식을 갖고 있으면  m-ary트리 즉 다항트리 다진 트리라고 정의 된다.
## 트리의 종류 그리고 이진트리

### **종류**
1️⃣  이진트리(Binary Tree) -  **자식노드를 최대 두개**를 갖는 구조이다 즉, 노드의 차수가 2 이하일 때  
&nbsp; &nbsp; 🔸 이진트리의 유형 : Full Binary Tree, Complete Binary Tree, Perfect Binary Tree.  
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 1). Full Binary Tree(정 이진 트리 ) - 각 노드의 차수가 0 개이거나 2개인 경우에 해당.    
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 2). Complete Binary (완전 이진 트리) - Full Binary Tree 이거나 leaf 노드의 경우 왼쪽 노드부터 채워져 있는 구조.  
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 3). Perfect Binary Tree (포화이진 트리 )- 모든 리프노드가 동일한 깊이/레벨.  
&nbsp; &nbsp; 🔸 이진트리 순회  
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 1). 중위순회  (in order traversal)
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 2). 전위순회 (preorder traversal)  
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 3). 후위순회  

2️⃣  이진탐색트리  
3️⃣  균형트리(AVL트리, RedBlack)  
4️⃣  이진힙 (최대힙, 최소힙)

여기서 다룰 이야기는 이진트리 순회이며, 순회하는 방식엔 중위순회, 전위순회, 후위순회가 있다.

![image](/assets/Binary-tree-traversal.png)

부모가 출력되는 기준에 따라 나눈다.  
전위순회(부모가 제일 먼저) 1-2-4-5-3-6-7  
중위순회(부모가 중간에) 4-2-5-1-6-7-3  
후위순회(부모가 제일 나중에) 4-5-2-6-7-3-1  


### **구현**
#### 1. 전위순회
```javascript
function solution(target){
    dfs(1);
    
    function dfs(n){
        //7포함
        if (target<n){
            return;
        }else{
            console.log(n);
            dfs(n*2);
            dfs(n*2+1);
        }
    }
}

solution(7); //1-2-4-5-3-6-7
```

#### 2. 중위순회
```javascript
function solution(target){
    dfs(1);
    
    function dfs(n){
        //7포함
        if (target<n){
            return;
        }else{
            
            dfs(n*2);
            console.log(n);
            dfs(n*2+1);
        }
    }
}

solution(7);
```

#### 3. 후위순회

```javascript
function solution(target){
    dfs(1);
    
    function dfs(n){
        //7포함
        if (target<n){
            return;
        }else{
            
            dfs(n*2);
            console.log(n);
            dfs(n*2+1);
        }
    }
}

solution(7);

```

---  
참고자료

* [인프런 자바스크립트 알고리즘 문제풀이](https://www.inflearn.com/course/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EB%AC%B8%EC%A0%9C%ED%92%80%EC%9D%B4){: target="_blank"}
* [도서 - 박상길, 2020, 파이썬 알고리즘 인터뷰, 책만 ]
