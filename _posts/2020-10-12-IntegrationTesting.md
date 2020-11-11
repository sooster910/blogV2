---
layout: post
title:  "Integration Testing with jest & puppeteer[1]"
description: 
tags: IntegrationTesting Jest puppeteer 
---
* puppeteer와 Jest를 공부하며 남긴 글입니다. 오타나 설명이 잘 못되었다거나 조금 더 보충설명이 필요하시다고 생각되시면 댓글 남겨 주세요 언제든지 환영입니다 😄

## 목차
 [1. npm run test 실행환경 구축 ](#npm-run-test-실행환경-구축)\
 [2. headless chromium 테스트 코드로 오픈하기](#headless-chromium-테스트-코드로-오픈하기)\
 [3. BeforeEach( ) ,afterEach( ) 이용하여 리팩토링](#dry)\
 [4. Jest test suite 작성](#test-suite-작성)



## npm run test 실행환경 구축 
Test를 할 환경을 구축하기 위해 필요한 것은, 테스트 할 파일이 필요하다. 

`/__test__` directory 생성한다.

`/__test__/header.test.js` 파일을 생성한다

package.json 에 이 snippet을 추가한다.
```javascript
 "scripts": {
        .
        .
        
    "test":"jest"
        .
        .
  }
```
 package.json 파일에 있는 scripts 는 npm으로 실행시키는 명령어를 개발자에 맞게 설정할 수 있는 부분이다. 즉 콘솔에서 명령어를 입력하면, script에서 이 명령어를 읽어들여 실행 시키게 된다. 
`npm run test` 를 입력하면 프로젝트 내부의 test.js 또는 spec.js 확장자를 가지는 파일을 실행한다. 

puppeteer 설치

```javascript
npm i puppeteer
# or "yarn add puppeteer"
```

## headless chromium 테스트 코드로 오픈하기 

virtural chromium을 실행하기위해선, pupeteer 이 필요하다. puppeteer는 다양한 function을 제공하는데 , 그중에 우리가 필요한 브라우저를 오픈하기 위한 함수도 포함되어있다.기본적으로 퍼페티어에선 launch() 가 browser객체를 리턴하고 있고, 또한  browser 객체를 이용해 우리는 또 다른 브라우저안에 브라우저 탭이라는 객체를 생성한다. 이 브라우저 탭 객체는 말그대로 크롬 브라우저의 각각의 탭을 의미하는데, 이 탭 객체를 이용하여, 다른 페이지로 이동 할 수도 있고, 요소들을 클릭하게 하는 테스트를 할 수 있다. 거의 모든 테스트들이 이 탭 객체과 상호작용하게 된다.



`/__test__/header.test.js`
``` javascript

const puppeteer = require('puppeteer');

test('We can launch a browser' ,async()=>{

    const browser = await puppeteer.launch({headless:true})

    const page = await browser.newPage();

})
```
`headless` option을 `false`로 지정해준다. 즉, `headless`가 `true`이면 user interface가 없는 브라우저를 오픈하고, false 일 경우 user interface가 있는 브라우저를 오픈한다. 
일단은, 브라우저가 오픈 여부에 대한 테스트를 위해, `headless: false` 로 지정한다.

puputeer는 asynchronous 기반이다. 그 이유는, 예를 들어 `browser` 의 경우 `puppeteer.launch()` 가 수행되는 시간이 언제인지 예측 할수가 없기 때문이다. 
`browser.newPage()` 도 새로운 tab을 열기 까지 수행되는 시간을 알 수 없으므로 비동기방식으로 실행된다. 

Test 실행

![image](/assets/puppeteer1.gif)


`npm run test` 후에, `header.test.js` 파일에서 1개의 테스트 코드를 실행하여 성공했다는 로그가 나오고, 
작업표시줄에 chromium 브라우저가 생성되는 것을 볼 수 있다. `{headless:false}` 의 결과로, 실제 브라우저가 launch 되었고, 앞으로의 테스트에는 `{headless:true}`로 쓸 것이다. 그 이유는, headless는 user interface가 없기 때문에 더 빠르다. 

## DRY

Brwoser를 열고 새로운 tab에 들어가, localhost:3000으로 이동하는 이 과정은 
앞으로 내가 작성할 모든 테스트에서 이뤄지는 중복되는 일이다. 
`beforeEach()` 와 `afterEach()`
가 중복을 해결하는데 도움이 된다.
`beforeEach(()=>{})` 는 테스트 파일의 각 테스트 코드가 돌기 전에 실행되는 Jest API로써, 반복되는 코드에 적합하다. 

`afterEach(()=>{})` 는 반대로 테스트 코드가 돌고 난 후에 실행되는 Jest  API이다. `beforeEach()` 에서 자동으로 브라우저를 오픈 했듯이, 테스트 코드를 돌고  난 후 브라우저를 자동으로 닫아주는 로직이 적합하다. 

이 두 API를 이용하여 코드를 리팩토링 할 수 있다.

 ```javascript

let browser,page;

beforeEach(async ()=>{
     browser = await puppeteer.launch({headless:false})
     page = await browser.newPage();
     await page.goto('localhost:3000'); //use await becuase navigate to somewhere take some amount of time
});

afterEach(async()=>{
    await browser.close();
})

 ```

## test suite 작성

테스트 내용 : 

browser를 띄웠을 때, nav element에 있는 브랜드 이름이 'BLOGGY'가 맞는지 확인. 

![image](/assets/test1.png)



```javascript
const puppeteer = require('puppeteer');

let browser,page;

beforeEach(async ()=>{
     browser = await puppeteer.launch({headless:false})
     page = await browser.newPage();
     await page.goto('localhost:3000'); //use await becuase navigate to somewhere take some amount of time
});
afterEach(async()=>{
    await browser.close();
})

test('Can get brand logo', async()=>{

    const text = await page.$eval('a.brand-logo',el=> el.innerHTML);
    expect(text).toMatch(/bloggy/gi);

});

```

puppeteer의 브라우저 인스턴스가 localhost:3000으로 들어가서 html element를 이용해서 `a.brand-logo` 를 찾아, 이 element의  innerHTML을 text라는 변수에 담아둔다.

`expect()`는 테스트할 대상을 넣는 api로, 여기서는 text라는 변수를 테스트 할 것이므로, `expect(text)`로 쓴다. 

테스트 할 대상을 알았으므로, 그 테스트의 예상이 어떻게 되어야 한다는 결과도출에 대한 method를 작성해야 한다.
우리의 테스트는, text 변수에 담긴 value 가 BLOGGY 와 일치한지 테스트 하는 것이므로, 일치한지에 대한 method를 사용한다. Jest 에서 `expect()`에 대한 다양한 method를 제공하는데, 이 예제의 경우에는 대문자, 소문자에 상관없이 테스트 하고 싶었기에 regex를 사용하게 된다. regex를 지원하는 method는 `.toMatch(rejexp)`가 있고, `/match할 letter/`뒤에 `gi` 가 붙으면, 대.소문자를 구분 하지 않는다는 정규표현식이다.  

`npm run test` 로 테스트를 하게 되면, chromium browser가 자동으로 오픈하여, 브랜드 이름을 찾는 테스트를 실행하고 테스트 결과를 log로 나타내고, 테스트가 끝나면 browser가 닫히게 된다. 

---
참고자료 :
* [puppeteer docs][puppeteer-docs]
* [Jest docs][jest-docs]
* [https://joshua1988.github.io/vue-camp/testing/jest-testing.html#jest-%EC%86%8C%EA%B0%9C](https://joshua1988.github.io/vue-camp/testing/jest-testing.html#jest-%EC%86%8C%EA%B0%9C){: target="_blank"}



[puppeteer-docs]: https://pptr.dev/
[jest-docs]:https://jestjs.io/docs/en/expect
