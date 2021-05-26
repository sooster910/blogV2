---
layout: post
title:  "<React hook series> render -1 "
description: 
tags: React hook TIL 
---
* puppeteer와 Jest를 공부하며 남긴 글입니다. 오타나 설명이 잘 못되었다거나 조금 더 보충설명이 필요하시다고 생각되시면 댓글 남겨 주세요 언제든지 환영입니다 😄

## 목차
 [1.puppeteer + Jest](#puppeteerjest)\
 [2. npm run test 실행환경 구축 ](#npm-run-test-실행환경-구축)\
 [3. headless chromium 테스트 코드로 오픈하기](#headless-chromium-테스트-코드로-오픈하기)\
 [4. beforeEach( ), afterEach( ) 이용하여 리팩토링](#drydont-repeat-yourself)\
 [5. Jest test case 작성](#test-case-작성)

## useState()
setter 함수에서 만약 값이 초기값과 같다면 rerender 하지 않는다. 

```
import React,{useState} from 'react'

export const UseState = () => {
    const [count,setCount] =useState(0)

    console.log('useState Render')
    return (
        <div>
            <button onClick={()=>setCount(c=>c+1)}>count = {count}</button>
            <button onClick={()=>setCount(0)}>count to 0 </button>
            <button onClick={()=>setCount(5)}>count to 5</button>

        </div>
    )
}

```

1. 