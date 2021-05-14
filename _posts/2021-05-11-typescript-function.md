---
layout: post
title:  "[TypeScript] 2.Function"
tags: TypeScript TIL
---
* TypeScript 노트 들은 제가 정진욱님의 타입스크립트 퀵스타트, 현재 최신 버전(4.0)의 타입스크립트 공식문서 그리고 다른 자료들을 바탕 으로 혼자 공부한 것들을 정리한 것입니다. 😄

## 목차
 [1. 자바스크립트에서의 함수](#1-자바스크립트에서의-함수)\
 [2. 타입스크립트에서의 함수  ](#2-타입스크립트에서의-함수 )\
&nbsp;&nbsp;&nbsp;&nbsp;[2.1. 어디에 타입을 추가하나?](#21-어디에-타입을-추가하나)\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[2.1.1 매개변수 타입 종류](#211-매개변수-타입-종류)\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[ 1. default parameter ](#1-default-parameter )\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[ 2. optional parameter ](#2-optional-parameter)\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[ 2. rest parameter ](#3-rest-parameter)\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[2.2 익명 함수](#22-익명-함수)
 
# 함수에 대한 타입지정(Function) 

타입스크립트에서는 함수의 매개변수와 리턴값에 대해 타입을 지정한다. 자바스크립트와 타입스크립트를 비교하여 타입스크립트에서 타입지정이 왜 조금더 안전성이 있는지 이해할 수 있다. 

## 1. 자바스크립트에서의 함수

```typescript
const totalNum=(num)=>{
     return 100+num;
}
console.log(totalNum("50")); //10050 expected result: 150

```
위의 코드를 실행하면 런타임에 문제가 없다. 하지만 ```num``` 이 문자로 인식하여 반환값이 두 수의 합이 아닌 문자간의 결합이 되었다. 실제 사용자가 사용하는 서비스 가령 계산을 하는 로직이라면 이런 일은 없어야 할 것이다. 그래서 자바스크립트에선 타입 검증코드를 항상 추가한다. 

```typescript
const totalNum=(num)=>{
    if(typeof num !=="number") num=Number(num)
     return 100+Number(num);
}
console.log(Number(totalNum("50"))); //150

```
이런 함수를 작성할 때마다 타입체킹 코드를 추가하는 것은,  
1. 반복적인 작업을 요구  
2. 강제적으로 타입캐스팅 ->불필요한 자원을 소비->성능저하  

## 2. 타입스크립트에서의 함수

위에서 언급한 자바스크립트의 타입을 쓰지 않는 점에 초래하는 문제점에 비해 타입스크립트는 함수에 타입을 지정함으로써 지정하지 않은 타입은 compile error 를 미리 알려주어 안전성을 강화하며 간결한 코드를 만들 수 있다.

### **2.1 어디에 타입을 추가하나?**
1️⃣ 매개변수 타입 (Parameter Type Annotations)  
2️⃣ 반환 타입(Return Type Annotations)  
3️⃣ 매개변수+반환  

#### **매개변수 타입**

```typescript
// Parameter type annotation
function greet(firstName: string) {
    console.log(`Hello, ${firstName.toUpperCase()}!!`);
}
greet("sue") //Hello, SUE!! ✅ compiled!
greet(42) // ❌ compiled error! 

```
#### **반환 타입**

```typescript
function greet():string {
    return `Hi, all!!`;
}
console.log(greet()); //Hi, all!!
   ```

#### **매개변수+반환**  

```typescript
function greet(firstName: string):string {
    return `Hello, ${firstName.toUpperCase()}!!`);
}
console.log(greet("sue")) //Hello, SUE!! ✅ compiled!
```

#### **2.1.1 매개변수 타입 종류**
함수 입장에서 아마도 이렇게 구분 할 수 있을 것 같다.  

* 인자가 전달되지 않을 때(생략가능) default값을 두는 경우, default parameter.  
* 인자가 전달 되지 않는 경우(생략 가능), optional parameter.  
* 인자가 여러개이고 순서가 중요하지 않은 경우, rest parameter.  

#### **1. default parameter**   
자바스크립트의 ES06에서 사용하듯이 타입스크립트 에서도 매개변수에 default값을 지정 할 수 있다.   

```typescript

const totalNum=(num:number=0)=>{
    return 100+num;
  }
  console.log(totalNum())// ✅ compiled!

```
❌ compile error
```typescript

const totalNum=(num:number)=>{
    return 100+num;
  }
  console.log(totalNum())// ❌ Expected 1 arguments, but got 0.ts(2554)
```
#### 2. **optional parameter**  
default parameter와 비슷하게 인자를 생략할 수 있다. 다른 점은 default parameter는 결국 함수에서 기본 값을 가지고 있었기에 에러가 나지 않았지만, optional parameter는 기본값을 초기화하지 않아도 생략이 가능하다.

```typescript

const totalNum=(num:number):number=>{
     return 100+num;
}

console.log(totalNum())// ✅ NaN

```

에러는 나지 않았지만 결과값이 NaN 으로 num이 초기화되지 않았기에 나온 리턴값이다. 만약 이런 상황을 보완하고 싶으면, 앞서 언급한 default로 초기화를 하거나, 따로 if 구문을 써도 가능하다.   

```typescript

const totalNum=(num?:number):number=>{
    if(num === undefined) num=0;
    return 100+num;
}

console.log(totalNum())// ✅ 100

```
#### 3. **rest parameter**  
[ES06의 rest parameters/Destructuring rest parameters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)와 동일하게 쓰인다.여러개의 매개변수들을 배열화 하여 처리하며 타입지정시 주의할 점은 타입 뒤에 ```[]``` 을 넣어 배열화 처리를 명시한다. 

```typescript

function multiply(n: number, ...m: number[]) {
    console.log(n) //10
    return m.map((x) => n * x);
    }
    const a = multiply(10, 1, 2, 3, 4);
    console.log(a) //[10, 20, 30, 40]

```

## 2.2 익명 함수

### 자바스크립트 즉시 실행 함수의 예 (Immediately Invoked Function) 

```typescript
  //IIF 
  (()=>console.log("fired instant func🔥"))(); // ✅ fired instant func🔥
  
  //return 값이 있는 경우
  const instantFuncVal = (str=>`${str}🔥`)("fired instant func");

  console.log(instantFuncVal); // ✅ fired instant func🔥
```

### 타입스크립트 즉시 실행 함수의 예 (Immediately Invoked Function) 

```typescript
    ((str?:string)=>`${str}🔥`)("fired instant func"); //✅ fired instant func🔥

    const instantFuncVal = ((str?:string)=>`${str}🔥`);
    console.log(instantFuncVal("fired instant func")); // ✅ fired instant func🔥

```

### **익명함수의 타입을 함수의 타입으로 분리**

익명함수에 타입을 따로 분리하여 함수 타입을 ```type``` alias를 이용해 선언한다.  
장점 :  
1. 가독성이 좋아진다.  
2. 재사용성  
3. 함수타입을 지정하는것 만으로도 타입의 안정성이 보장된다.  

```typescript

let addNums =(num1:number, num2:number):number=>num1+num2; //😑 가독성 ↓

========================================================
// 함수타입
type calcType =(num1:number, num2:number)=>number;

let addNums = calType= (num1,num2)=>num1+num2;
let subtractNums= calType= (num1,num2)=>num1-num2;

```

이 외에도 함수 파트에선 다룰 이야기들이 더 있다. 객체에서의 함수와 콜백함수는 interface와 tsconfig를 더 공부한 후에 정리한 내용을 포스팅 해야 할 것 같다. 

참고자료

* [TypeScript 4.0 공식문서](https://www.typescriptlang.org/docs/){: target="_blank"}
* [도서 - 정진욱, 타입스크립트 퀵스타트, 루비페이퍼 ]