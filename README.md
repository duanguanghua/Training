# Training
# 前端原生开发培训文档
### [ES6语法特性](http://es6.ruanyifeng.com/)
JavaScript是一门直译式脚本语言，js编写的代码不需要进行预编译，经内置于浏览器的JavaScript引擎解释即可运行。ECMAScript 6.0（以下简称 ES6/ES2015）是 JavaScript 语言的下一代标准，已经在 2015 年 6 月正式发布了。它的目标，是使得 JavaScript 语言可以用来编写复杂的大型应用程序，成为企业级开发语言。目前各大浏览器的最新版本，对 ES6 的支持可以查看[kangax.github.io/compat-table/es6/](kangax.github.io/compat-table/es6/)。对于不支持ES6语法的浏览器，Babel 是一个广泛使用的 ES6 转码器，可以将 ES6 代码转为 ES5 代码，从而在现有环境执行。
#### ES6的主要特性如下：

+ ##### let 命令和const 命令
  + let的用法类似于var，但let只在let命令所在的代码块内有效，在代码块之外使用会报错，很合适在for循环中使用let命令。
      ```
        {
           var a = 10;
           let b = 1;
        }
        console.log('a',a);
        console.log('b',b);
      ```
  + let不存在变量提升，即变量是不可以在声明之前使用，会报错，var声明变量是存在变量提升的，值为undefined。
      ```
        console.log(foo);
        var foo = 2;
        
        console.log(bar);
        let bar = 2;
      ```
  + let存在暂时性死区，只要块级作用域内存在let命令，它所声明的变量就“绑定”这个区域，不再受外部的影响。暂时性死区的本质就是，只要一进入当前作用域，所要使用的变量就已经存在了，但是不可获取，只有等到声明变量的那一行代码出现，才可以获取和使用该变量。
      ```
        var tmp = 123;

        if (true) {
          tmp = 'abc'; 
          let tmp;
        }
      ```
  + let实际上为 JavaScript 新增了块级作用域，各个块级作用域之间的同名变量互不影响。内层块级作用域可以使用外层块级作用域的变量，外层不可访问内层的变量。ES6明确允许在块级作用域之中声明函数，且函数声明语句的行为类似于let，在块级作用域之外不可引用。
      ```
        function f1() {
          let n = 5;
          if (true) {
            let n = 10;
            console.log('n1', n);
          }
          console.log('n2', n);
        }
        
        function f2() {
          let n = 5;
          if (true) {
             n = 10;
             let t = 20;
             console.log('n1',n);
             console.log('t1', t);
          }
          console.log('n2',n);
          console.log('t2', t);
        }
        
        function f3() { console.log('I am outside!'); }

        (function () {
          if (false) {
            // 重复声明一次函数f3
            function f3() { console.log('I am inside!'); }
          }
        
          f3();
        }());
      ```
      *报错的原因：为了减轻对老代码产生的不兼容问题，浏览器的实现可以不遵守上面的规定，有自己的行为方式允许在块级作用域内声明函数。函数声明类似于var，即会提升到全局作用域或函数作用域的头部。同时，函数声明还会提升到所在的块级作用域的头部。*
      *上面三条规则只对 ES6 的浏览器实现有效，其他环境的实现不用遵守，还是将块级作用域的函数声明当作let处理。*
  + 如果代码块中存在let和const命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。变量只在代码块内有效，凡是在声明之前就使用或重复声明这些变量，就会报错。
      ```
        if (true) {
          let tmp = 'abc'; 
          let tmp = '123';
        }
      ```
  + const声明一个只读的常量。const一旦声明变量，就必须立即初始化，不能留到以后赋值，常量的值在声明且赋值之后就不能改变。const声明的常量,也是不提升，同样存在暂时性死区。
      ```
      const foo;
      foo = 3;
      
      const PI = 3.1415;
      console.log('PI',PI);
    
      PI = 3;
      ```
  + const实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址所保存的数据不得改动。对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。但对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指向实际数据的指针，const只能保证这个指针是固定的（即总是指向另一个固定的地址），至于它指向的数据结构是不是可变的，就完全不能控制了。因此，将一个对象声明为常量必须非常小心。
      ```
      const foo = {};

      foo.prop = 123;
      foo.prop // 123
      console.log('foo', foo);
    
      foo = {};
      ```
