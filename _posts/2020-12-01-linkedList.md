---
layout: post
title:  "Integration Testing with jest & puppeteer[1]"
description: 
tags: DataStructure/Algorithm JS  
---
* LinkedList 를 공부하며 남긴 글입니다. 오타가 있거나 설명이 잘못 되었다거나 조금 더 보충설명이 필요하시다고 생각되시면 댓글 남겨 주세요. 언제든지 환영입니다 😄

## 목차
 [1.puppeteer + Jest](#puppeteerjest)\
 [2. npm run test 실행환경 구축 ](#npm-run-test-실행환경-구축)\
 [3. headless chromium 테스트 코드로 오픈하기](#headless-chromium-테스트-코드로-오픈하기)\
 [4. beforeEach( ), afterEach( ) 이용하여 리팩토링](#drydont-repeat-yourself)\
 [5. Jest test case 작성](#test-case-작성)

## What is Singly Linked List?
 직역으로 서로 연결된 리스트이다. 주로 배열과 함께 비교가 된다. 그 이유는 배열도 자료를 저장하는 구조가 되고, 연결 리스트도 또한 자료를 저장하기 위해서 만든 자료구조에 해당한다. 이 둘의 장단점을 이용해 더 효율적으로 코딩을 할 수 있을 것이다.  
 배열 : 연속적인! 같은 자료형!들의 집합!이다.  
 장점: 1. 연속적이기 때문에, 데이터의 위치를 알기가 쉽다.  
 단점 : 메모리를 한꺼번에 만들어야 한기 때문에, 메모리 공간 낭비가 크다.  
 이러한 단점을 보완한 것이 Singly Linked List 이다.
 linked list 는 실행시간에 필요할 때 마다 동적으로 메모리를 생성 한다.(동적메모리로 만들어진다) 그럼 필요 할때 마다 만들어진 메모리들이 어떤 위치에 저장되는지 어떻게 알 수 있으며, 첫번째로 만들어진 메모리인지 두번째로 만들어진 메모리인지 어떻게 알 수 있을까?
 배열의 경우 시작점만 알면 그 나머지의 element 들을 쉽게 접근할 수 있다. 이런 데이터를 연결하기 위해 첫번째 데이터가 저장된 곳에, 추가적으로 두번째 데이터의 정보가 저장되어 있어야 한다.  
 

무엇을 연결? 노드들을 연결한다.  
얼마나? 제한이 없다.  
노드가 뭔가요?

head, tail , length property를 가지고 있다.
Linked List 는 노드로 이루어져있고, 이 각각의 노드들은 value그 노드의 값과 다음 노드를 가르키는 pointer 로 이루어져 있다.
Linked List 는 index 가 없다. 이 말은 즉, 5번 째 element의 값을 달라고 요구할 수 없다. Linked Lists는 Array와는 또 다른 특징을 가진 다른것임을 유추 할 수 있다. 
