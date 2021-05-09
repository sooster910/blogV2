---
layout: post
title:  "Array.prototype.sort()"
description:
tags: JavaScript
---
* 오타나 설명이 잘 못되었다거나 조금 더 보충설명이 필요하시다고 생각되시면 댓글 남겨 주세요 언제든지 환영입니다 😄

## sort() 

 javaScript의 sort()는 빌트인 함수이며, 매개변수로 함수를 받을 수도 있다. (optional)  
 ```arr.sort([compareFunction])```

## 문자열 정렬
```javascript
const months = ['March', 'Jan', 'Feb', 'Dec'];
months.sort();
console.log(months);
// expected output: Array ["Dec", "Feb", "Jan", "March"]
```

## 숫자 정렬 
```javascript
let numbers = [0, 1 ,2, 3, 10, 20, 30 ];
numbers.sort();
console.log(numbers); //[ 0, 1, 10, 2, 20, 3, 30 ]

```
생각한 결과와는 다르다. 😨 mdn에서 sort() 문서를 보면

>
**```compareFunction``` Optional **\
Specifies a function that defines the sort order. If omitted, the array elements are converted to strings, then sorted according to each character's Unicode code point value.

>

만약 매개변수인 ```compareFunction``` 을 생략 한다면, 배열에 있는 요소를 문자열로 변환한다음 문자열에 대응되는 UTF-16 유니코드에 따른 값으로 정렬을 한다고 되어있다.

