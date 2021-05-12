---
layout: post
title:  "[TypeScript] 1.Type"
tags: TypeScript TIL
---
* TypeScript 노트 들은 제가 정진욱님의 타입스크립트 퀵스타트와 현재 최신 버전(4.0)의 타입스크립트 공식문서를 바탕으로 혼자 공부한 것들을 정리한 것입니다. 😄

# Type

1️⃣ &nbsp; JavaScript built-in types(Primitive Type)  
&nbsp; &nbsp; &nbsp; - number  
&nbsp; &nbsp; &nbsp; - string  
&nbsp; &nbsp; &nbsp; - bigint  
&nbsp; &nbsp; &nbsp; - boolean  
&nbsp; &nbsp; &nbsp; - symbol  
&nbsp; &nbsp; &nbsp; - null  
&nbsp; &nbsp; &nbsp; - undefined  
&nbsp; &nbsp; &nbsp; - object  

2️⃣ &nbsp; TypeScript built-in types  
&nbsp; &nbsp; &nbsp; - unknown  
&nbsp; &nbsp; &nbsp; - never  
&nbsp; &nbsp; &nbsp; - void  

3️⃣ 기타 타입[유니온(union), 인터섹션(intersection),특수타입] 

## 기본 타입 정리(Primitive Type) 

```typescript

const num:number = -6;
const str:string = "hello";
const bool : boolean = false;
 
```
Undefined 의 경우, 만약 변수에 자료형을 undefined으로 지정한다면, 그 후 어떤 값을 그 변수에 넣어도 에러가 난다. 그 이유는 undefined 자료형은 값을 할당하지 않는 것인데, 값을 할당했기 때문이다.

```javascript

let name: undefined;
name = 'hello'
console.log(name) // Err
```

해결 : optional type 이용하기 👍
```javascript

let name : string | undefined;
name = undefined;
console.log(name) //undefined
name = 1 
console.log(name) //1
```

### **null**
```typescript

  let person :null;
  person = 1 //Type '1' is not assignable to type 'null'.

  let person : number | null;
  person=1;
  console.log(person); //1
  person=null;
  console.log(person); //person

```

### **unknown (TypeScript 내장타입)**

```typescript

let lunchMenu:unknown='burger';
lunchMenu =0;
lunchMenu='he';
lunchMenu = true;
```
💢 모든 데이터 타입을 지정할 수 있다. 데이터 타입에 엄격한 타입스크립트 그 자체의 목적성과 멀어지는 type 이다. 그럼 왜 이런 타입이 있는건가?  

타입스크립트는 타입에 유연한 자바스크립트와 연동해서 쓰이는 부분에서 좀 더 유동성을 갖기 위해서이다. 예를 들어 만약 타입스크립트 프로젝트에 자바스크립트 라이브러리를 사용하게 된다면 자바스크립트 함수로부터 return 하는 값에 대한 타입을 모르기 때문에 ```unknown``` 을 사용할 수 있다.

### **any (TypeScript 내장타입)**
1️⃣  any 타입은 모든 타입의 가장 최상위 타입으로 간주되며, 자바스크립트의 모든 값(=모든 타입)을 할당 받을 수 있다.  

```typescript
let dinnerMenu:any='burger';
dinnerMenu =0;
dinnerMenu='he';
dinnerMenu = true;

```
2️⃣  any 타입으로 선언된 변수는 최소한의 정적 타입 검사만 하므로, 선언되어 있지 않은 속성에 접근하더라도 컴파일 오류가 발생하지 않는다. 

```typescript

let mikeAge=30;
let myAge:any=30;

//내장함수 이용 
mikeAge.toFixed(2); //30.00
myAge.toFixed(2); //30.00 

//내장함수가 아닌 존재하지 않는 함수이용
mikeAge.anyFunc();
myAge.anyFunc(); 
```
>
Don’t use **any** as a type unless you are in the process of migrating a JavaScript project to TypeScript.   
-생략-  
In cases where you don’t know what type you want to accept, or when you want to accept anything because you will be blindly passing it through without interacting with it, you can use unknown.  
Last updated: May 10, 2021  
출처 :[TypeScript 공식문서 Declaration Files/Do's and Don'ts](https://www.typescriptlang.org/docs/handbook/declaration-files/do-s-and-don-ts.html#any)
>
무분별하게 사용하지 말아야 겠다.

### **void (TypeScript 내장타입)**

아무것도 리턴하지 않는 함수에 써 준다, 
```typescript
function close():void{
    console.log('close');
}

```

### **never (TypeScript 내장타입)**

void와 마찬가지로 함수가 리턴할 계획이 없을 때 명시적으로 never을 써준다. custom한 unexpected 에러를 잡는 함수이거나 try, catch 구문에서 에러를 client로 보내는 일이 전부일 경우.

```typescript
function throwErr(msg:string):never{

    return 
    //Type 'undefined' is not assignable to type 'never'
}

```
return만 명시되어 있어도 Error가 뜬다. 

### Object

원시타입을 제외한 모든 타입 할당 가능.

```typescript
  let obj:object=["obj"];
  function func(obj:object){
    console.log(obj)
  }
  func(obj); //["obj"]
  func({fruit:"apple"});//{fruit:"apple"}
  func(["tomato","1"]); //["tomato","1"]
  func("string");//error TS2345: Argument of type 'string' is not assignable to parameter of type 'object'.

```
Object타입은 유연하게 타입의 할당이 가능하다는 점에서 any 타입과 유사하지만, 타입에 속하는 프로퍼티를 검사하는 시점이 다르다. 

|   |any|object|
|----|---|---|
| 시점  | 런타임  | 컴파일  |
| 코드 | num.toFixed(2) //✅ compiled! | num.toFixed(2) // ❌ compile err!  |

```typescript
let num :objecet =10;
num.toFixed(2);  //❌ Property 'toFixed' does not exist on type 'object'.

```
compile 에러가 나는 이유는 toFixed()는 Number객체에만 포함된 프로퍼티중 toFixed라는 메서드이다. 컴파일 시, 컴파일러는 object라는 타입으로 입력되고 숫자 타입인지는 모르기 때문에, 에러를 낸다.  


---  
참고자료

* [TypeScript 4.0 공식문서](https://www.typescriptlang.org/docs/){: target="_blank"}
* [도서 - 정진욱, 타입스크립트 퀵스타트, 루비페이퍼 ]
