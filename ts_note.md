# TypeScript

## 基础

类型

```tsx
let v1:string = 'abc'
let v2:number = 2
let v3:boolean = true
let nu:null = null
let un:undefined = undefined
let v4:string | null = null
let anyType:any = 123
let obj1:object = {name:"test",age:2}
```

array设定

```tsx
let arr1:number[] = [1,2,3]
let arr2:Array<string> = ['a','b']
let t1:[number,string,number?] = [1,'a']

// tuple元祖:已知数量已知类型
let tupleArr:[number,string,boolean]
rupleArr = [12,'333',true]
```

枚举

```tsx
enum MyEnum {
A,
B,
C
}
let e:MyEnum = MyEnum.A
```

void

```tsx
function hwFn():void{
	console.log("hello world")
}
function myFn(a:number,b?:string,c=10,d?:boolean,...rest:number[]):string{
	return a+b
}
```

断言 (只有很肯定才能这么写，因为有可能为空)

```tsx
let numArray = [1,2,3]
const result= numArr.find(item⇒item>2) as number
result * 5
```

never: 任何类型的子类型，不会出现的值

常用于无尽运行的功能，和switch fn里拿到不在范围里的值之后的报错。

```tsx
let a:never = (()=>{
	throw new Error('wrong')
})()
```

interface

```tsx
interface Obj {
	name:string,
	age:number,
	readonly isMale:boolean,
	say:(words:sgring) => string,
	[propName:string]:any 
}
// 除了一下设定还继承父
interface Son extends Father {
	name:string,
	age:number
}
```

type

```tsx
type MyUserName = string | number
```

泛型

```tsx
function myFn<T>(a:T,b:T):T[] {
	return [a,b]
}
myFn<number>(1,2)
myFn("a","b")
```

交叉类型 and

```tsx
function extend<T,U>(first:T,second:U):T&U {
	let result:<T&U> = {}
	for(let key in first){
		result[key] = first[key]
	}
	for(let key in second){
		if(result.hasOwnProperty(key)){
			result[key] = second[key]
		}
	}
	return result
}
```

联合类型 or

let a: string[] | string

类型索引

```tsx
interface Button {type:string,text:string}
type ButtonKeys = keyof Button // 'type' | 'text'
```

类型约束

```tsx
type BaseType = string | number
function copy<T extends BaseType>(arg:T):T{return arg}

// 和索引一起用
function getValue<T,K extends keyof T>(obj:T,key:K){
	return obj[key]
}
```

类型映射

```tsx
type Readonly<T> = {
	readonly [P in keyof T]:T[P]
}
interface Obj {a:string,b:string}
type ReadOnlyObj = Readonly<obj>{
}
```

类

```tsx
class Animal {
	move(distance:number=0){
		console.log("move",distance)
	}
}
class Dog extends Animal {
	move(){
		super.move(1)
		console.log("dog move")
	}
}
const dog = new Dog()
dog.move(10)
```

修饰符

public 自由访问

private 内部使用

protect 内部访问+子类中访问

```tsx
class Father {
	protected name:string
	constructor(name:string){
		this.name = name
	}
}
class Son extends Father{
	say(){
		console.log(this.name)
	}
}
```

抽象类

extends里可以用，但是本身不会用

```tsx
abstract class Animal {
	abstract makeSound():void;
	move():void{
		console.log("move")
	}
}

// props类型例子
export default class XXX from React.Component<Props,State>
export default class Props {
	public children:Array<React.ReactElement<any>> | React.ReactElement<any> | never[]=[]
	public speed:number:500
	public afterChange:()=>{}
	
}
```

枚举类型 enum

```tsx
// 数字
enum Direction {
	UP = 10
	DOWN, //11
	LEFT, //12
	RIGHT, //13
}
Direction.UP === 10

//字符串
enum Dir {
	UP = 'up',
	DOWN = 'down',
	LEFT = 'left',
	RIGHT = 'right',
}
// 异构
enum test {
	NO = 0,
	YES = 'yes',
}

// 常量枚举
// 变异阶段被删除
const enum Color {Red, Green, Blue} // JS: Color[Color['Red']=0]="Red"
const red = Color.Red // JS: const red = 0
```

动态接口

```tsx
interface DynamicObject{
  [key: string]: number | string;

*// 允许任意属性名，但属性值必须为 number 或 string 类型*

}
```

**泛型约束中的 keyof**

```tsx
function getProperty<T, K extends keyof T>(obj: T, key: K) {
  return obj[key];
}
let x = { a: 1, b: 2, c: 3 };
let a = getProperty(x, "a"); // 正确
console.log(a)
```

## 面试题

