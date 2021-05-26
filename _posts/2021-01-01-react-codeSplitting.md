---
layout: post
title:  "React Code splitting 적용기"
tags: React
---
* 이 포스트는 회사의 리액트 프로젝트의 로딩 최적화 적용기를 유튜브의 codevolution의 mini project로 재구성 한 것입니다. 저의 경험으로 아직 부족한 점이 많습니다.😭 발전하는 개발자가 될 수 있도록 설명이 잘 못되었다거나 조금 더 보충설명이 필요하시다고 생각되시면 댓글 남겨 주세요. 언제든지 환영입니다 😄

## 목차
 [1.puppeteer + Jest](#puppeteerjest)\
 [2. npm run test 실행환경 구축 ](#npm-run-test-실행환경-구축)\
 [3. headless chromium 테스트 코드로 오픈하기](#headless-chromium-테스트-코드로-오픈하기)\
 [4. beforeEach( ), afterEach( ) 이용하여 리팩토링](#drydont-repeat-yourself)\
 [5. Jest test case 작성](#test-case-작성)


## Code Splitting 

서비스를 구성하는 리액트의 모든 컴포넌트들은 bundle.js라는 하나의 파일에 번들링된다. bundle.js 라는 파일은 브라우저 다운받게 되는데 이것은 사용자가 http 서버 요청을 하지 않고 원활하게 서비스를 사용할 수있게 한다. 하지만 앱의 규모가 커질수록 번들링 사이즈도 커지게 된다. 이렇게 되면 사용자측에선 초기 페이지 로딩속도가 느려지는 현상이 나타난다. 

해결책으론 모든것을 담고 있는 단일 파일을 더 작게 분할 하고 사용자가 페이지를 오픈했을 땐 필요한 최소한의 자바스크립트 코드만을 다운로드 할 수 있어야 한다.

고맙게도 웹팩은 분할 코드를 지원하는데 그 바탕에는 es06의 모듈 시스템이 있다. 


코드 분할을 하게 되면 최소한의 자바스크립트만 다운로드하면 된다 이것이 코드 분할의 힘이라고 볼 수 있다. 

코드 분할은 초기 로딩 속도를 단축시킬수 있는 성능최적화의 방법중 하나이다.  
1. Code Splitting
2. Lazy load routes with React.lazy()
3. React.Suspense to render a fallback UI 

## 초기 로딩 문제점 분석 및 번들링 파일 분석

```npm run build``` 를 하게 되면 번들링 된 파일 사이즈들을 스냅샷으로 보여준다.  

![image](/assets/2021-01-01-react-codeSplitting/build.png)
5개의 파일이 있는데 맨 밑의 파일부터 하나하나 살펴보자면  

```  584 B (+10 B) build/static/css/main.e7fd4024.chunk.css```  
어플리케이션에 필요한 모든 css 코드를 담고 있다.  

```1.06 KB (+503 B) build/static/js/main.ef7708cd.chunk.js```  
어플리케이션 필요한 js파일을 나타낸다. ex) app.js, home.js, profile.js etc

```1.18 KB (-32 B) build/static/js/runtime-main.af52a370.js```  
웹팩의 런타임 로직으로써 어플리케이션 로드와 실행부분을 나타낸다.

```1.57 KB (-1012.26 KB)  build/static/js/3.bd3d9297.chunk.js```  
```1.04 MB (+1013.09 KB) build/static/js/2.7992bff4.chunk.js```  
이 두 파일은 어플리케이션에 사용된 모든 라이브러리를 의미한다. 기본적으로는 node-modules가 포함 되어 있다.


---
### devtool 분석

이 어플리케이션은 5개의 파일로 나누어져 프로덕션 빌드가 최적화 되었습니다. 하지만 브라우저에서 페이지를 로드하면 분할된 다섯개의 chunk파일이 모두 로딩됩니다.


![image](/assets/2021-01-01-react-codeSplitting/before.png)

특히 1.1MB 를 차지하는 plotjs 차트 라이브러리를 담고있는 이 js파일은 dashboard page에 있는 차트를 위해 설치 된 것이나, 차트를 렌더링하고 있지 않은 home page에서도 받고 있으며 이것은 렌더링 시간을 지연시키는 결과를 낳습니다.   

프로덕션 모드에 있는 어플리케이션 성능을 좀 더 정확하게 측정하기 위해선 크롬의 incognito를 이용하는게 좋으며 크롬 익스텐션 기능들이 꺼지기 때문에 좀 더 명확한 상태로 측정할 수 있습니다. 

dev tool의 Performance탭에서  network를 Fast3G 상태로 전환합니다. Fast3G는 모바일 네트워크를 테스트하기 좋은 베이스라인입니다. 그 다음 Network탭에서 disable cache를 체크합니다. 기본적으로 크롬 브라우저는 웹페이지를 빨리 로딩하기 위하여 캐시를 합니다. 하지만 이렇게 테스트 하는 환경에서는 캐시로 돌아가는 모든 리소스를 가져와야 하기 때문에 수동으로 캐시를 unable시킵니다. 


![image](/assets/2021-01-01-react-codeSplitting/performance_before.png)


빨간색 박스 중 가장 아래쪽에는 frame을 시간별로 나누어서 보여줍니다. 첫 스크린샷이 있는데 이것은 페이지가 다시 reload되기 전이며, 그런 다음 흰색 배경의 화면을 꽤 오랜시간 동안 보여주고 다시 페이지가 렌더링 되는 것을 볼 수 있습니다. 여기서 분석할 수 있는 점은 노란색 바 가 있는 부분을 request하는 동안 흰색 배경의 화면이 지속되고 그 시간이 바로 로딩이 지연되는 것 임을 알 수 있습니다. 노란색 바는 ```2.7992bff4.chunk.js```파일로 위에서 언급한 라이브러리가 포함되어 있습니다. 그럼 이 라이브러리를 home 페이지에 로드를 하지 않으면 됩니다. 

## 해결 접근법 : React.lazy, Suspense 적용

가장 첫번째 페이지인 홈페이지 컴포넌트만 불러 오고 나머지 페이지는 유저가 해당 페이지로 이동할 때 비동기적으로 경로에 대한 코드를 fetch 합니다.
고맙게도 리액트는 lazy loading을 지원하여 Suspense로 각각의 컴포넌트를 핸들링하기 쉽게 해줍니다. 

React.lazy()사용 전 알아두어야 할 사항
```현재 React v17.0.2```

1. React.lazy 는 default exports만 지원한다. 

```javascript
export default function Profile(){
    return <h1>Profile</h1>
}
```
만약 named exports 를 사용하면, tree shaking이 동작하며 사용하지 않는 컴포넌트는 가져오지 않습니다. 
2. React.lazy와 Suspense는 서버사이드렌더링에선 지원되지 않고, Loadable Component를 추천한다고 합니다. 

<hr>

### React.lazy
 
```React.lazy()``` 함수는 다른 함수를 인자로 받을 수 있으며, 프로미스를 반환함으로써 그 함수가 동적 import를 수행하는 함수가된다.  

[React 공식문서](https://reactjs.org/docs/code-splitting.html)에서 제공한 기본 사용법 입니다..  

```React.lazy(()=>import('./OtherComponent'))```

Before:
```javascript
import OtherComponent from './OtherComponent';
```
After:
```javascript
const OtherComponent = React.lazy(() => import('./OtherComponent'));

```
이 방식을 그대로  profile 페이지에 적용해 보면, 

```javascript
//app.js

import logo from './logo.svg';
import './App.css';
import {BrowserRouter as Router, Route,Link} from 'react-router-dom'
import Home from './components/Home';
//기존 route
// import Profile from './components/Profile';
// import Dashboard from './components/Dashboard'; 

//lazy loading
const LazyProfile = React.lazy(()=>import('./components/Profile'));
const LazyDashboard = React.lazy(()=>import('./components/Dashboard'));

function App() {
  return (
    <Router>
      <div>
        <ul>
          <li><Link to='/'>Home</Link></li>
          <li><Link to ='/profile'>Profile</Link></li>
          <li><Link to='/dashboard'>Dashboard</Link></li>
        </ul>
      
        <hr />
        <Route exact path='/'>
          <Home />
        </Route>
        <Route path='/profile'>
          <LazyProfile />
        </Route>
        <Route path='/dashboard'>
          <LazyDashboard />
        </Route>
      </div>
    </Router>
  );
}

export default App;

```
### 개선된 사항 확인

다시 빌드를 합니다. 네트워크 탭에서 로드된 js파일을 확인해보면, 초기 페이지 로딩을 지연 시켰던 1.1MB 크기의 큰 파일이 로드되지 않았음을 알 수 있습니다. 
![image](/assets/2021-01-01-react-codeSplitting/after.png)

Performance에서 또한 라이브러리를 담고 있는 파일이 줄어든 것은 육안으로 확인할 수 있으며, 이 파일의 request 시간 또한   6.68s -> 863.78ms 으로 단축된 것을 알 수 있습니다. 물론 렌더링 시간, request 시간은 항상 일정한건 아닙니다. 하지만 최소한의 자바스크립트 파일만을 로드 할 수 있다는 사실을 확인했으므로, 렌더링 시간이 단축되는 것을 알 수 있습니다.

![image](/assets/2021-01-01-react-codeSplitting/performance_after.png)

### Suspense 
리액트 lazy loading 을 적용할 때, 그 컴포넌트가 실제 지연 로드 될 때 그 때의 상태처리를 고려해야 합니다. 리액트에서 제공하는 Suspense를 사용하는데 지연로딩되는 컴포넌트를 wrap해주면된다.   componenet 지정을 하지 않으면 이런 에러가 발생하는데,
[image]
fallback UI가 지정되지 않았다는 의미로 

```javascript
function App() {
  return (
    <Router>
      <div>
        <ul>
          <li><Link to='/'>Home</Link></li>
          <li><Link to ='/profile'>Profile</Link></li>
          <li><Link to='/dashboard'>Dashboard</Link></li>
        </ul>
      
        <hr />
        {/*suspense 🔥*/}
        <React.Suspense fallback="Loading..."> 
          <Route exact path='/'>
            <Home />
          </Route>
          <Route path='/profile'>
            <LazyProfile />
          </Route>
          <Route path='/dashboard'>
            <LazyDashboard />
          </Route>
        </React.Suspense>
      </div>
    </Router>
  );
```


<!-- 
[React 공식문서](https://reactjs.org/docs/code-splitting.html)에서 코드스플릿으로 가장 좋은 방법은 동적 import 문법을 사용하는 것이라 명시되어있다. 기본 문법으론 

```javascript
import("./math").then(math => {
  console.log(math.add(16, 26));
});
```
이 코드는  -->
 resolves with module once the module complely loaded.
 
 2가지 방법
 1. 라우팅 레벨
 2. 컴포넌트 레벨

 