+ ##### 变量的解构赋值
  + 数组的解构赋值
    只要等号两边的模式相同，左边的变量就会被赋予对应的值。如果解构不成功，变量的值就等于undefined。
    ```
    let [a, b, c] = [1, 2, 3];
    console.log('a',a);
    console.log('b',b);
    console.log('c',c);
    ```
    *事实上，只要某种数据结构具有 Iterator 接口，都可以采用数组形式的解构赋值。*
  + 默认值
    解构赋值允许指定默认值,ES6 内部使用严格相等运算符（===），判断一个位置是否有值。所以，只有当一个数组成员严格等于undefined，默认值才会生效。
    ```
    let [foo = true] = [];
    foo;
    
    let [x, y = 'b'] = ['a']; // x='a', y='b'
    let [x, y = 'b'] = ['a', undefined]; // x='a', y='b'
    let [x, y = 'b'] = ['a', null]; // x='a', y=null
    ```
  + 对象的解构赋值
    对象的解构与数组有一个重要的不同。数组的元素是按次序排列的，变量的取值由它的位置决定；而对象的属性没有次序，变量必须与属性同名，才能取到正确的值。
    ```
    let { bar, foo } = { foo: 'aaa', bar: 'bbb' };
    console.log('foo',foo);
    console.log('bar',bar);
    
    let { baz } = { foo: 'aaa', bar: 'bbb' };
    console.log('baz',baz);
    ```
    如果变量名与属性名不一致，必须写成下面这样。即为解构赋值。
    ```
    let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
    console.log('baz',baz);
    ```
  + 字符串、数值和布尔值也可以解构赋值
    字符串结构时被转换成了一个类似数组的对象，类似数组的对象都有一个length属性，因此还可以对这个属性解构赋值。
    数值和布尔值解构赋值时，则会先转为对象。
    ```
    let [a,b,c,d] = 'hello';
    console.log('a', a);
    console.log('b', b);
    console.log('c', c);
    console.log('d', d);
    
    let {length : len} = 'hello';
    console.log('len', len);
    
    let {toString: s1} = 123;
    console.log('s1', s1);
    console.log('Number prototype', s1 === Number.prototype.toString);

    let {toString: s2} = true;
    console.log('s2', s2);
    console.log('Boolean prototype', s2 === Boolean.prototype.toString);
    ```
    *解构赋值的规则是，只要等号右边的值不是对象或数组，就先将其转为对象。由于undefined和null无法转为对象，所以对它们进行解构赋值，都会报错。*
  + 函数参数的解构赋值
    函数的参数也可以使用解构赋值，与对象和数组的解构赋值同理，函数参数的解构也可以使用默认值，默认值生效的规则遵循严格匹配模式。
