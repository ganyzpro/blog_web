# js系统

## 数据类型、类型检测转换、拷贝
    
### 类型检测
    
typeof返回类型字符串，可以检测原始类型，引用类型除了函数function都是object。null返回object是bug。

instanceof返回布尔值，判断一个对象是不是另一个的实例，用来判断引用类型。
    
Boolean, Number和String基本包装对象。
        
        var str1 = 'hello world'
        str1 instanceof String // false

        var str2 = new String('hello world')
        str2 instanceof String // true

### 类型转换
    
if()和== 会隐式转换，最后转为数字进行比较。== 和===，先判断类型是否相同。Object.is(a,b)判断两个值是否相等，在===的基础上修复了+0和-0（false）及NaN和NaN（true）。

### 赋值、深浅拷贝

赋址（将一个对象类型的赋给变量）是复制变量指向的地址，基本类型的值改变也会跟着变；
    
浅拷贝拷贝了第一层，基本类型不会跟着变，引用类型还是只复制了引用地址。常用的有assign(),展开...,数组的slice()等。
    
单=的意思是创建或修改变量在内存中的指向。参数传值，传引用类型时是将形参指向实参相同的地址。

## 执行上下文、闭包、this指向

### 执行上下文
js里的可执行代码无非就是全局代码，函数代码，eval代码。主要关注函数代码。

函数作用域为词法作用域（静态作用域），词法作用域就是定义在词法阶段的作用域，是由写代码时将变量和块作用域写在哪里来决定的。查找变量按函数定义的位置通过作用域链查找。
    
执行一个函数的时候会先进行准备工作，创建执行上下文，然后才执行。
    
执行上下文存储在执行栈中，后进先出。首先全局执行上下文被压入执行上下文栈中，初始化一个全局执行上下文栈。所有的函数执行上下文都包在全局执行上下文中，不同函数的调用会不断的压栈弹栈。当一个函数中又调用了另外的函数，则继续压栈，以此类推。

函数内有一个scope属性，用于保存所有的父变量对象。当前函数激活时将自己的活动变量对象添加到scope作用域链的前端，形成完整作用域链。每一个子函数都会拷贝上级的作用域，形成一个作用域的链条。查找变量会一直沿着作用域链往上层查找，直到全局作用域。
    
        checkscopeContext = {
                AO: {
                    arguments: {
                        length: 0
                    },
                    scope: undefined,
                    f: reference to function f(){}
                },
                Scope: [AO, globalContext.VO],
                this: undefined
            }
            
        //VO变量对象全局，AO活动对象函数上下文。
    
变量对象（存储了上下文中定义的函数和变量声明）>执行上下文>执行栈>执行

AO + scope + this > 执行上下文

### 变量、函数提升。
    
变量只是声明提升，值是undefine。函数提升整个提升，有多个同名函数声明时后面的会覆盖。函数会全部提升上去，变量提升声明后执行到赋值操作会重新赋值。function定义函数和var func = function(){}表达式定义是不同的。

### 闭包
闭包是一个函数，可以访问另外一个函数作用域中的变量的函数。MDN 对闭包的定义为：闭包是指那些能够访问自由变量的函数。
    
自由变量，既不是参数也不是函数内的局部变量。正常来说一个普通函数内定义的局部变量，在函数执行完之后就会被内存回收，但是通过闭包的方式不会还可以获取到他的值。
    
通常我们说的闭包即一个函数作为父函数的返回值，这个函数引用了父函数作用域中的变量。

### this（动态，执行时确定）
    
全局上下文和直接调用函数（函数表达式赋值再调用），window/undefined
    
dom事件绑定中指向dom
    
对象.方法，指向对象
    
new构造函数，指向实例对象
    
箭头函数的 this 始终指向函数定义时的 this，而非执行时。箭头函数中没有 this 绑定，必须通过查找作用域链来决定其值，如果箭头函数被非箭头函数包含，则 this 绑定的是最近一层非箭头函数的 this，否则，this为 window。

## 原型链、继承
    
通过_proto_实现原型链继承。

每个对象拥有一个原型对象，通过 __proto__指针指向上一个原型 ，并从中继承方法和属性，同时原型对象也可能拥有原型，这样一层一层，最终指向null。这种关系被称为原型链 (prototype chain)，通过原型链一个对象会拥有定义在其他对象中的属性和方法。
    
        Person.prototype.constructor === Person
        //原型对象有一个constructor属性，定义了一个构造函数，原型对象创建对象时调用这个构造函数.
    
        Person.prototype.constructor === person.constructor  //true
        //当获取person.constructor时，其实person中并没有constructor属性,当不能读取到constructor属性时，会从 person 的原型也就是Person.prototype中读取，正好原型中有该属性
        
## 事件循环

宏任务和微任务。
    
宏任务包括setTimeout等延时任务。微任务有promise.then()等。
    
先执行宏任务，再执行宏任务里的微任务。
    
async函数遇到await时，会直接执行后面的表达式，然后把函数内后面部分的代码加入微任务
    
## V8、内存机制、GC回收机制

栈，堆。
内存回收（标记清除）。局部作用域的变量，函数执行完后就被回收了。闭包还可以通过作用域链访问到。
    
编译器 编译得到二进制文件机器码
    
解释器 将ast转成字节码
    
v8既有解释器也有编译器，两者结合，即时编译。
## DOM事件，事件捕获冒泡委托等
    
域名dns解析得到ip，请求服务器时涉及缓存。
协商缓存last-modified

强制缓存cache-control。
    
小js，图片缓存在memory内存，css，大文件在dist磁盘。请求html后，解析css先引入，js放在尾部。js串行。得到文件后渲染涉及重绘和重排。
    
        querySelector():            // 获取指定节点的第一个
        querySelectorAll():         // 获取符合条件的节点，返回数组
    
所谓事件冒泡就是事件像泡泡一样从最开始生成的地方一层一层往上冒，直至最外层的html或document。
    事件捕获和事件冒泡相反，事件捕获是自上而下执行，我们只需要将addEventListener的第三个参数改为true就行。
    
事件委托，通俗的说就是将元素的事件委托给它的父级或者更外级的元素处理，它的实现机制就是事件冒泡。
## 异步编程
    
setTimeout延时函数

callback，通过往函数最后一个参数传入回调函数的方式。嵌套问题。
    
promise，链式调用。
    
async返回promise。
## webpack
    
在前端工程化开发中，有各种模块的import、export，webpack模块打包工具根据依赖关系将模块打包成相应的bundle。一般涉及babel将ES6转为ES5，生成依赖图谱（一个对象），最后打包生成浏览器可执行的一个js文件。