TypeScript高频面试题及解析 ****[https://juejin.cn/post/7321542773076082699](https://juejin.cn/post/7321542773076082699)

### const vs readonly

const 防止改值

readonly 防止改属性

### type vs interface

两个都可以描述类型，interface可以用于基本类型，联合类型，元祖

共同点：描述对象或函数，允许拓展

不同点：

- type 基本类型，联合类型，元祖（Tuple限定长度和类型的array）
- type可用typeof获取类型
- 多个相同的interface可以合并

### any作用

不清楚当前类型，值来自动态内容或者第三方库

### any,never,unknown,null,undefined,void

any不查类型

never永远不存在的值的类型，抛出异常，不会返回值的函数

unknown 任何类型都可以， unknown只能赋值给unknown和any

null, undefined 所有类型子类型。strictNullChecks标记null或者undefined只能赋值给void或者他们自己

void没有任何类型，函数没有返回值。

### interface可以给function/array/class(indexable)做声明吗

可以

```tsx
// fn
interface Say{
	(name:string):void;
}
let say:Say = (name:string):void=>{}
// array
interface NumArray{[index:number]:number;}
let list:NumArray = [1,2,3,4]
// class
interface Person {
	name:string
	sayHi(name:string):string
	
}
```

### 可以用string, number, boolean, symbol, object给类型做声明吗

可以

### this在TS和JS的区别

TS: noImplicitThis: true 必须声明this类型才能用

TS箭头函数

### 使用union types注意事项

只能访问共有属性或者方法，比如说string和number可以用toString但不能用length

### 如何联合枚举类型的key

```tsx
enum str{A,B,C}
type strUnion = keyof typeof str //'A'|'B'|'C'
```

### 模块加载机制

1. 模块文件
2. 外部模块生命.d.ts
3. 报错

### 兼容性 type campatibility

any可以做不正确的行为，比如string当number用

结构匹配所以名字不重要。但只能多不能少

```tsx
// example1
interface Point {
  x: number;
  y: number;
}

class Point2D {
  constructor(public x: number, public y: number) {}
}

let p: Point;

// ok, 因为是结构化的类型
p = new Point2D(1, 2);
// example2
interface Point2D {
  x: number;
  y: number;
}

interface Point3D {
  x: number;
  y: number;
  z: number;
}

const point2D: Point2D = { x: 0, y: 10 };
const point3D: Point3D = { x: 0, y: 10, z: 20 };
function iTakePoint2D(point: Point2D) {
  /* do something */
}

iTakePoint2D(point2D); // ok, 完全匹配
iTakePoint2D(point3D); // 额外的信息，没关系
iTakePoint2D({ x: 0 }); // Error: 没有 'y'
```

函数兼容的时候X=Y时，X能包含的参数必须大于等于Y的。

### 对象展开副作用 object spread

1. 后面属性覆盖前面属性
2. 进包含可枚举的属性，不可枚举属性丢失。

```tsx
const obj1 = { a: 1, b: 2 };
const obj2 = { c: 3, d: 4 };

const combined = { ...obj1, ...obj2, e: 5 };

// combined 结果: { a: 1, b: 2, c: 3, d: 4, e: 5 }
```

### 全局申明和局部申明

带Import export的是局部，否则全部

### 如何引入并识别js的npm包

1. 选择ts版本`npm install @types/XXX —save`
2. 没有，需要编写同名的.d.ts。里面包含`*declare* *module* "my-untyped-module"`
3. tsconfig里要include这个文件

### tsconfig

```tsx
{
	"files":[], // 文件相对或者绝对路径
	"include":[], // 编译某些文件 
	"exclude":[], 
	"compileOnSave":false,
	"extends":"", // 继承其他的tsconfig
	"compilerOptions":{
		"paths":{
			"@utils/*":["src/utils/*"]
		}
	}
}
```

### declare vs declare global

declare 定义全局变量，全局函数，全局命名空间, js, modules, class等

declare global 全局对象window增加新属性

### exclude, omit, merge, intersection,overwrite

Exclude<T,U> 从T中排除可以给U的元素

Omit<T,K> 忽略T中某些元素

Merge<01,02>将两个对象属性合并

Overwrite<T,U> U属性替代T属性

Intersection<T,U>取TU都有的属性

## React

```tsx
import React from 'react'
interface UserMsg {name:string,message:string}
const Message:React.FC<UserMsg>=({name,message})=>{
	return (<p>{name}: {message}</p>)
}
export default Message
```

useState

```tsx
import React,{useState,useEffect} from 'react'
import Message from "./Messsage"
import {UserContext} from "./UserContext"

const App:React.FC<UserMsg>=({name,message})=>{
	const [userName,setUserName]=useState<string>('User')
	const [userMsg,setUserMsg]=useState<string>('Message')
	useEffect(()=>{
		const timer = setTimeout(()=>{
			setUserName("May")
			setUserMsg("Updated Message")
			},5000)
		return ()=>clearTimeout(timer)
	},[])
	return (
		<UserContext.Provider value={{name:userName,message:userMsg}}>
			<h1>Hello World</h1>
			<Message name={userName} message={userMsg} />
		</UserContext.Provider>
	)
}
export default Message
```

context

```tsx
import React from "react"
interface UserContextType{
	name:string,
	message:string
}
export const UserContext = React.createContext<UserContextType | undefined>(undefined)
```

```tsx
import React from 'react'
import {UserContext} from "./UserContext"

interface UserMsg {name:string,message:string}
const Message:React.FC = () =>{
	const context = useContext(UserContext)
	if(!context){
		throw new Error("Missing Context")
	}
	return (<p>{context.name}: {context.message}</p>)
}
export default Message
```

```tsx
import {}
```