+ ##### 字符串的扩展
  + 字符的 Unicode 表示法
    ES6 加强了对 Unicode 的支持，允许采用\uxxxx形式表示一个字符，其中xxxx表示字符的 Unicode 码点。这种表示法只限于码点在\u0000~\uFFFF之间的字符。超出这个范围的字符，必须用两个双字节的形式表示。
    ```
    "\u0061"
    
    "\uD842\uDFB7"
    
    "\u20BB7"
    ```
  + 字符串的遍历器接口
     ES6 为字符串添加了遍历器接口，使得字符串可以被for...of循环遍历。
     ```
     for (let codePoint of 'foo') {
        console.log(codePoint)
     }
     ```
  + 模板字符串
    模板字符串（template string）是增强版的字符串，用反引号（`）标识。它可以当作普通字符串使用，也可以用来定义多行字符串，或者在字符串中嵌入变量。多用于字符串拼接。
    ```
    // 普通字符串
    `In JavaScript '\n' is a line-feed.`
    
    // 多行字符串
    `In JavaScript this is
     not legal.`
    
    console.log(`string text line 1
    string text line 2`);
    
    // 字符串中嵌入变量
    let name = "Bob", time = "today";
    `Hello ${name}, how are you ${time}?`
    ```
    *如果在模板字符串中需要使用反引号，则前面要用反斜杠转义。*
    *如果使用模板字符串表示多行字符串，所有的空格和缩进都会被保留在输出之中。*
+ ##### 函数的扩展
  + 函数参数可设置默认值，且能解构赋值默认值结合使用
    ```
    function foo({x, y = 5} = {}) {
      console.log(x, y);
    }
    ```
  + rest参数,ES6 引入 rest 参数（形式为...变量名），用于获取函数的多余参数，这样就不需要使用arguments对象了
    ```
    function add(...values) {
      let sum = 0;
    
      for (var val of values) {
        sum += val;
      }
    
      return sum;
    }
    
    add(2, 5, 3) // 10
    ```
  + 箭头函数
    ```
    var f = v => v;

    // 等同于
    var f = function (v) {
      return v;
    };
    
    var sum = (num1, num2) => num1 + num2;
    // 等同于
    var sum = function(num1, num2) {
      return num1 + num2;
    };
    ```
    *箭头函数与普通函数的this指向问题需要注意，箭头函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。*
+ ##### [Promise 对象](https://www.jianshu.com/p/1b63a13c2701)
###### 什么是Promise
  + Promise的含义，Promise不是ES6才出现的异步编程解决方案，ES6 将其写进了语言标准，统一了用法，原生提供了Promise对象。
    所谓Promise，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。从语法上说，Promise 是一个对象，从它可以获取异步操作的消息。Promise 提供统一的 API，各种异步操作都可以用同样的方法进行处理。
###### 为什么用Promise
  + 在JavaScript的世界中，所有代码都是单线程执行的。
由于这个“缺陷”，导致JavaScript的所有网络操作，浏览器事件，都必须是异步执行。异步执行可以用回调函数实现。但是回调很容易进入到回调地狱中，导致代码异常难读，结果就是问题可以解决，但是代码维护困难。如下：
  
    ```
    let requestA = (callback) => $.ajax({
      url:"xxx",
      success:(data)=>{
        console.log("requestA,a=",data);
        requestBByA(data,callback);
      }
    });
    let requestBByA = (a,callback) => $.ajax({
      url:"xxx",
      data:{a},
      success:(data)=>{
        console.log("requestBByA,b=",data);
        requestCByB(data,callback);
      }
    });
    let requestCByB = (b,callback) => $.ajax({
      url:"xxx",
      data:{b},
      success:(data)=>{
        console.log("requestCByB,c=",data);
        callback(data);
      }
    });

    function getC(){
      function callbackC(c){
        console.log('getC',c);
      }
      requestA(callbackC)
    }
    ```
  阅读起来，这回调一层层的嵌套下去，代码阅读以及理解起来就会变的非常困难。
##### 怎么用Promise
  + 那么如上所述的问题，我们使用Promise该怎么实现呢，如下：
  ```
    let requestA = () => new Promise((resolve,reject)=>{
      $.ajax({
        url:"xxx",
        success:(data)=>{
          console.log("requestA,a=",data);
          resolve(data);
        },
        error:(e)=>{
          console.error("requestA,error=",e);
          reject('requestA error');
        }
      });
    })
    let requestBByA = (a) => new Promise((resolve,reject)=>{
      $.ajax({
        url:"xxx",
        data:{a},
        success:(data)=>{
          console.log("requestBByA,b=",data);
          resolve(data);
        },
        error:(e)=>{
          console.error("requestBByA,error=",e);
          reject('requestBByA error');
        }
      });
    })
    let requestCByB = (b) => new Promise((resolve,reject)=>{
      $.ajax({
        url:"xxx",
        data:{b},
        success:(data)=>{
          console.log("requestCByB,c=",data);
          resolve(data);
        },
        error:(e)=>{
          console.error("requestCByB,error=",e);
          reject('requestCByB error');
        }
      });
    })

    function getC(){
      requestA()
      .then((data)=>requestBByA(data))
      .then((data)=>requestCByB(data))
      .then((data)=>{
          console.log('getC',c);
      })
      .catch((error)=>{
        console.error('getC error',error);
      })
    }
  ```
  如上，虽然整体的代码并没有减少甚至可能还变多了，但是每个请求之间的依赖关系可以看的一清二楚，逻辑读起来很简单。

##### Promise进阶
  Promise除了then、catch处理正常业务流、异常业务流之外，还有如下高级用法：
  + Promise.all
   执行所有Promise，都执行完毕就继续向下执行，如：
   ```
    let requestA = () => new Promise((resolve,reject)=>{
      $.ajax({
        url:"xxx",
        success:(data)=>{
          console.log("requestA,a=",data);
          resolve(data);
        },
        error:(e)=>{
          console.error("requestA,error=",e);
          reject('requestA error');
        }
      });
    })
    let requestB = () => new Promise((resolve,reject)=>{
      $.ajax({
        url:"xxx",
        success:(data)=>{
          console.log("requestB,b=",data);
          resolve(data);
        },
        error:(e)=>{
          console.error("requestB,error=",e);
          reject('requestB error');
        }
      });
    })

    function getAAndB(){
      Promise.all([requestA(),requestB()])
      .then((dataList)=>{
          console.log('getAAndB',dataList);
      })
      .catch((error)=>{
        console.error('getAAndB error',error);
      })
    }
  ```
  + Promise.race
  多个Promise，只要有一个执行完毕，就继续向下执行，如：
  ```
    let requestA = () => new Promise((resolve,reject)=>{
      $.ajax({
        url:"xxx",
        success:(data)=>{
          console.log("requestA,a=",data);
          resolve(data);
        },
        error:(e)=>{
          console.error("requestA,error=",e);
          reject('requestA error');
        }
      });
    })
    let requestB = () => new Promise((resolve,reject)=>{
      $.ajax({
        url:"xxx",
        success:(data)=>{
          console.log("requestB,b=",data);
          resolve(data);
        },
        error:(e)=>{
          console.error("requestB,error=",e);
          reject('requestB error');
        }
      });
    })

    function getAOrB(){
      Promise.race([requestA(),requestB()])
      .then((data)=>{
          console.log('getAOrB',data);
      })
      .catch((error)=>{
        console.error('getAOrB error',error);
      })
    }
  ```
  + Promise.resolve
  将普通函数改造成Promise函数的方法，如：
  ```
  function syncDoSomething(){
    let a=1,b=2;
    return a+b;
  }

  asyncDoSomething();
  ... ...
  ```
  ==>
  ```
  function asyncDoSomething(){
    let a=1,b=2;
    return Promise.resolve(a+b);
  }

  asyncDoSomething.then(()=>{
    ... ...
  })
  ```



#### [React生命周期](https://react.docschina.org/docs/react-component.html)
React设计的核心前提是，UI 只是把数据通过映射关系变换成另一种形式的数据，同样的输入必会有同样的输出，这有别于JQuery驱动dom的方式。然后你需要把 UI 抽象成多个隐藏内部细节，又可复用的函数，通过在一个函数中调用另一个或多个函数来实现复杂的 UI。每个组件（函数）都包含自己的“生命周期方法”，你可以重写这些方法，以便于在运行过程中特定的阶段执行这些方法。
#### React常见的生命周期如下：

+ ##### 挂载，当组件实例被创建并插入 DOM 中时，其生命周期调用顺序如下：
  + constructor()，构造函数仅用于以下两种情况：
    1. 通过给 this.state 赋值对象来初始化内部 state。
    2. 为事件处理函数绑定实例
  + static getDerivedStateFromProps()，不管原因是什么，都会在每次渲染前触发此方法，会在调用 render 方法之前调用，并且在初始挂载及后续更新时都会被调用。它应返回一个对象来更新 state，如果返回 null 则不更新任何内容。
    1. 使用场景为，state 的值在任何时候都取决于 props
  + render()，该方法是 class 组件中唯一必须实现的方法。当 render 被调用时，它会检查 this.props 和 this.state 的变化并返回以下类型之一：
    1. React 元素。通常通过 JSX 创建。例如，<div /> 会被 React 渲染为 DOM 节点，<MyComponent /> 会被 React 渲染为自定义组件，无论是 <div /> 还是 <MyComponent /> 均为 React 元素。
    2. 数组或 fragments。 使得 render 方法可以返回多个元素。欲了解更多详细信息，请参阅 fragments 文档。
    3. Portals。可以渲染子节点到不同的 DOM 子树中。欲了解更多详细信息，请参阅有关 portals 的文档
    4. 字符串或数值类型。它们在 DOM 中会被渲染为文本节点
    5. 布尔类型或 null。什么都不渲染。（主要用于支持返回 test && <Child /> 的模式，其中 test 为布尔类型。)
  + componentDidMount()，会在组件挂载后（插入 DOM 树中）立即调用。依赖于 DOM 节点的初始化应该放在这里。如需通过网络请求获取数据，此处是实例化请求的好地方。
+ ##### 更新，当组件的 props 或 state 发生变化时会触发更新。组件更新的生命周期调用顺序如下：
  + static getDerivedStateFromProps()同上
  + shouldComponentUpdate()，根据该函数返回值（默认为true）判断 React 组件的输出是否受当前 state 或 props 更改的影响。默认行为是props或是state每次发生变化组件都会重新渲染。首次渲染或使用 forceUpdate() 时不会调用该方法。此方法仅作为性能优化的方式而存在。
  + render()同上
  + getSnapshotBeforeUpdate()在最近一次渲染输出（提交到 DOM 节点）之前调用。它使得组件能在发生更改之前从 DOM 中捕获一些信息（例如，滚动位置）。此生命周期的任何返回值将作为参数传递给 componentDidUpdate()。
  + componentDidUpdate()该方法会在更新后会被立即调用，首次渲染不会执行此方法。
+ ##### 卸载，当组件从 DOM 中移除时会调用如下方法：
  + componentWillUnmount()，该方法会在组件卸载及销毁之前直接调用。在此方法中执行必要的清理操作

#### 组件化开发思想：
React 是一个声明式，高效且灵活的用于构建用户界面的 JavaScript 库。使用 React 可以将一些简短、独立的代码片段组合成复杂的 UI 界面，这些代码片段被称作“组件”。创建拥有各自状态的组件，再由这些组件构成更加复杂的 UI。组件逻辑使用 JavaScript 编写而非模版，因此你可以轻松地在应用中传递数据，并使得状态与 DOM 分离，组件都遵循功能单一原则。



#### [函数柯里化](jianshu.com/p/2975c25e4d71)
///下次分享
