- [CS](#cs)
	- [Web APIs](#web-apis)
	- [Event Loop](#Event-loop)
	- [Closure](#closure)
	- [Pattern](#pattern)
	- [Tip](#tip)
		- [구조분해할당](#destructuring-assignment)
		- [Curry Function](#curry-function)
- [Prototype](#prototype)
- [Scope](#scope)
- [this](#this)
- [bind(), call(), apply()](#bind-call-apply)
- [Hoisting 호이스팅](#hoisting)
- [함수 function](#function)
- [Generator function\*](#generator)
- [Type](#type)
- [Symbol](#symbol)
- [Iterable](#iterable)
- [Iterator](#iterator)
- [\*\[Symbol.iterator\]()](#symbol-iterator)
- [Document 요소 접근](#document)
- [Event](#event)
- [얕은복사 깊은복사](#copy)
- [컴포넌트가 뷰포트에 얼마나 보이는지 Intersection Observer](#intersection-observer)
- [reduce()](#reduce)
- [take()](#take)
<br/><br/>
- [반복문](#loop)
	- [forEach](#foreach)
	- [for in](#for-in)
	- [for of](#for-of)
	- [map](#map)
	- [for 결론](#결론)
<br/><br/>
- [비동기처리](#asynchronous)
	- [Promise](#promise)
		- [Promise.all](#promise-all)
		- [Promise.allSettled](#promise-allsettled)
		- [Promise.race](#promise-race)
	- [반복문관련 비동기처리](#asynchronous-loop)
		- [async, for of](#async-for-of)
		- [for await of](#for-await-of)

# CS
## Web APIs
- JS 가 아닌 외부요소, 비동기처리는 JS가 아닌 외부에서 처리됨
- DOM (document)
- AJAX (XMLHttpRequest)
- Timeout(setTimeout)

## Event Loop
- Evnet Loop는 call stack과 callback queue를 계속 주시하고 있다가 call stack이 비면
<br/>먼저 들어온 순서대로 callback queue에 있는 함수들을 call stack에 넣어줍니다.
<br/>비동기처리 시 Web API의 작업이 끝나면 해당 작업의 callback 함수가 call queue에 대기합니다.
<br/>그동안 call stack에 있는 함수의 동작이 완료되고 call stack이 비면
<br/>callback queue에 대기하던 함수가 call stack에 들어가 동작을 완료합니다.

## Closure
- 외부함수 내에 내부함수가 외부함수의 변수를 참조하는 경우
<br/>외부함수가 종료되어도 내부함수는 외부함수의 변수를 기억하고 접근할 수 있다.

## Pattern
- Singleton Pattern : 클래스의 인스턴스화를 하나의 객체만 허용 (1 class 당 1객체)
- Factory Pattern : 특수함수인 팩토리 함수를 사용해 비슷한 객체를 많이 만드는 것 (그냥 class)
- Mediator Pattern : 객체그룹에 대한 중앙권한을 제공하는 것 (중재자패턴 > 채팅룸에 사용?)
- Observer Design Pattern : event-driven 시스템을 이용하는 것(Publisher-Subscriber > 팔로우, 알림)
- Module Pattern : 재사용 가능하게 분할하는 것
	- <script type = "module">
		- 항상 엄격모드로 실행
		- 지연실행 (html 문서가 모두준비 완료된 후 실행)
		- 외부에서 스크립트를 불러오려면 Cors 헤더가 있어야함
		- 중복 스크립트는 무시됨
		- 모듈은 한번만 실행되기 때문에 다른곳에서 수정한경우 다른 모듈에서도 동일하게 수정됨
	- <script async type = "module">
		- 인라인 모듈 스크립트도 비동기처리 가능
		- 다른 스크립트나 HTML 문서처리 기다리지 않고 바로 실행됨
		- 독립적인 기능을 구현할 때 유용
	- <script nomodule>
		- 구형브라우저에서는 module 사용불가 > 모듈스크립트 사용할수없음을 명시
```javascript
function outerFunction(outValue){
	let outTemp = 1;
	return function innerFunction(inValue){
		console.log(outValue + '/' + inValue + 'outTemp');
	}
}
function testFunction1(){
	let b = 2;
	console.log(a, b);
}
function testFunction2(){
	let a = 1;
	testFunction1();
}
function testFunction3(){
	let c = 3;
	function testFunction4(){
		let d =4;
		console.log(c,d);
	}
	testFunction4();
}
const newFunction = outerFunction('out');	// innerFunction 반환 (outerFunction 종료)
newFunction('in');				// out/in 출력 (innerFunction에서 outValue, outTemp 기억)
function testFunction1();			// error (a 존재X)
function testFunction2();			// error (function1에서 a 기억 불가)
function testFunction3();			// 3,4 출력 (function4에서 function3의 C 기억 > closure)
```
	
## Tip
### Destructuring Assignment
- 구조분해할당
```javascript
// JSON
let obj = {
	a: 1,
	b:2,
	c:3,
	d: {
		x:10,
		y:20,
		z:30
	}
};
const {a, b, c} = obj;
const {d: {x,y,z}} = obj;
const {a: tempA = 100} = obj;	// (obj에 a가 존재) ? tempA = a : tempA = 100
const {e: tempB = 100} = obj;	// (obj에 e가 존재) ? tempB = a : tempB = 100
const {d: {x: tempC = 100}} = obj;	// (obj에 d:{x}가 존재) ? tempC = x : tempC = 100

console.log(a,b,c);	// 1,2,3 출력
console.log(x,y,z);	// 10,20,30 출력
console.log(tempA);	// 1 출력 (a가 obj에 있음)
console.log(tempB);	// 100 출력 (e가 obj에 없음)
console.log(tempC);	// 10 출력 (d:{x}가 obj에 있음)
// array
const temp = [1,2,3,4,5];
const [one, two, three, four, five] = temp;
const [,,tt,,ff] = temp;
console.log(one, two, three, four, five, tt, ff);	// 1,2,3,4,5, 3,5 출력
```

### Curry Function
- 연속된 lambda 함수에서 매개변수를 ()()()형태로 전달해줄 수 있도록 자동변환
```javascript
const function = (a)=>{ return (b)=>{ return (c)=>{return `${a} ${b} ${c}`;}; }; };
const temp = function("x")("y")("z");
console.log(temp);	//x y z 출력

const f = d=>e=>f=> `${d} ${e} ${f}`;
const fValue = x('x')("y")("z");
console.log(fValue);	// d e f 출력

// 동적으로 curry 함수 생성
function curry(func) {
	return function curried(...args){
		if(args.length >= func.length){
			return func.apply(this, args);
		}
		else {
			return function (...args2){
				return curried.apply(this, args.concat(args2));
			}
		}
	}
}
const sum = (x,y,z) => x+y+z;
const currySum = curry(sum);
console.log(currySum(1)(2)(3);	// 6 출력
```

# Prototype
- 다른 객체로부터 메서드와 속성을 상속받는 메커니즘
- class 없이 상속관계를 구현할 수 있고 실행시점에 동적으로 속성/메소드를 추가/변경 가능
- 모든 JS 객체는 prototype object를 상속받음
- `Object.create([객체])` - 객체를 prototype에 생성
```javascript
function Animal(name) {
	this.name = name;	// 생성자 this > 빈객체
}
Animal.prototype.speak = function() {
	console.log(this.name + ' makes a noise.');
};
function Dog(name) {
	Animal.call(this, name);		// Dog의 this를 Animal의 this로 설정하고 Animal(name) 실행 > call() 참고
	// let dogPrototype = Object.create(Animal.prototype);
	// return dogPrototype;		// Dog.prototype = Object.create(Animal.prototype); 와 동일
}
Dog.prototype = Object.create(Animal.prototype);	// Animal의 prototype을 Dog의 prototype에 복사
Dog.prototype.constructor = Dog;		// 생성자 설정
Dog.prototype.speak = function() {		// class 의 override
	console.log(this.name + ' barks.');
};
```
- [참고 - this](#this)
- [참고 - call()](#bind-call-apply)

# Scope
- var : 함수레벨 스코프(함수를 벗어나면 사용불가)
- let/const : 블록레벨 스코프({}를 벗어나면 사용불가)

# this
- method : 해당 객체
- function : window 객체
- lambda function : 상위 [Scope](#scope)의 this
- constructor(생성자)  함수 : 빈 객체

# bind call apply
- 원본의 this는 변경X > call, apply 는 1회성, bind 는 변수에 저장해야 재사용 가능
- `[function].bind([this 매개변수])` - function 안의 this를 this 매개변수로 설정 (별도의 실행 필요)
- `[function].call([this 매개변수], [매개변수], [매개변수], ...)` - function 안의 this를 this 매개변수로 설정한 후 실행
- `[function].apply([this 매개변수], [매개변수 array])` - call 에 필요한 매개변수가 여러개일 때 배열로 매개변수 할당가능
```javascript
const temp = {
	tempKeys: ['value1', 'value2'],
	tempF(){
		console.log(this);
	}
	tempTest(){
		this.tempKeys.forEach(function(key){	// 여기서의 this는 temp
			console.log(`this.tempKeys`);	// 여기서의 this는 window
		})
	}
	tempForEachTest(){
		this.tempKeys.forEach(function(key){	// 여기서의 this는 temp
			console.log(`this.tempKeys`);	// 여기서의 this는 forEach의 두번째인자
		}, {tempKeys: ['value in foreach1', 'value in foreach2']});
	}
	tempLambdaTest(){
		this.tempKeys.forEach((key) => {	// 여기서의 this는 temp
			console.log(`this.tempKeys`);	// 여기서의 this는 temp
		}, {tempKeys: ['value in foreach1', 'value in foreach2']});
	}
}
function tempFunction(){
	console.log(this);
}
function tempClass(){
	console.log(this);
}
function tempCall(city, country){
	console.log(this + city + country);
}
const bindFunc = tempCall.bind(temp);	// temp를 this 로 지정, 실행X
bindFunc('abc', 'def');		// temp, abcdef 출력
tempCall.call(temp, 'abc', 'def');	// temp, abcdef 출력 (.call(temp))
// tempCall.apply(temp, ['abc', 'def']);	// temp, abcdef 출력 (.call(temp))
tempCall('abc', 'def');		// window 출력

temp.tempF();		// temp 출력
temp.tempTest();		// undefined 출력 (window에는 tempKeys가 없기 때문)
temp.tempForEachTest();	// ['value in foreach1', 'value in foreach2'] 출력
temp.tempLambdaTest();	// ['value1', 'value2'] 출력

tempFunction();		// window 출력
const audio = new tempClass();// {} 출력
```
- [참고 - forEach](#foreach)

# Hoisting
- JS 인터프리터는 변수생성의 선언, 할당 단계를 분할함
- var : 선언부분은 코드가 실행되기 전에 현재 범위의 맨 위로 호이스팅되고 undefined 값이 초기화됨
- let/const : 선언부분은 코드가 실행되기 전에 현재 범위의 맨 위로 호이스팅되지만 초기화 안됨
- function
	- 함수 선언문 : hoisting 됨 (할당 전에 사용 가능)
	<br/>function fName(){}
	- 함수 표현식 : hoisting 안됨 (할당 전에 사용 불가능)
	<br/>let functionValue = function(){}
```javascript
console.log(varValue);	// undefined 출력
console.log(letValue);	// error
abc();			// abc 출력
var varValue = "hello";
let letValue = "hi";
function abc() {
	console.log("abc");
}
```

# Function
- IIFE (Immediately Invoked Function Expression) : 정의되자마자 즉시실행
	- `( function(){} )()`
	- 변수를 전역으로 선언하는것을 피하기 위해 사용
	- 함수 내부에 다른변수들이 접근하는 것을 막기 위해 사용
```javascript
const increment1 = ()=>{
	let counter = 0;
	console.log(counter);
	const number = (num) =>{ console.log(`${num}`};
	return () =>{ counter++; number(counter); }
};
const increment2 = (()=>{
	let counter = 0;
	console.log(counter);
	const number = (num) =>{ console.log(`${num}`};
	return () =>{ counter++; number(counter); }
})();	// 바로실행 > console.log(counter) 출력 > 0
increment1();	// console.log(counter) 출력 > 0 
increment1();	// console.log(counter) 출력 > 0 
increment2();	// console.log(`${num}`) 출력 > 1
increment2();	// console.log(`${num}`) 출력 > 2 (closure로 인해 let counter 기억)
```
- [참고 - Closure](#closure)

# Generator
- `function\* [functionName] (){yield [value1]; yield [value2]; yield [value3];...}` - 호출할 때마다 다음 value를 반환함 
- 사용자의 요구에 따라 다른 시간 간격으로 여러 값을 반환할 수 있음
- 사용자의 요구에 따라 일시적으로 정지될 수 도 있고 다시 시작할 수도 있음
```javascript
function* generatorFunction(){
	yield 1;
	yield 2;
	yield 3;
}
const number = generatorFunction();	//Iterator 를 반환함
console.log(number.next());		// {value: 1, done: false}
console.log(number.next());		// {value: 1, done: false}
console.log(number.next());		// {value: 1, done: false}
console.log(number.next());		// {value: undefined, done: true}
```
- [참고 - Iterator](#iterator)

# Type
- `typeof [value]` - value의 타입 가져옴
```javascript
let value = false;
console.log(value);	// boolean 출력
```

# Symbol
- `Symbor('[text]');
- 유니크한 식별자를 위해 사용 (보이는게 같더라도 다른 값을 가짐)
- Symbol.for() : 같은 text를 가지면 같은 값을 가짐
- Symbol.keyFor() : Symbol.for()를 이용해 전역 Symbol을 만들때(찾을때) 사용하는 text를 얻을 수 있음
- for in, getOwnPropertyNames에서 제외됨
```javascript
let carA = {
	id: 1,
}
const idSym = Symbol('id');
const idSym2 = Symbol('id');
const idSym3 = Symbol.for('id');
const idSym4 = Symbol.for('id');

console.log(idSym===idSym2);	// false
console.log(idSym3===idSym4);	// true

console.log(Symbol.keyFor(idSym3);	// id 출력

carA[idSym] = 300;
console.log(carA);				// {id:1, Symbol(id): 300} 출력

for(const key in carA) {
	console.log(key);			// id 만출력
}
console.log(Object.getOwnPropertyNames(carA));	// (1) ['id'] 출력
```

# Iterable
- 반복 가능한것 (for of 이용가능하거나 [Symbol.iterator] () 이 값을 가지는 것)

# Iterator
- 반복자 (next()를 호출해서 {value: , done: } 두개의 속성을 가지는 객체를 반환하는 객체
```javascript
const set = new Set([1,2,3,4]);
console.log(set[Symbol.iterator]());	// SetIterator {1,2,3,4} 출력	> 값을 가지므로 set은 Iterable 함

const map new Map([['a', 1], ['b', 2]]);

console.log(set[Symbol.iterator]().next());		// {value: 1, done: false}
console.log(map[Symbol.iterator]().next());	// {value: Array(2), done: false}


function tempIterator(array){
	let nextIndex = 0;
	return {
		next: function() {
			return nextIndex < numbers.length ?
			{value: numbers[nextIndex++], done: false}
			: {value: undefined, done: true}
		}
	}
}
const tempArray = [1,2,3];
const arrayIterator = tempIterator(tempArray);
console.log(arrayIterator.next());	// {value: 1, done: false}
console.log(arrayIterator.next());	// {value: 2, done: false}
console.log(arrayIterator.next());	// {value: 3, done: false}
console.log(arrayIterator.next());	// {value: undefined, done: true}
```
## Symbol Iterator
-  `\*[Symbol.iterator]()`
```javascript
// Iterable 한 class
class Model {
	constructor(value = {}){
		this._value = value;
	}
	get(k){
		return this._value[k];
	}
	set(k, v){
		this._value[k] = v;
		return this;
	}
}
class Collection{
	constructor(models = []){
		this._models = models;
	}
	at(index) {
		return this._models[index];
	}
	add(model){
		this._models.push(model);
		return this;
	}
	*[Symbol.iterator](){	// Generator
		for(const model of this._models){
			yield model;
		}
	}
}

const collection = new Collection();
collection.add(new Model({id:1, name: 'A'}));
collection.add(new Model({id:2, name: 'B'}));
collection.add(new Model({id:3, name: 'C'}));
console.log([...collection]);			// [{id:1, name: 'A'}, {id:2, name: 'B'}, {id:3, name: 'C'}]
```

# Document
- Node : Element, text, 주석 모두 포함
- Element : 태그를 사용해 작성된 Node
- `document.querySelector('#[id] / .[class] / [tag]')` - id/class/tag 로 element 접근
- `document.createElement('[tag]')` - tag로 element 생성
- Node
	- `.previousSibling` - 이전 형제노드
	- `.nextSibling` - 다음 형제노드
	- `.appendChild(node)` - 자식노드의 마지막에 node 추가
	- `.removeChild(node)` - 자식노드에서 node 제거
	- `.replaceChild(newNode, removeNode)` - 자식노드의 removeNode를 newNode 로 변경
```javascript
let idElement = document.querySelector('#[id]');
let classElement = document.querySelector('.[class]');
let tagElement = document.querySelector('li');
let newElement = document.createElement('div');

idElement.childNodes;	// 배열이 아닌 반복가능한 collection
idElement.firstChild;		// childnodes[0]
idElement.lastChild;		// childnodes[lastIndex]
idElement.previousSibling;	// 이전형제
idElement.nextSibling;	// 다음형제

idElement.parentElement;	
idElement.children;		
idElement.firstElementChild;
idElement.lastElementChild;
idElement.previousElementSibling;
idElement.nextElementSibling;
```
# Event
- Event Capturing > Event Target > Event Bubbling 순으로 동작
- Event Capturing : event 발생 요소로부터 최하단요소까지 순서대로 event가 전달되는 것
	- 기본적으로는 보여지지 않음
	- `element.addEventListener('[event name]', ()=>{}, [{capture: true} / true])` 지정시 Capturing 보여짐
- Event Bubbling : event 발생 요소로부터 최상위요소까지 순서대로 event가 전달되는 것
	- event.stopPropagation() 실행 시 Bubbling 안됨
- Event Delegate : 하위요소의 Event를 상위요소에서 제어
	- Event Bubbling 으로 인해 하위요소의 Event가 상위요소에 전달되기때문에 상위요소에서 한번에 제어
- `element.addEventListener('[event name]', ()=>{});` - element 에 eventListener 부여 
- UI Event
	- load : 문서나 객체가 로드완료될 때
	- change : 객체가 focus 잃거나 변동되었을 때
	- resize : 객체 크기 바뀌었을 때
	- scroll : 스크롤바 조작할 때
	- error : 에러 발생 시
- Keyboard Event
	- keydown / keyup / keypress : 누를때 / 뗄때 / 누른문자가 입력될때
- Mouse Event
	- click / dblclick : 클릭 / 더블클릭
	- mouseover / mouseout : 객체안으로 들어올때 / 객체밖으로 나갈때
	- mousedown / mouseup : 누를때 / 뗄때
	- mousemove : 움직였을 때
- Focus
	- focus / blur : 객체에 focus 될때 / 객체가 focus 잃을 때
- Form
	- input : 요소값이 입력될 때
	- change : 요소값이 변경될 때
	- select : 선택했을 때
	- reset / submit : 리셋 / 폼제출
	- cut / copy / paste : form을 잘라내기 / 복사 / 붙여넣기 했을 때
```javascript
// element에 focus 될때마다 hadleEvent 동작
element.addEventListener('focus', handleEvent);
function handleEvent(e) {
	e.preventDefault();
	...
}
```

# copy
- 얕은복사 : 최초깊이의 값만 새롭게 생성, 두번째 깊이부터 같은객체를 공유함
- 깊은복사 : 모든 부분을 새롭게 생성
```javascript
let obj = {
	a:1,
	b:2,
	c:3,
	d: {
		x:10,
		y:20,
		z:30
	}
};
const firstCopy = {...obj};
firstCopy.a = 2;
firstCopy.d.x = 20;
console.log(obj, firstCopy);	// {a:1,b:2,c:3,d: {x:20,y:20,z:30}}, {a:2,b:2,c:3,d: {x:20,y:20,z:30}} 출력

firstCopy.a = 1;
firstCopy.d.x = 10;

const secondCopy = {...obj, d:{...obj.d}};
firstCopy.a = 2;
firstCopy.d.x = 20;
console.log(obj, firstCopy);	// {a:1,b:2,c:3,d: {x:10,y:20,z:30}}, {a:2,b:2,c:3,d: {x:20,y:20,z:30}} 출력
```

# Intersection Observer
- 무한 스크롤에 사용됨
- `let observer = new IntersectionObserver(entries =>{entries.forEach(entry=>{});}, {root: [null / element], threshold: [int]}`
- `observer.observe([element])` - element를 관찰할 대상으로 추가
	- entries : .observe로 지정된 element 들
		- entry.boundingClientRect :
		- entry.intersectionRatio : 0~1 (root와 얼마나 겹치는지)
		- entry.intersectionRect : 
		- entry.isIntersecting : intersectionRatio가 threshold 이상일때(타겟과 설정한 threshold 이상 겹칠때) true
		- entry.isVisible : 
		- rootBounds : 
		- target : 관찰하는 대상
		- time : 
	- root : null 일 경우 뷰포트(보이는 화면)를 기준으로, element 지정시 element기준으로 판단
	- threshold : 0~1 (함수가 동작하기 위해 root와 target이 겹치는 정도 설정)
```javascript
// 끝에 다다를때마다 10개씩 추가생성
let newCount = 10;
let itemIndex = 0;
const observer = new IntersectionObserver(entries =>{
	entries.forEach(entry=>{
		const list = document.querySelector('.list');
		if(entry.isIntersecting){	// 관찰대상이 root와 threshold만큼 겹쳤을 때 동작
			for(let i=itemIndex; i<itemIndex + newCount;i++){
				let item = document.createElement('p');
				item.textContent = i;
				item.className += 'item';
				list.appendChild(item);
			}
			itemIndex = itemIndex + newCount;
		}
	}
}, {root: null, threshold: 1});	// 뷰포트와 완전히 겹칠때 동작
observer.observe(document.querySelector('.end'));	//end class 를 가진 element target에 추가
observer.observe(document.querySelector('.img'));	//img class 를 가진 element target에 추가
```

# reduce
- `[array].reduce(function(accumulator, currentValue, currentIndex, array){ }, [초기값])` : 함수 실행 후 하나의 결과값 반환
	- accumulator : 누적계산
	- currentValue : 현재 값
	- currentIndex : 현재 index 
	- array : 원본배열
```javascript
[0,1,2,3,4].reduce(function (acc, current, currentIndex, array){
	console.log(acc, current, currentIndex, array);
	//10,0,0,[0,1,2,3,4]
	//10,1,1,[0,1,2,3,4]
	//11,2,2,[0,1,2,3,4]
	//13,3,3,[0,1,2,3,4]
	//16,4,4,[0,1,2,3,4]
	return acc+current;	
}, 10);	//10+0+1+2+3+4 = 20 반환
```

# take
- `Iterator.prototype.take([int])` - int 만큼만 반복가능한 Iterator 반환
- 아주 큰 배열의 일부만 사용하고싶을 때 속도측면에서 효율적임
```javascript
function* fibonacci() {
  let current = 1;
  let next = 1;
  while (true) {
    yield current;
    [current, next] = [next, current + next];
  }
}

const seq = fibonacci().take(3);
console.log(seq.next().value);	// 1
console.log(seq.next().value);	// 1
console.log(seq.next().value);	// 2
console.log(seq.next().value);	// undefined
```
- [참고 - Generator function\*](#generator)
- [참고 - Iterator](#iterator)



# Loop
- [참고 - 반복문관련 비동기처리](#asynchronous-loop)
```javascript
var o = [1,2,3];
o.sophie = 'dophie';
console.log(o); // [1,2,3, {sophie: 'dophie'}]
```

## forEach
- `[array].forEach((value, index, array)=>{})`
- `[array].forEach((value, index, array)=>{}, thisArray)`
	- thisArray : forEach 내 함수에서 [this](#this) 참조 시 thisArray 참조
	```javascript
	const temp = {
		tempKeys: ['value1', 'value2'],
		tempF(){
			console.log(this);
		}
		tempTest(){
			this.tempKeys.forEach(function(key){	// 여기서의 this는 temp
				console.log(`this.tempKeys`);	// 여기서의 this는 window
			})
		}
		tempForEachTest(){
			this.tempKeys.forEach(function(key){	// 여기서의 this는 temp
				console.log(`this.tempKeys`);	// 여기서의 this는 forEach의 두번째인자
			}, {tempKeys: ['value in foreach1', 'value in foreach2']});
		}
		tempLambdaTest(){
			this.tempKeys.forEach((key) => {	// 여기서의 this는 temp
				console.log(`this.tempKeys`);	// 여기서의 this는 temp
			}, {tempKeys: ['value in foreach1', 'value in foreach2']});
		}
	}
	temp.tempF();		// temp 출력
	temp.tempTest();		// undefined 출력 (window에는 tempKeys가 없기 때문)
	temp.tempForEachTest();	// ['value in foreach1', 'value in foreach2'] 출력
	temp.tempLambdaTest();	// ['value1', 'value2'] 출력
	```
	- [참고 - this](#this)
- 배열순회
```javascript
o.forEach((value, index, array)=>{
	console.log(`${index} : ${value}`);
	console.log(array);	//(3) [1, 2, 3] 출력
});
// 0 : 1
// (3) [1, 2, 3]
// 1 : 2
// (3) [1, 2, 3]
// 2 : 3
// (3) [1, 2, 3]
```

## for in
- `for (const e in [array]){}`
- key값을 순환
- 배열, 객체 모두 사용 가능
- `순환하는 객체가 다른 객체를 상속받았을 경우 상속에 사용된 객체의 프로퍼티까지 포함해 반복문을돌아 예상치못한 결과가 나올 수 있음`
```javascript
for (const e in o) {
    console.log(e); // 0,1,2,'sophie'
}

const objects = { a: 1, b: 2, c: 3 };
for (const object in objects) {
	console.log(object); // a,b,c
}
```
## for of
- `for (const e of [array]){}`
- value값을 순환
- 배열만 사용 가능
```javascript
for (const a of o) {
    console.log(a); // 1,2,3
}
```

## map
- `let newArray = [array].map((value, index)=>{});` - map 내에서 this 시 상위scope의 this 접근
- `let newArray = [array].map(function(value, index){ }, [this 매개변수]);` - map 내에서 this 시 this 매개변수 접근
- 배열 순회 후 새 배열 반환
- [참고 - this](#this)

## 결론
- 단순반복 : for
- 배열순회 : forEach
- 배열 순회 후 새 배열 : map



# Asynchronous
## Promise
비동기 처리에 활용되는 객체

- pending(대기)
	- Promise 객체 생성 시 대기상태
	- resolve(실행), reject(실패) 인자를 가진다.
- fulfilled(이행)
	- resolve() 호출 시 이행
	- .then() 을 통해 비동기처리 가능
- rejected(실패)
	- reject() 호출 시 실패
	- .catch() 를 통해 예외처리 가능

### Promise all
- 매개변수로 주어진 비동기 작업이 모두 처리 된 후 다음작업 진행
- 매개변수로 주어진 작업은 각각은 비동기처리 작업이지만 전체는 동기처리함
- 최종적으로 각각의 작업결과값의 배열이 반환됨
- 매개변수로 주어진 작업이 하나라도 실패하면 reject
```javascript
Promise.all([promise1, promise2, promise3])
	.then((values) => {
		console.log(values); // 다 성공하면 [1결과, 3결과, 2결과]
	})
	.catch((error) => {
		console.log(error); // 하나라도 실패하면 실행
	});
```

### Promise allSettled
- 매개변수로 주어진 작업중 실패한 작업이 있어도 resolve
```javascript
Promise.allSettled([promise1, promise2, promise3])
	.then((values) => {
		console.log(values); // [{status: "fulfilled", value: 결과1}, {status: "rejected", reason: Error: an error}, {...}]
	})
	.catch((error) => {
		console.log(error); // 매개변수의 작업 외에 예외 발생한경우 실행
	});
```

### Promise race
- 매개변수로 주어진 비동기 작업 중 가장 먼저 실행이 완료된 것 만 반환함
```javascript
Promise.race([promise1, promise2, promise3])
	.then((values) => {
		console.log(values); // 가장먼저 완료된 작업의 값
	})
	.catch((error) => {
		console.log(error); // 가장먼저 완료된 작업이 reject된 경우
	});
```

## Asynchronous Loop
### async for of
- async, for of : 배열의 요소를 순차적으로 처리

### for await of
- for await of : 배열이 비동기 작업인경우에도 사용가능
```javascript
// async, for of
async function temp() {
	const params = [1, 2, 3, 4];
	const resArray = [];
	for ( const param of params ) {
		// 시간이 각자 다르게 걸리는작업
		resArray.push(param);
	};
	console.log(resArray); // [1,2,3,4];
}

// for await of
const resArray = [];
async function* foo() {
	yield 1;
	yield 2;
}

(async function () {
	for await (const num of foo()) {
		resArray.push(num);
	}
	console.log(resArray); // [1, 2];
})();
console.log(resArray); // [];
```