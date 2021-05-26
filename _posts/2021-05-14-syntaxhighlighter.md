---
layout: post
title:  "Jekyll에 Syntax Highlighter 적용"
tags: Jekyll Rouge
---


며칠전 타입스크립트의 함수사용시 타입추가에 대한 글을 쓰는 중이었다. 가독성이 좋아진다. 가독성?? 코드 블럭을 보니 가독성이 좋지가 않다. 그 이후 블로그에 타이핑만 하고 있으면 그 코드 블럭이 눈에 밟히기 시작했다. 😑   

![image](/assets/readerable.png)



# Syntax highlighter 적용

Rouge라는 툴은 루비로 쓰여진 유명한  syntax highlighter 이다. Rouge가 theme을 제공해주기 때문에 syntax 하나하나에 수동으로 css에서 컬러를 지정하지 않아도 된다. 

**1 . ```_config.yml``` 설정파일에 추가하기.**

```ruby 
markdown: kramdown
kramdown:
  input: GFM
  syntax_highlighter: rouge
```

**2 . rouge 설치하기 (command line)**

```gem install rouge```

**3 . 설치가 성공적으로 되었다면, rouge 에서 제공하는 theme 알아보기.**

```rougify help style ```

어떤 스타일인지 preview 를 보고싶다면 [preview 클릭](https://spsarolkar.github.io/rouge-theme-preview/) .

**4 . 원하는 theme을 선택했다면, theme에 응한 css파일 생성**

나는 ```monokai.sublime```이란 theme을 사용했다. 

`rougify style monokai.sublime > assets/css/syntax.css`

syntax.css라는 파일이 css의 하위폴더에 생성된다.   

![image](/assets/scssfile.png) 

**5. 새로 생성된 css 코드베이스를 기존에 있던 syntax-highlighting.css로 옮기기**

새로 생성된 css의 코드를 전체 복사한 후, css의 원래 적용되어 있는 syntax-highlighting.css 파일 또는 scss안에 내용은 지우고 붙여넣기 한다.아마 theme 마다 파일이름은 다를 수 있다.

![image](/assets/scssfile1.png) 

**6 . 수정된 사항 적용 위해 build 하기**  

``` bundle exec jekyll serve```

```javascript

//적용된 theme 

const num:number = -6;
const str:string = "hello";
const bool : boolean = false;

```

---

참고자료

* [moon9342.github.io 님의 Jekyll 기반의 GitHub Page 생성(4) - rouge를 이용한 syntax highlighting](https://moon9342.github.io/jekyll-rouge){: target="_blank"}