여담으로 왜 UTF-16인지 궁금해서 찾아본결과, 
브라우저에서 자바스크립트가 작동하는 절차와 관련이 있었다. 작성한 .js파일은 UTF-8으로 저장되고, 브라우저로부터 .js 파일을 불러 올 때는 UTF-8 기준으로 디코딩하고 코드포인트(문자에 부여한 고유한 숫자)를 해석하며, 자바스크립트 엔진은 해석된 값 코드포인트 UTF16으로 인코딩하여 메모리에 올린다.
UTF-16이 더 궁금하다면, [UTF-16에 대한 자세한 설명](https://www.bsidesoft.com/3526#:~:text=UTF8%EC%9D%80%20%EC%BD%94%EB%93%9C%EC%9C%A0%EB%8B%9B%EC%9D%B4,%EC%9C%A0%EB%8B%9B%EC%9D%84%20%EA%B0%80%EB%A6%AC%ED%82%A4%EB%8A%94%20%EA%B2%83%EC%9E%85%EB%8B%88%EB%8B%A4.){: target="_blank"}

그래서 숫자로 인식하여 정렬하기 위해선 ```compareFunction```을 사용한다.  
2개의 매개변수를 받으며 여기서 return 하게 되는 방식 (반환값)이 정렬 하는 방식을 정한다.

```javascript
numbers.sort(compareFunction); // [0, 1 ,2, 3, 10, 20, 30 ]

function compareFunction(a,b){
    if(a > b) return 1;
    if(a < b) return -1;
    return 0;
    
}

```

위의 코드를 보면, sort함수에 compareFunction이라는 callback을 사용한다. 콜백(compareFunction)의 첫번째 인덱스와, 두번째 인덱스가 각각 a,b 가 될 것이며
compareFunction에 있는 조건문들이 default로 정해져 있는 방식으로 어떻게 정렬이 되는지를 결정하는것이다.  
중요한 점은 return이 -1 일 때만 정렬이 된다(내부적으로 원소의 위치가 바뀜). 매개 변수인 a,b가 어떻게 되는지 보기 위한 과정을 봐보았다. 
```javascript
let numbers = [3,2,1];
numbers.sort(compareFunction); // [1,2,3] 

function compareFunction(a,b){
    console.log(`a:${a}, b:${b}`)

    if(a > b){
        console.log(`a>b: ${a}>${b}`)
        return 1;
    }
    if(a < b){
        console.log(`a<b: ${a}<${b}`)
        return -1;
    }
    return 0;

}
```
![image](/assets/sort.png)

callback 함수 ```compareFunction``` 는 매개변수 a,b를 받고 a= 두번째원소, b= 첫번째원소로 지정된다. 여기서 return 값의 의미는  -, + ,0 의미를 갖고 있다. console에 찍힌 비교문 하나씩 보면, 
a가 b보다 더 크면 1 을 return 하는데, 이 의미는 a-b를 할 경우 값은 양수이다라고 볼 수도 있다. 그래서 양수인 1을 return 한다. 그럼 만약 b가 a 보다 크다는 것은 a-b를 했을 때 - (음수)의 결과를 갖는다고도 볼 수 있다. 그래서 -1을 return 한다. 이럴경우는 a=2, b=3서로 swap한다. 만약 a,b가 같다면 return 0으로 바뀌지 않는다.   
그렇다면 이런 이론을 전제로 더 심플하게 표현할 수도 있다.

1. 오름차순 a-b
```javascript
numbers.sort(function(a, b) {
  return a - b;
});
```

2.내림차순 b-a
```javascript
numbers.sort(function(a, b) {
  return b - a;
});
```

3. ES2015를 사용한 오름차순
```javascript
let numbers = [4, 2, 5, 1, 3];
numbers.sort((a, b) => a - b);
console.log(numbers); //[1,2,3,4,5]
```

4. ES2015를 사용한 내림차순
```javascript
let numbers = [4, 2, 5, 1, 3];
numbers.sort((a, b) => b-a);
console.log(numbers); //[5,4,3,2,1]
```

## 객체 정렬
```javascript
var items = [
 { name: '이', age: 21 },
 { name: '박', age: 37 },
 { name: '강', age: 45 },
 { name: '우', age: 12 },
 { name: '서', age: 13 },
 { name: '장', age: 37 }
];


```

#### 나이순서 (오름차순)
```javascript
// 나이별로 정렬 a.age - b.age -> negative 
items.sort(function (a, b) {
  return a.age - b.age;
});

//ES06
items.sort((a, b)=> a.age - b.age);

//or
items.sort((a,b)=> a<b ? -1 :a>b ? 1:0)

/*
[
  { name: '우', age: 12 },
  { name: '서', age: 13 },
  { name: '이', age: 21 },
  { name: '박', age: 37 },
  { name: '장', age: 37 },
  { name: '강', age: 45 }
]


* */
```

#### 나이순서 (내림차순)
```javascript

items.sort(function (a, b) {
  return b.age - a.age;
});
 
/*
* [
  { name: '강', age: 45 },
  { name: '박', age: 37 },
  { name: '장', age: 37 },
  { name: '이', age: 21 },
  { name: '서', age: 13 },
  { name: '우', age: 12 }
]

* */
```
#### 이름순서 (오름차순)

```javascript
items.sort(function (a, b) {
 return a.name - b.name;
});


//다른 표현식
items.sort((a,b)=> a.name<b.name ? -1 : a.name>b.name ? 1:0)

/**
 [
 { name: '강', age: 45 },
 { name: '박', age: 37 },
 { name: '서', age: 13 },
 { name: '우', age: 12 },
 { name: '이', age: 21 },
 { name: '장', age: 37 }
 ] 
 */
```

#### 이름순서 (내림차순)

```javascript
items.sort(function (a, b) {
 return a.name - b.name;
});
//or
items.sort((a,b)=>b.name-a.name)

//or
items.sort((a,b)=> a.name<b.name ? 1 : a.name>b.name ? -1:0)

/**
 [
 { name: '장', age: 37 },
 { name: '이', age: 21 },
 { name: '우', age: 12 },
 { name: '서', age: 13 },
 { name: '박', age: 37 },
 { name: '강', age: 45 }
 ]

 */
```

## sort()는 원본 객체를 mutating 한다. 
위에 썼던 모든 방식들은 원복객체도 같이 정렬한 객체로 바뀐다.

```javascript
var items = [
 { name: '이', age: 21 },
 { name: '박', age: 37 },
 { name: '강', age: 45 },
 { name: '우', age: 12 },
 { name: '서', age: 13 },
 { name: '장', age: 37 }
];

const sortedArr=items.sort(function (a, b) {
 return b.age - a.age;
});

console.log(items)

console.log(sortedArr)

/*
* [
  { name: '강', age: 45 },
  { name: '박', age: 37 },
  { name: '장', age: 37 },
  { name: '이', age: 21 },
  { name: '서', age: 13 },
  { name: '우', age: 12 }
]
[
  { name: '강', age: 45 },
  { name: '박', age: 37 },
  { name: '장', age: 37 },
  { name: '이', age: 21 },
  { name: '서', age: 13 },
  { name: '우', age: 12 }
]
 
* */
```


### 원본을 유지하면서 정렬하는 방법 

#### 1. spread-syntax 를 사용하여 copy 한 후 sort
 ```javascript
const sorted = [...items].sort(items.sort((a,b)=>a.name-b.name));
console.log(sorted)


```  
#### 2. array-like object

```javascript
const sorted = Array.prototype.slice.call(items).sort((a,b)=>a.age -b.age);
console.log(items)
console.log(sorted)

/*
* [
  { name: '이', age: 21 },
  { name: '박', age: 37 },
  { name: '강', age: 45 },
  { name: '우', age: 12 },
  { name: '서', age: 13 },
  { name: '장', age: 37 }
]
[
  { name: '우', age: 12 },
  { name: '서', age: 13 },
  { name: '이', age: 21 },
  { name: '박', age: 37 },
  { name: '장', age: 37 },
  { name: '강', age: 45 }
]
* */
```


이것으로 sort() 에 대한 정리를 마친다. 

참고자료 :

* [mdn Array.prototype.sort()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort){: target="_blank"}
* [javascripttutorial.net](https://www.javascripttutorial.net/javascript-array-sort/){: target="_blank"}


