---
layout: post
title:  "[DataStructure/Algorithm] Two Sum - LeetCode"
description: 
tags: DataStructure/Algorithm JS
---

### 문제
Given an array of integers, return indices of the two numbers such that they add up to a specific target.
You may assume that each input would have exactly one solution, and you may not use the same element twice.
You can return the answer in any order.
```

Input: nums = [2,7,11,15], target = 9

Output: [0,1]

Output: Because nums[0] + nums[1] == 9, we return [0, 1].

```
### 어떤 식으로 이 문제를 접근할 것인가
  
edge case :  
1.  target이 주어진 배열에서 찾을 수 없는 경우 -> return null 
2.  주어진 배열이 empty 일 경우. -> return null
3. 배열의 요소가 하나인데, 그 요소가 target일 경우 -> return null


### 1. Brute Force — O(n²)
\
생각한 첫번째 방법은 모든 수를 대입하여 9가 나오는지 확인하는 방식이다.\
for loop에서 i=0 일 때, j loop을 한번 씩 더 돌게 하여, array안에 있는 숫자들을 다 더해 보게 된다.\
그림으로 나타내면 이렇다.

![image](/assets/twosome.jpg)


 ```javascript
    const getTwoSum = (arr, target) => {
    let res = [];

        for (let i = 0; i < arr.length; i++) {
            for (let j = i + 1; j < arr.length; j++) {
                if (arr[i] + arr[j] === target) {
                    res.push(i);
                    res.push(j);
                }
            }
        }
        return res;
    }
    console.log(getTwoSum([2, 7, 11, 15], 9));
    // => Output [ 0, 1 ]

 ```


### 2. A better time complexity solution with Hash Map- O(n) 
일단 nested loop을 사용하지 않는 방법을 생각했다.   
첫번 째 인덱스의 값인 (arr[0]), 2 부터 시작하여 매칭하는 값과 더하여 9가 나오는 과정을 생각하니, lookup을 하게 되므로 Map객체를 이용하는게 효율적이라 한다.


![image](/assets/twosum2.jpg)
loop을 시작했을 때, 
arr[0] = 2 이고 , 2+x=9 ,  x =7 이다.\
Map 객체에 x 의 값인 7 이 있는지 확인한다. 만약 7이 없다면 Map에 넣어주고 (`Map.set()`), 7이 있다면 
 `[현재 인덱스, 7에 대응 하는 value 값]` 을 리턴한다.
 
 

```javascript




const getTwoSum = (arr, target) => {
    const map = new Map();

    for(let i =0; i<arr.length; i++){
        let currentValue = arr[i];

        if(map.has(currentValue)){
            return[ map.get(currentValue), i];
        }
        let diff = target- currentValue;
        map.set(diff,i);

    }

}

getTwoSum([2, 7, 11, 15], 9) //[0,1]
```


### 3. Another solution using Two Pointer

포인터 두개를 나란히 [0],[1] 인덱스에 initialize 를 한다음, 인덱스 [0] 를 기점으로 target -[0] 의 값을 나머지 인덱스들 중에 찾는다. 