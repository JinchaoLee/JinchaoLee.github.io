---
layout: post
title: JavaScript 单例模式
subtitle: 保证一个类仅有一个实例，并提供一个访问它的全局访问点。
tags: [JavaScript]
---
### 单例模式的定义

保证一个类仅有一个实例，并提供一个访问它的全局访问点。 即对于单例模式，一个对象无论被初始化多少次，其返回的都将是同一个实例对象，所有的实例都指向同一个地址。

### 为什么要使用单例模式？

单例模式是一种常用的模式，有一些对象我们往往只需要一个，比如线程池、全局缓存、浏 览器中的 window 对象等。在 JavaScript 开发中，单例模式的用途同样非常广泛。试想一下，当我 们单击登录按钮的时候，页面中会出现一个登录浮窗，而这个登录浮窗是唯一的，无论单击多少次登录按钮，这个浮窗都只会被创建一次，那么这个登录浮窗就适合用单例模式来创建。 

### 怎么实现单例呢？

对于单例模式的定义我们可能会产出如下代码：

```javascript
var CreateSingle = (function () {
    var instance = null;
    return function () {
        if (instance) {
            return instance;
        }
        this.init();
        return instance = this;
    }
})();
CreateSingle.prototype.init = function () {
    console.log("创建提示框")
}
var instance_1 = new CreateSingle();
var instance_2 = new CreateSingle();
console.log(instance_1 === instance_2);
```

可以肯定的是，我们确实实现了单例，但是我们这样做有什么问题呢？

​	1.对于JavaScript非完全面向对象的特性，使用构造函数创建实例的方式有些多余。

​	2.通过使用自闭包的方式创建构造函数，构造函数其实是内部返回的函数，可读性不强。

​	3.创建实例的逻辑（this.init）跟单例模式放在同一逻辑下，职责不单一，如过我们要创建一个其他的单例怎么办呢？有需要按照上面的代码重写一次，不利于维护。下面我们通过代理的方式来讲单例逻辑和业务逻辑区分开。

### 代理实现单例模式 

```javascript
var CreateInstance = function (html) {
    this.html = html;
    this.init();
}
CreateInstance.prototype.init = function () {
    console.log('创建弹出框');
}
var proxySingleCreateInstance = (function () {
    var instance;
    return function( html ){
        if ( !instance ){
            instance = new CreateInstance( html );
        }
        return instance; 
    }
})()
var instance_1 = proxySingleCreateInstance();
var instance_2 = proxySingleCreateInstance();
console.log(instance_1 === instance_2);
```

通过代理实现单例模式，确实解决了我们上面职责不单一的问题，但是仍然存在一个问题，我们创建单例的方式仍然使用了面向对象的方式，即对象是从类中创建出来的。但 JavaScript 其实是一门无类(class-free)语言，也正因为如此，生搬单例模式的概念并无意义。 

### >惰性单例

```Javascript
var getSingle = function (fn) {
    var instance = null;
    return function () {
        return instance || (instance = fn.apply(this, arguments));
    }
}
var createLoginView = function () {
    
    return "loginViewDOMElement"

}
var loginView = getSingle(createLoginView);
```



通过惰性单例，真正的解决了职责不单一问题，以及过于面向对象的问题，可作为实现单例的最优方式。





