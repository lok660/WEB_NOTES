1.模板字符串

> 里面可以写js逻辑

```js
const Jelly = {
  name: 'Jelly',
  date: '2020-3-23',
  todos: [
    { name: 'Go to Store', completed: false },
    { name: 'Watch Movie', completed: true },
    { name: 'Running', completed: true }
  ]
}


const template = `
    <ul>
      $${Jelly.todos.map(todo => `<li>${todo.name}</li>`)}
    </ul>
`
```

```js
function highlight (strings, ...values) {
  const hightlighted = values.map(value => `<span>${value}</span>`)

  let str = ''
  strings.forEach((string, i) => str += `${string}${hightlighted[i]}`)
  return str
}

const user = 'Mary'
const topic = 'Learn to user markdown'
const sentence = highlight`${user} has commented on your topic ${topic}`

console.log(sentence)
//	<span>Mary</span> has commented on your topic <span>Learn to user markdown</span>undefined
```

2.新增字符串方法

```javascript
//	startWith(value,index)		以什么开头,后面索引代表从哪开始
//	endWith(value,index)		以什么结束,后面索引代表从哪开始
//	includes(value,index)		包含了什么,后面索引标识从哪开始判断
//	repeat(time)				重复time次
```

3.箭头函数不适合场景

> 箭头函数的this是指向父级作用域,且在词法作用域中,是不会改变的

```javascript
//  1.作为构造函数,一个方法需要绑定到对象
const Person = (name, points) => {
  this.name = name  //  报错,箭头函数没有this
  this.points = points
}
const Jelly = new Person('jelly', 5)

Person.prototype.updatePoints = () => {
  console.log(this)   //  此时的this指向window
  this.points++
  console.log(this.points)
}

//  2.当你真的需要this的时候
const button = document.querySelector('.zoom')
button.addEventListener('click', () => {
  console.log(this)   //  button
  this.classList.add('in')
  setTimeout(() => {
    //  计时器中必须使用箭头函数,因为计时器中的this是window
    this.classList.remove('in')
  }, 200)
})

//  3.需要使用arguments对象
const sum = () => {
  return Array.from(arguments).reduce((prevSum, value) => prevSum + value, 0)
}

sum(1, 2, 3)  //  arguments is not defined
```

4.对象解构

```javascript
const Tom= {
    family:{
        mother:'Norah Jones',
        father:'Richard Jones',
        sister:0
    }
}
//	只有明确这个只没有,为undefined的时候默认值才会生效,否则不会(false,0,null)
const {father:f,mother:m,sister='have no sister'} = Tom.family
```

5.for...of

> 除Object之外都支持(Array,map,String,arguments)

```js
//	for...in
const fruits = ['A', 'B', 'C']
fruits.describe = 'D'

for (const index in fruits) {
  console.log(fruits[index])  //  A,B,C,D 原型中的属性也会遍历
}

//  for..of可以打断循环,且不会遍历原型上的属性
for (const fruit of fruits) {
  if (fruit === 'C') {
    break
  }
  console.log(fruit)  //  A,B
}
```

``` js
//	能够获取 值和索引值
const fruits = ['A', 'B', 'C', 'D']

for (const [index, fruit] of fruits.entries()) {
  console.log(`${fruit} ranks ${index = 1}`)
}
//	能遍历字符串
let name ='Laravise'
for (const char of name){
    console.log(char)	//	L a r a v i s e
}
```

6.Array

> from

```js
// from不是在原型上的方法,所以只能Array.from
const todos = document.querySelectorAll('li')
//	第二参数相当于map方法对每个元素进行操作
const names = Array.from(todos, todo => todo.textContent)

const website = 'Laravise'
console.log(Array.from(website))  //  L a r a v i s e
```

> of

```js
// 传入参数不同,输出值也不同
const arr1 = new Array(7)         //  [undefined x 7]
const arr2 = new Array(1, 2)      //  [1,2]

const arr3 = Array.of(7)          //  [7]
const arr4 = Array.of(1, 2, 3)    //  [1,2,3]
```

7.Class

> 与构造函数不同,类如果在声明之前使用,不会存在变量提升,而是会报错
>
> 只能通过new调用

```js
class User {
  // 类构造方法
  constructor(name, email) {
    this.name = name
    this.email = email
  }
  // 类方法
  info () {
    console.log('hello world')
  }
  // 静态方法只能通过类名.调用
  static description () {
    console.log('im a user of codecasts.com')
  }
  set github (value) {
    this.githubName = value
  }
  get github () {
    return `github.com/${this, githubName}`
  }
}
```

```js
class Dog extends Animal {
  constructor(name, age) {
    super(name)
    this.age = age
  }

  bark () {
    console.log('Barl bark!')
  }
}

const lucky = new Dog('lucky', 2)
```

8.遍历器

```js
//	重写Array.values方法
Array.prototype.values = function () {
  let i = 0
  let items = this

  return {
    next () {
      const done = i >= items.length
      const value = done ? undefined : items[i++]
      return {
        value, done
      }
    }
  }
}

const colors = ['red', 'blue', 'green']
```

9.Proxy

> 保护或是说预处理,判断方法是否符合你的逻辑,或者你可以手动改变,从而存入对象中

```js
const person = { name: 'laravise', age: 2000 }

const personProxy = new Proxy(person, {
  // 获取属性并且将值转化为大写
  get (target, key) {
    return target[key].touPPerCase()
  },
  // 设置属性的时候去除空格
  set (target, key, value) {
    if (typeof value === 'string') {
      target[key] = value.trim()
    }
  }
})

personProxy.name = 'codecasts'
```

```js
const phonerHandler = {
  // 设置属性的时候先去掉空格
  set (target, key, value) {
    target[key] = value.match(/[0-9]/g).join('')
  },
  get (target, key) {
    return target[key].replace(/(\d{3})(\d{4})(\d{4})/, '$1-$2-$3')
  }
}

const phonerHandler = new Proxy({}, phonerHandler)

// 设置
phonerHandler.number = '136 4799 1457'  //  13647991457

//  获取
phonerHandler.numer   //  136-4799-1457
```

