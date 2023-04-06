# [인간 JS엔진 되기👩‍💻]
> #### 출처: [ZeroCho TV 인간 JS 엔진 되기 (JS 고급 강좌)](https://www.youtube.com/playlist?list=PLcqDmjxt30Rt9wmSlw1u6sBYr-aZmpNB3)  
***

## 목차
[1-1 함수와 함수의 호출-고차함수](#1-1-함수와-함수의-호출-고차함수)  
[1-2 자바스크립트 스펙 외우지 마세요](#1-2-자바스크립트-스펙-외우지-마세요)
[1-3 호출 스택 분석](#1-3-호출-스택-분석)  
[1-4 스코프체인](#1-4-스코프체인)  
[1-5 호이스팅](#1-5-호이스팅)  
[1-6 this는 호출때 결정된다고](#1-6-this는-호출때-결정된다고)  
[1-7 this를 분석할 수 없는 케이스](#1-7-this를-분석할-수-없는-케이스)  
[1-8 블록 스코프와 매개변수](#1-8-블록-스코프와-매개변수)  
[노드 교과서 개정판 promise async와 await](#노드-교과서-개정판-promise-async와-await)  
[2-1 프로미스의 최고 장점을 아십니까](#2-1-프로미스의-최고-장점을-아십니까)  
[2-2 비동기는 동시가 아니다 순서의 문제다](#2-2-비동기는-동시가-아니다-순서의-문제다)  
[2-3 한번 비동기는 영원한 비동기](#2-3-한번-비동기는-영원한-비동기)  
[2-4 promise에도 동기 부분이 있다](#2-4-promise에도-동기-부분이-있다)  
[2-5 async와 await promise로 바꾸기](#2-5-async와-await-promise로-바꾸기)  
[2-6 무지성 await 연달아쓰기 금지](#2-6-무지성-await-연달아쓰기-금지)  
[2-7 프로미스 다양한 활용](#2-7-프로미스-다양한-활용)  
[2-8 클로저closure 분석](#2-8-클로저closure-분석)  
[개별 정리](#개별-정리)  
* * *


## 1-1 함수와 함수의 호출, 고차함수
#### 🟥 함수와 함수 호출의 차이
```js
 const add = (a,b) => a+b;
 function calculator(func,a,b){
    return func(a,b);
 }
 
 add(3,5) //8
 calculator(add,3,5) //8
```
😫 초보자들이 하는 실수  
`calculator(add,3,5)`에 `calculator(add(),3,5)`를 함


함수에 호출()을 붙이면 함수가 실행된다 => 즉, **return값이 온다고 생각** 해야 함  
`calculator(add(),3,5)`의 경우 `undefinded + undefined ` 이렇게 실행됨  

#### 🟧‼__헌데 만약__‼  
 `document.querySelector('#header').addEventListener('click', onClick(event)); `에서
 
 ```js
 //고차함수일 경우
 const onClick = () => (event) => {
   alert('addag')
 }
 
 //return문으로 바꿔주기
  const onClick = () => {
    return (event) => {
      alert('addag')
  };
 }
 
  //arrow function을 안쓰면??
  //()=> 는 function()
  const onClick = function() {
    return function(){
      alert('addag')
  };
 }
 ```

라면 return값이 있기때문에 ()을 붙여도됨  
react에서도 다음과 같이 사용

```js
const onClick = useCallback((e) =>{
  console.log(e.target)
},[]);

return{
  <div onClick={onClick}></div>
}
```
* * *


## 1-2 자바스크립트 스펙 외우지 마세요
#### 🟥 공식스펙 다~ 보지말고 코드보고 해석하기👩‍💻 __나무보단 숲을 보장🌳__
```js
//함수안에 선언들이 많을 경우
const x = 'x'; 
function c(){
  const y = 'y';
  console.log('c')
  //debugger
}

function a(){
  const x = 'x'; //함수의 선언
  console.log('a') //함수의 호출
  function b(){
    const z = 'z';
    console.log('b')
    c();
  }
  b()
}
a(); //a b c 
c(); //c
```
순차적으로 실행


🏢 스택 : 데이터를 차례대로 쌓는 구조  
__LIFO__ : LAST IN FIRST OUT __후입선출__  
__FILO__ : FIRST IN LAST OUT __선입후출__  

🚝 큐 : 한쪽에서 추가 반대편에서 삭제가 이뤄지는 구조  
__FIFO__ : FIRST IN FIRST OUT __선입선출__
* * *  


## 1-3 호출 스택 분석
1.  호출에 따라 벽돌쌓듯 차곡차곡 호출시 **선언**으로 가서 스택에 쌓임
2.  함수가 __}__ 끝날때마다 스택을 빠져나감


👀 빠져나가는 걸 보는 방법 => **debugger** 에서 Call stack 확인
* * *



## 1-4 스코프체인
#### 🟥 함수에서 어떤값에 접근이 불/가능한가?


🏢 block 기준 {}  
➕ 객체는 ({})  
```js
const x1 = 'x';
function c(){
  const y = 'y';
  console.log('c')
  function b(){
    const z = 'z';
    console.log('b')
    c();
  }  
}

function a(){
  const x2 = 'x'; //함수의 선언 
  console.log('a') //함수의 호출

  b()
}
a(); 
c();
```

💻 함수의 선언들만 보고 내 부모함수가 누군지 파악하기..  
최상위 함수: a,c => annoy(파일전체)  

__b의 경우는 c를 거쳐서 ... b → c → annoy__ 이걸 __Lexical Scope__(함수를 어디서 선언하였는지에 따라 상위 스코프를 결정하는 것)라 함

```js
annony -> x1, c, a
b -> z
c -> y, b
a-> x2
//즉, a에선 b에 접근할 수 없음
```


* * *



## 1-5 호이스팅
✔ 선언은 가장 위에, 선언하기전 접근을 하지마  
✔ eslint 사용하면 호이스팅을 막을 수 있음

```js
function a(){
  console.log(x); //TDZ :temporal dead zone 이런상황 피해...
  const x = 'x3'
}
```

❔ 만약에~ 호이스팅이 있으면 우짜나...
```js
var y; //이 선언 부분만 떼어서 위로 올려
y = "hitu" //var 쓰지 않는 이유 : 코드분석 시 헷갈리게 만들고 직관적이지 않음

//function 선언부분은 전체로 위로 올려
function c(){ 
  const y = 'y';
  console.log('c')
  function b(){
    const z = 'z';
    console.log('b')
    c();
  }  
}

function a(){
  const x2 = 'x'; //함수의 선언 
  console.log('a') //함수의 호출

  b()
}

const x1 = 'x'; //나머지는 그상태 유지

a(); 
```

```js
function a(){
  console.log(z)
}
const z = "z1"
a(); //z1


//반대로 다음상황이면  error
a(); //z1
const z = "z1"

```


* * *
## 1-6 this는 호출때 결정된다고!!!  
➕ js, node 최근에 globalThis 로 통합됨

#### 🟥 함수 앞에 객체가 붙는다면? => this는 그 객체가 됨  
```js
const obj = {
   name: 'hitu',
   sayName(){ 
     console.log(this.name) 
   }
}

obj.sayName() ////hitu 
//this는 함수가 호출될'때' 정해짐
```


#### 🟧 특별한 동작을 한게 없다면  ?
`console.log(this) //Window(~~~)` 


#### 🟨 기본적으로 this는 window인데 this가 바뀌는 경우
1. 객체에 붙어서 메서드로 호출하는 경우
2. new 붙여서 호출하는경우 (예) `function Human(name){this.name = name;}; new Human('hitu')`
3. bind, apply, call로 this를 직접적으로 바꿔주는 경우 (예) `sayName.bind({name:'hitu'})()`


```js
const obj ={
 name: 'hitu',
 sayName(){
   console.log(this.name);
   function inner(){
    console.log(this.name)
   }
   inner();
 }
}

//실행
obj.sayName() //hitu => 객체에 붙어서 메서드 호출
inner() //공란 => this를 바꿔준게 없음
```


#### 🟩➡ arrow function, __화살표함수는 ❕부모❕함수❕의 this를 받는다..__
```js
const obj ={
 name: 'hitu',
 sayName(){
   console.log(this.name);
   function inner = () => { 
    console.log(this.name)
   }
   inner();
 }
}

//실행
obj.sayName() //hitu 
inner() //hitu => 부모함수의 this를 받기 때문, ❗호출❗될때 정해진다 호출스택을 머리속으로 정리...

//그래서
const SN = obj.sayName; //undefinded ❗호출❗될때 정해진다
SN() //window(기)가 됨
```
🌼[스택 그리면서 총 정리 타임라인](https://youtu.be/pgo6URFz8tc?list=PLcqDmjxt30Rt9wmSlw1u6sBYr-aZmpNB3&t=1359)



* * *
## 1-7 this를 분석할 수 없는 케이스
```js
header.addEventListner('click',function(){console.log(this + '는 ❗호출❗될때 정해진다')})
```
다음과 같은 코드에서는 어디서 호출되는지 모름, 분석할수 없음



💭만약에 addEventListner를 만든다면  ~~ ?(추론)
```js
const header = {
 addEventListner :function(eventName, callback){
  callback.call(this) //이렇게 썼을수도.. header가 곧 이벤트리스너의 this니까...
  callback.call(obj) //obj를 call로 넣어줌으로써 this가 곧 obj가 됨
 }
}

header.addEventListner('click',function(){console.log(this + '는 ❗호출❗될때 정해진다')}) 
//this는 header가 됨
```

➡ 화살표 함수를 넣는다면 ~~~ ?
`
header.addEventListner('click',()=>{console.log(this)}) //addEventListner가 정해주는 값을 따르지않고 window가 나옴
`
1. 화살표 함수는 부모의 this를 따라가고
2. call을 못붙이며 무조건 실행되게 되어있음


* * *
## 1-8 블록 스코프와 매개변수
```js
const x = true;
const y = false;

function a(){
 let a = 4;
 if(x){
  let a = 3;
  for(let i = 0; i < a; i++){
   console.log(i)
  }
  if(!y){
   aaa()
  }
 }
 z()
}
a(); //0 1 2
const z = () => {};
```


1-1.   

|선언 map 작성||
|:--- | :--- |
|annony|a(function), x(true), y(false->true)|
|a|`let a = 4`|
|a if(x)|`let a = 3`|
|a if(x)|for (i가 0부터 2까지 block scope가 3개가 생김)|  



1-2. 
|호출스택 작성(아래서부터)|
|:--- |
|annoy, this는 window(만약 'use strict'모드라면 undefined)|
|a|
|z //선언을 안한상태에서 호출하면 __Error: z is not defined__|  



* * *
## 노드 교과서 개정판 promise, async와 await
#### 🟥 프로미스란? 내용은 실행 되었지만 결과를 아직 반환하지 않은 객체  
· 실행해놓고 필요할때 꺼내쓰면 됨💌  
· then을 붙이면 결과를 반환함(성공리턴값: resolved) / (실패리턴값: reject => catch로 연결)  
· 실행이 완료되지 않았으면 완료된 후에 Then 내부 함수가 실행됨  

➕ Promis all(배열): 여러 개의 프로미스를 동시에 실행
```js
function findAndSaveUser(users){
 Users.findOne({})
  .then((user) => {
   user.name = 'hitu';
   return user.save();
  })
  .then((user) => {
   return Users.findOne({gender : 'm'});
  })
  .then((user) => {
   //생략
  })
}
```
프로미스는 위에서 아래로, 좌에서 우로 실행됨


#### 🟧 async(asynchronous[eɪˈsɪŋkrənəs]: 동시에 존재, 발생하지 않는) / await[əˈweɪt] (~을 기다리다)  
➕ AJAX(Asynchronous Javascript And XML)) ❗발음 유의❗

```js
async function findAndSaveUser(Users){
 let user = await.Users.findOne({});
 user.name = 'hitu';
 user = await user.save();
 user = await Users.findOne({gender: 'm' })
}
```
우에서 좌로 실행, 코드가 짧다는 장점


> 
* * *
## 2-1 프로미스의 최고 장점을 아십니까


😫초보자가 하는 실수: 콜백 =  무조건 비동기? ❌NO❌  
✅콜백은 비동기가 아님, 동기일 수도 아닐수 도 있음  

### 동기 콜백
```js
calculator(function(x,y){return x + y},3,5)
```


### 비동기 콜백, 대표적인 예 `setTimeout`
```js
setTimeout(()=>{
 console.log('비동기 콜백')
},1000)
```

> ↪ Promise __내용은 실행 되었지만 결과를 나중에 쓸 수 있는 것.__
```js
const promise = new Promise((resolve, reject) => {
 setTimeout(()=>{
  resolve();
 },1000)
});
```
신나게 딴짓하다가 결과값을 꺼내고 싶을 때는 `promise.then((결괏값)=>{//결괏값 사용})`


➕ 습관적으로 promise를 만들자마자 사용하고, catch도 붙이는 경우 
```js
new Promise().then((결괏값) =>{
 //결괏값 사용
}).catch((에러) => {});
```

#### promise all 사용 예제
```js
const p1 = axios.get('sever address 1');
const p2 = axios.get('sever address 2');
const p3 = axios.get('sever address 3');
const p4 = axios.get('sever address 4');
const p5 = axios.get('sever address 5');
const p6 = axios.get('sever address 6');

Promise.all([p1,p2,p3,p4,p5,p6]).then((results) => {})
```


#### 😥 promiseAll의 단점 : 하나라도 오류나면 catch로 넘어간다
```js
Promise.all([p1,p2,p3,p4,p5,p6]).then((results) => {}).catch((error)=>{})
```



#### 😎 So, __allSettled__ 사용✨
```js
Promise.allSettled([p1,p2,p3,p4,p5,p6]).then((results) => {}).catch((error)=>{})

//results에 각각의 📃성공여부를 표시해줌
//후에 실패한 것만 필터링해서 다시 시도함👍
```

* * *
## 2-2 비동기는 동시가 아니다 순서의 문제다
> 동시의 개념이 아닌 __순서__의 문제 


```js
setTimeout(()=>{
 console.log('a')
},0)


setTimeout(()=>{
 console.log('b')
},1000)


setTimeout(()=>{
 console.log('c')
},2000)
```
// a, b, c 출력

#### 🟥 순서  
1. anonymous
2. 첫번째 setTimeout 호출 후 빠져나감
3. 두번째 setTimeout 호출 후 빠져나감
4. 세번째 setTimeout 호출 후 빠져나감
5. anonymous 빠져나감

#### 🟧🔄[Event Loop](https://nodejs.org/ko/docs/guides/event-loop-timers-and-nexttick)

#### 🟨제로초의 요약된 방식 -> 다음 세가지로 분석
1. Background
2. Macro task ✔
3. micro task ✔  


#### 🟩👀 Background란 ? 실제로 있는 개념이 아니라 제로초가 만든 추상적인 개념
1. 이 부분은 javascript가 아닌 javascript 엔진 또는 오히려 더 넓은 범위인 운영체제라고 생각하면 됨. 
2. 이 Background의 특징은 다른언어( c++일수도있고  c일수도 있음. 즉, ✨동시에 돌아갈 수 있음)로 되어있는 부분이라고 볼 수 있음
3. javascript는 싱글쓰레드이기때문에 동시의 개념이 없음
4. Background에 들어가는 것들 (예)setTimeout의 timer, promise, nextTick, ajax요청, eventListener, 커스텀이벤트 등등...  

✔ Background로 간 코드는 반드시 task queue를 거친다   
🕑setTimeout의 경우는 Macro task에 쌓임  


✔ 그 후 호출스택이 비어있을때 함수를 호출스택에 끌어올려준다 ..차례로..FIFO



* * *
## 2-3 한번 비동기는 영원한 비동기
#### Macro task & micro task에는 각자 누가 들어가나❔❔

1. micro task : Promise, process.nextTick  
2. Macro task : 그 외 나머지(setTimeout, eventListener ..)
 
 
😶 Macro task & micro task에 각각 들어간다면?  
1. 먼저 micro task 가 먼저 호출스택으로 끌어올림
2. micro task가 꽉 차있으면 Macro task는 실행안됨❌


```js
let a = 2;
setTimeout(()=>{
 a = 5;
 console.log(a)
 //비동기는 비동기 안에서만 코딩해야해
 
  setTimeout(()=>{
     setTimeout(()=>{
        //그래서 callback hell 이 생기기도...
     })
  })
},0)

console.log(a) // 5 ❌

// 2 5 가 차례로 나옴
```
😎한번 비동기는 영원한 비동기 😎 : 비동기 안으로 들어가면 무조건 비동기임


* * *
## 2-4 promise에도 동기 부분이 있다
#### callback hell을 해결한 Promise도 hell이 존재하지 않을까?


```js
//promise❌
let a = 2;
setTimeout(()=>{
 a = 5;
 console.log(a)
},0)

//promise⭕ 
const p = new Promise((resolve, reject) => {

  //함수(이 부분은 동기임)가 먼저 실행되고  p에 대입된다.
  console.log('제일먼저') //1️⃣번째 출력
  setTimeout(()=>{
    a = 5;
    console.log(a) //3️⃣번째 출력
    resolve(a) //결괏값
  },0)
});

//딴짓
//딴짓
//딴짓
//console.log('딴짓') 2️⃣번째 출력
//딴짓
//딴짓
//...

//promise 실행👴
p.then((result) => {
 console.log('result', result) //4️⃣번째 출력
})
```
***
## 2-5 async와 await, promise로 바꾸기 
```js
p.then((result)=>{
  console.log('result', result);
  return 1
}).then((result)=>{
    console.log(result) //1 출력
}).then((result)=>{
    console.log(result) //undefined 출력 
    //왜? undefined 출력? 함수는 return안적어 놓으면 기본적으로 undefined
}).then(()=>{
  
}).then(()=>{
  
}).catch(()=>{
  //전체 then에 대해서 그중 하나라도 에러났을경우 catch로 이동
}).finally(()=>{

})
```

✔ 굳이 catch를 아래 쓸 필요없음  
then - catch, then - catch, then - catch .... 이렇게 빼서 사용해도 됨
```js
//setTimeout을 Promise로 만들기
function delayP(ms){
  return new Promise((resolve, reject)=>{
    setTimeout(resolve, ms);
    reject(err)
  })
}
async function a(){
  //따로 처리할때
  try{
    await delayP(1000)
  }catch(error){
    console.log(error)
  }

  //함께 처리할때
  try{
    await delayP(1000)
    await delayP(1000)
    await delayP(1000)
    await delayP(1000)
    await delayP(1000)
  }catch(error){
    console.error(error)
  }
}
```

```js
async function a(){
  const a = await 1;
  const b = await Promise.resolve(1);
}
```
```js
async function a(){
  const a = await 1;
  console.log('a',a)
  console.log('gglgl')
  await null;
  const b = await Promise.resolve(1);
  console.log('b',b)
  return b
}


//바꿔보기
/*
  1.일단 await이 기준임 => await이 then이다
  2. async의 해석은 우에서 좌, 반면 promise는 위에서 아래로
*/
Promise.resolve(1)
  .then((a) =>{
    console.log('a',a)
    console.log('gglgl')
    return null
  }).then(() =>{
    return Promise.resolve(1);
  }).then((b) =>{
    console.log('b',b);
    return b
  })
```

✔ 주의할점  
<img src="/img/2_5_img1.jpg">  
다음 await이 나올 때 까지 작성 => null은 return에 넣어줌


***
## 2-6 무지성 await 연달아쓰기 금지
😫 promise를 async로 바꿀때 많은 사람들이 하는 실수 : await을 줄줄이 쓴다...  
=> 이럴 경우 낭패 볼 수 있음
```js
function delayP(ms){
  return new Promise((resolve, reject) => {
    setTimeout(resolve,ms)
  })
}
async function a(){
  await delayP(3000)
  await delayP(6000)
  await delayP(9000)
  //total 18초 걸림;;
}
```

✨ allSettled 사용  
```js
function delayP(ms){
  return new Promise((resolve, reject) => {
    setTimeout(resolve,ms)
  })
}
async function a(){
  await p1 = delayP(3000)
  await p2 = delayP(6000)
  await Promise.allSettled([p1,p2]); //Promise 2개가 background에 넘어가서 동시에 실행
  await delayP(9000)
  //total 15초 걸림
  
  
  /*
    🕑 Promise의 시간의 흐름:  
    실행은 바로 ----> 결괏값이 나올 때는 나중 ----> 결괏값을 사용할 때는 더 나중  
  */
}
```

😎 예를 들어 게시글 조회 코드를 짠다고 하자
```js
async createPost(){
  await db.getPost(); //게시물 조회
  if(post){
    res.status(403).send('이미 게시글이 존재합니다.')
  }else{ 
    await db.createPost(); //게시글 작성
    const p1 = db.userIncrementPostCount(); //사용자에 게시글 카운트 1 올림
    const p2 = db.createNoti(); //새로운 게시글 작성 완료 알림
    await Promise.allSettled([p1,p2]); 
  }
}

/*
  await 을 쭉 나열하기보단, 
  순차적으로 할 필요없는 코드는 Promise정리!!!
*/
```

😫 Promise로 못바꾸는 경우
```js
async function c(){
  const a =  await 1;
  const b =  await 2;
  return a + b
}

//원래는 promise로 못바꿈❌
//generator function으로 바꿈
Promise.resolve(1);
  .then((a)=>{
    return 2
  })
  .then((b)=>{
    /*
      이건 안됨 scope가 달라서.. 
      generator에서는 동일한 scope를 제공하기 때문에 가능하다👀  
    */
    return a + b 
  })
  


//즉시 실행 함수로 가능하게는 할 수 있음..⭕
(function (){
  let a;
  let b;
  Promise.resolve(1);
    .then((a)=>{
      return 2
    })
    .then((b)=>{
      /*
        이건 안됨 scope가 달라서.. 
        generator에서는 동일한 scope를 제공하기 때문에 가능하다👀  
      */
      return a + b 
    })
)()
```
***
## 2-7 프로미스 다양한 활용 
```js
async function b(){
  await p1 = delayP(3000)
  await p2 = delayP(6000)
  await Promise.allSettled([p1,p2]); 
  await delayP(9000)
}

new Promise((resolve, reject) => {
  const p1 = delayP(3000)
  const p2 = delayP(6000)
  return Promise.allSettled([p1,p2]); 
})

  .then(()=>{
    return delayP(9000)
  })
  
```

```js
const p1 = axios.get('서버주소')
const p2 = axios.get('서버주소')
const p3 = axios.get('서버주소')
...


await Promise.all([p1, p2, ... p12])
await Promise.all([p13, p4, ... p24])
```
✔ 단 allSettled 같은 거 사용할때,  
브라우저에서 서버에 요청을 보낼 수 있는양이 정해져 있음(maybe..12개?)  
그래서 완전히 동시에 보낼 순 없고 한 묶음별로 보낸다고 이해  
🤙 안정성을 위해서라면 묶음으로 끊어서 보내면 됨  

👀 map 쓰는 경우
```js
const result = await Promise.all([p1,p2,p3])

result.map(async()=>{
  await result조작(); //p1,p2,p3 동시에(js에서 동시라는 개념은 없지만 여기서 동시에란 소리는 backgroud에서 이뤄지는 것을 말함)
},[])

for(let result of results){
  await result조작(); //p1,p2,p3 순서대로
}
```
[그림판 그려보기](https://youtu.be/JG2DPt00bXs?list=PLcqDmjxt30Rt9wmSlw1u6sBYr-aZmpNB3&t=478)


***
## 2-8 클로저(closure) 분석
1.  클로저 문제 => 스코프, 비동기, var
2. 클로저가 문제다 => ❌
3. 클로저를 사용해서 해결하는 문제
4. for문(반복문)과 비동기를 함께 사용하면 종종 발생
 
 ```js
function a(){
  for(var i = 0; i<5; i++>){ //i가 4일때까지는 true인데 i가 5가되면서 5<5 false
    setTimeout(()=>{
      console.log(i)
    },i*1000)//0 1000 2000 3000 4000
  }
}
a(); // 5 5 5 5 출력


//해결법
//1. 즉시실행함수로 출력
function a(){
  for(var i = 0; i<5; i++>){
    (function(j){
      setTimeout(()=>{
        console.log(j)
      },i*1000) 
    })(i)
  }
}
a(); // 1,2,3,4 출력




//2. var을 ✨let으로 바꿈
function a(){
  for(let i = 0; i<5; i++>){ 
    setTimeout(()=>{
      console.log(i)
    },i*1000)
  }
}
a();// 1,2,3,4 출력

 ```
😫 문제점: for과 var의 문제  
=> 해결법: 즉시실행함수로 클로저 생성 || let


<img src="/img/2_8_img1.jpg">  
🟧 var로 실행해봤을 때 그려보기  

1. 호출스택에 호출  
2. 선언지도  
2-1. var의 경우 block scope를 따르지 않고 ***함수scope***를 따름. 즉, fuction a를 따른다  
3. 호출스택에 setTimeout 호출   
3-1. 호출할때마다 background에 timeout이 생성됨  
3-2. for문돌면서 setTimeout이 끝남  
4. a함수, Annonymos도 끝남(= 호출스택에서 빠짐)  
5. 호출스택이 텅비게 되니까 이벤트루프가 하나씩 끌어올려줌  
5-1. 그전에 앞서 background는 조건이 만족하면(timeout이 0초 지나면..) 콜백함수를 큐로 보냄  
5-2. 그다음 큐에서 호출스택에 올림  
5. 비동기: ()=> 익명의 함수에서 i에 접근할수있는지 거슬러 올라가면 var=i는 이미 실행되어 5가 되어있음  


<img src="/img/2_8_img2.jpg">  
🟨 즉시 실행 함수로 실행해봤을 때 그려보기  



<img src="/img/2_8_img3.jpg">  
🟩 let로 실행해봤을 때 그려보기  
















***
## 개별 정리  
### 1. Arrow Function 
> 출처: [Arrow function은 function을 대체하는 신문법이 아님](https://codingapple.com/unit/es6-3-arrow-function-why/)  

#### 🌚 기존의 함수를 만드는 방법
```js
function 함수(){

}

var 함수 = function(){

}

함수(); //함수실행
````


#### 🌝 함수를 만드는데에 있어서 es6의 편리한 문법: ➡Arrow Function⬅
```js
var 함수 = () => {

}

함수(); //함수실행
```

#### 🟥 함수 본연의 기능
1. 코드들을 기능으로 묶고 싶을 때 사용
2. **입출력 기계**를 만들고 싶을 때 사용  


#### 🟧 Arrow Function의 장점👍  
1. 입출력 기능을 만들때 직관적으로 볼 수 있음  
1-1. 입출력 기능: `var 함수 = (a) => { return a + 10 }; 함수(5); //15출력`  
2. 파라미터가 1개면 ? **소괄호** 생략가능 `var 함수 =  a => {return a +  10}`  
3. 코드가 한줄 밖에 없다? **중괄호 & return**도 생략가능 `var 함수 =  a =>  a +  10`  


#### 🟨 Arrow Function의 예시
#### 1. forEach함수(반복문)
```js
[1,2,3,4].forEach(fucntion(a){
  console.log(a); //1,2,3,4 출력
})

//➡Arrow Function
[1,2,3,4].forEach( a => console.log(a);)  
```  

#### 2. 이벤트 리스너
```js
document.getElementById('버튼').addEventListener('click',
  function(e){

  }
)


//➡Arrow Function
document.getElementById('버튼').addEventListener('click',(e) => {})
//여기서 this를 사용하면 window
```
#### 3. object 안의 함수
```js
var 오브젝트 = {
  함수 : function(){}
}

//➡Arrow Function
var 오브젝트 = {
  함수 : () => {}
}
```
#### 🟩 Arrow Function의 특징  
- 바깥에 있던 this값을 내부에서 그대로 사용, 함수 내의 this값을 변경시키지 않음  
1. 일반 이벤트 리스너에선 `this == e.currentTarget`
2. Arrow function 이벤트 리스너에선 `this == 바깥의 this값`  
2-1. Arrow function 이벤트 리스너에서 e.currentTarget 사용하고 싶으면 e.currentTarget를 그대로 사용해서 쓰면 됨. this를 사용하지 않고...


***
### 호출과 선언의 차이 


***
### 스코프 체인이란?


***
### 호이스팅 최적화



***
### this의 결정시기


***
## promise란?
> 출처: [자바스크립트 12. 프로미스 개념부터 활용까지 JavaScript Promise](https://youtu.be/JB_yU6Oe2eE)  
#### 🟥 promise란 ?
- javascript안에 내장되어져 있는 object, 비동기 함수를 사용할때 callback함수 대신에 유용하게 쓸 수 있음 
- State  
-- pending(우리가 지정한 오퍼레이션이 작동 중 일때)  
-- fullfilled(완료한 상태)  
-- rejected(파일을 찾을 수 없거나 네트워크에 문제가 생겼을때)  

- Producer  
-- 원하는 기능을 수행해서 해당하는 데이터를 만들어냄, 즉 promise 의 object

- Consumer  
-- 원하는 데이터를 소비


#### 🟧 Producer 
```js
//promise는 class이기때문에 new라는 키워드를 이용해서 object 생성
const promise =  new Promise((resolve, reject) => {
    /* 
    
    doing some heavy work 
    (ex). 네트워크에서 데이터를 받아오거나 큰 데이터를 읽어들일때 
    동기적으로 받아오면 그 다음 라인의 코드가 실행되지 않기 때문에 시간이 걸리는 일들은 promise로 처리
    
    */

   console.log('doing something') //바로 출력됨, promise를 만드는순간 콜백함수가 실행되기 때문
});
```
✔ 이런 식으로 작성하게되면 바로 실행되기때문에 사용자가 요구하지않았는데도 ❌불필요한 통신❌이 일어날 수 있음  


```js
const promise =  new Promise((resolve, reject) => {
  setTimeout(()=>{
      resolve('hitu'); //성공시 받아온 데이터를 resovle를 통해서 출력
      reject(new Error('no network')); //reject는 보통 Error라는 클래스를 통해서 전달
  }, 2000)
});
```

#### 🟨 Consummer : then, catch, finally
```js
promise.then((value)=>{
  //value에 resolve 값이 전달 됨
  console.log(value) //hitu 출력
})
```


```js
promise
  //🌝 성공케이스 출력
  .then(value=>{ //value에 resolve 값이 전달 됨
    console.log(value) //hitu 출력
  })

  //🌚 Error 케이스 출력
  .catch(error=>{
    console.log(error) //Error: no network 출력
  })

  //🆕 성공/실패 상관없이 무조건 마지막에 호출됨
  .finally(()=>{
    console.log('finally')
  })
```

#### 🟩 Promise Chaing
```js
const fetchNumber =  new Promise((resolve, reject) => {
  setTimeout(()=> resolve(1),1000) //1초후 숫자 1을 전달하는 promise
})

fetchNumber
.then(num = > num*2) //1*2 = 2
.then(num = > num*3) //2*3 = 6
.then(num = > {
  return new Promise((resolve, reject) => {
    setTimeout(()=> resolve(num-1),1000)  //6-1=5
  })
})
.then(num => console.log(num)) //5
```

#### 🟦 Error Handling
```js
//닭을 받아서 달걀을 받구 그 달걀로 후라이 해먹기
const getHen = () =>
  new Promise((resolve, reject)=>{
    setTimeout(()=>resolve('닭'), 1000)
  });
const getEgg = hen =>
  new Promise((resolve, reject)=>{
    setTimeout(()=>resolve(`${hen} => 달걀`), 1000)
  });
const cook = egg =>
  new Promise((resolve, reject)=>{
    setTimeout(()=>resolve(`${egg} => 후라이`), 1000)
  });

  /*
  getHen()
  .then(hen => getEgg(hen))
  .then(egg => cook(egg))
  .then(meal => console.log(meal));
  */

  //value 같을 경우 생략 가능
  getHen()
    .then(getEgg)
    .then(cook)
    .then(console.log);

  // 닭 => 달걀 => 후라이 출력
```

만약 reject 발생시 
```js
const getHen = () =>
  new Promise((resolve, reject)=>{
    setTimeout(()=>resolve('닭'), 1000)
  });
const getEgg = hen =>
  new Promise((resolve, reject)=>{
    setTimeout(()=>reject(new Error(`${hen} => 달걀`)), 1000)
  });
const cook = egg =>
  new Promise((resolve, reject)=>{
    setTimeout(()=>resolve(`${egg} => 후라이`), 1000)
  });

  getHen()
    .then(getEgg)
    .catch(error =>{
      return '베이컨' 
      //계란을 받아오는건 실패했지만 베이컨을 대신 전달해줌, 즉 promise chain 이 실패하지 않고 전달해줌
    })
    .then(cook)
    .then(console.log)
```


#### 🟪callback hell To Promise
callback hell
```js
class UserStorage {
  loginUser(id, password, onSuccess, onError){
    setTimeout(()=>{
      if(
        (id === 'hitu' && password ==="sophia")
        (id === 'mega' && password ==="study")
      ){
        onSuccess(id);
      } else {
        onError(new Error('not found'));
      }
    },2000)
  }

  getRoles(user, onSuccess, onError){
    setTimeout(()=>{
      if(user === 'hitu'){
        onSuccess({name:'hitu', role:"sawon"});
      } else {
        onError(new Error('no access'))
      }
    }, 1000)
  }
}

const userStorage = new UserStorage();
const id = prompt('enter your id');
const password = prompt('enter your password');

userStorage.loginUser(
  id,
  password,
  user =>{
    userStorage.getRoles(
      user,
      userWithRole =>{
        alert(
          `Hello ${userWithRole.name}, you have a ${userWithRole.role} role`
        );
      },
      error =>{
        console.log(error)
      }
    )
  }
)
```

Promise
```js
class UserStorage {
  loginUser(id, password){
    return new Promise((resolve, reject) =>{
      setTimeout(()=>{
        if(
          (id === 'hitu' && password ==="sophia")
          (id === 'mega' && password ==="study")
        ){
          resolve(id);
        } else {
          reject(new Error('not found'));
        }
      },2000)
    })
  }

  getRoles(user){
    return new Promise((resolve, reject) =>{
      setTimeout(()=>{
        if(user === 'hitu'){
          onSuccess({name:'hitu', role:"sawon"});
        } else {
          onError(new Error('no access'))
        }
      }, 1000)      
    })
  }
}

const userStorage = new UserStorage();
const id = prompt('enter your id');
const password = prompt('enter your password');
userStorage.loginUser(id, password)
.then(userStorage.getRoles()) //user호출
.then(user => alert(`Hello ${user.name}, you have a ${user.role} role`))
.catch(console.log)

```


***

## async, await란?
> 출처: [자바스크립트 13. 비동기의 꽃 JavaScript async 와 await 그리고 유용한 Promise APIs](https://youtu.be/aoQSOZfz3vQ)  

#### 🟥 async, await란?
- promise를 간결, 간편하고 동기적으로 실행 되는 것처럼 보이게 함  
-- 기존에 존재하는 promise 위에 조금더 간편한 api 제공 = syntactic sugar🍩  

🌚 before
```js
function fetchUser(){
  // 10초동안 네트워크 요청을 받는 코드라고 해보자
  return 'hitu';
}

const user = fetchUser();
console.log(user)

//이렇게 동기적으로 이 코드를 실행하면 실행이 완료될때까지 사용자가 기다려야함😡
```

🤙promise
```js
function fetchUser(){
  return new Promise((resolve, reject)=>{
    return 'hitu';
  })
}

const user = fetchUser();
user.then(console.log)
```


👐🏼 async 
```js
async function fetchUser(){
  return 'hitu';
}

const user = fetchUser();
user.then(console.log)
```

✋🏼 await (async를 사용했을때만 사용가능)
```js
function delay(ms){
  //정해진 ms초가 지나면 resolve를 호출
  return new Promise(resolve => setTimeout(resolve, ms))
}

async function getApple(){
  //await을 사용하게 되면 delay가 끝날때까지 기다려줌
  await delay(1000) 
  throw: 'error'; //error가 발생할 경우
  return '사과';
};

//동기적인 코드를 쓰는 '것'처럼 만들어두면 기다렸다가 return을 하니까 더 쉽게 이해할 수 있음
async function getBanana(){
  await delay(500)
  return '바나나';
};

/*
🤙promise로 쓸 경우 : chaining

function getBanana(){
  return delay(1000)
  .then(()=>'바나나') ;
};

*/


//promise callback 지옥
function pickFruits(){
  return getApple()
  .then(apple => {
    return getBanana()
    .then(banana => `${apple} + ${banana}`);
  })
}

pickFruits().then(console.log) //사과 + 바나나 출력
```


👐🏼 async 사용할 경우
```js
async function pickFruits(){
  try{
      const apple = await getApple();
      const banana = await getBanana();
  }catch(){
    //error처리
  }
  return `${apple} + ${banana}`;
}
pickFruits().then(console.log) //사과 + 바나나 출력

```

👭 await 병렬처리
```js
async function getApple(){
  await delay(1000) 
  throw 'error'; 
  return '사과';
};

async function getBanana(){
  await delay(1000)
  return '바나나';
};

/*
바나나와 사과를 받아오는데는 두개가 서로 연관이 없기때문에 
사과 받아온뒤~ 바나나를 받아올 필요가 없음!! 
즉, 병렬적 기능 수행
*/
async function pickFruits(){
  //promise 즉시실행
  const applePromise =  getApple();
  const bananaPromise =  getBanana();

  const apple = await applePromise;
  const banana = await bananaPromise;
  return `${apple} + ${banana}`;
}
pickFruits().then(console.log) //사과 + 바나나 출력
/*
```
👭 await 병렬처리 more simple
```js
function pickAllFruits(){
  return Promise.all([getApple(),getBanana()]);
  .then(fruits => fruits.join('+')) //배열을 string으로 묶으려면 join 사용
}

pickAllFruits().then(console.log) //사과+바나나
```

🏃🏼‍♀️ 어떤것이든 상관없이 **먼저** 값을 리턴하는 과일만 받아오고 싶다 ??
```js
function pickOnlyOne(){
  return Promise.race([getApple(),getBanana()]);
}

pickOnlyOne().then(console.log)  //바나나 출력
```

## Closure 란?
> 출처: [자바스크립트 중급 강좌 #11 클로저(Closure) 5분만에 이해하기](https://youtu.be/tpl2oXQkGZs)  

자바스크립트란 어휘적환경(Lexical Environment)을 갖음  
코드가 실행되면 스크립트내에서 선언한 변수들이 Lexical환경에 올라간다  

🟥ex.1
```js
//2. 외부환경에서 one을 찾음
let one;
one = 1;

function addOne(num){
  console.log(one+num)
}

// 1. num값은 받았지만 one은 함수 내부에 없어
addOne(5);
```

🟧ex.2
```js
//makeAdder, add3은 전역 Lexical환경에 들어감
function makeAdder(x){
  return function(y){
    return x + y;
  }
}

const add3 = makeAdder(3); //makeAdder실행되고 x값을 부여받음
console.log(add3(2)) //y가 2로 들어감

const add10 = makeAdder(10);
console.log(add10(5))  //15
console.log(add2(1)) //4
```
=> 함수와 렉시컬 환경의 조합을 클로저라고 함  
<span style="color:red">함수가 생성될 당시의 외부 변수를 기억</span>





|용어                  | 정의                        |
|:--- | ---: |
|             |             |
|            |            |
