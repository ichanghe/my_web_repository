# 关于js中this的总结

this的指向问题一直是困扰初级前端开发者的难题，今天做一下对于知识点的总结

- **this的指向在函数定义的时候是确定不了的，只有函数执行的时候才能确定this到底指向谁，实际上this的最终指向的是那个调用它的对象**

换句话说就是：谁触发事件，函数里面的 this 指向的就是谁

## 1. 全局环境中调用

- 在全局环境中调用，返回window对象

- 在node模块中的this,指向module.export对象

## 2. 函数内部调用

#### 2.1 普通函数直接调用与 window 调用

```
console.log(this) //window
```

#### 2.2 对象中的函数直接调用与 window 调用

- 函数被多层对象所包含，如果函数被最外层对象调用，this 指向的也只是它上一级的对象

```
var o = {
    user:"helloword",
    fn:function(){
        console.log(this.user);
    }
}
o.fn() //helloword
```

- this最终指定的是调用的对象，在下面的情况实际上执行的是window.test()

```
var o = {
    user:"helloword",
    fn:function(){
        console.log(this.user);  
    },
    b:{
        fn:function(){
            console.log(this);
        }
    }
}
let test = o.b.fn
test();//window
```

- 构造函数中的 this 指向的是实例对象

```
    /*
    *   1. 调用函数
    *   2，自动创建一个对象
    *   3, 把创建出来的对象和this进行绑定
    *   4, 如果构造函数没有返回值，隐式返回this对象
    * */

    function fn(){
        this.num = 10;
        return null;
    }

    var obj = new fn();
    console.log(obj.num);
```

- 如果构造函数中有 return 如果 return 的值对象，this 指向返回的对象，如果不是对象，则 this 指向保持原来的规则，在这里，null 比较特殊，返回null，this还是指向函数的实例

```
function fn()  
{  
    this.user = 'hello world';  
    return {};  
}
var a = new fn;  
console.log(a.user); //结果为undefind 因为{}里没有user的键
```


```
function fn()  
{  
    this.user = 'hello world';  
    return 1;  
}
var a = new fn;  
console.log(a.user); //结果为helloworld 因为return 不是对象，那么保持原来的规则，
所以a = {user:'hello world'},所以a.user = 'hello world'

```

## 3. this指向的特殊性之箭头函数
箭头函数中的this的指向是根据上下文环境决定

## 4. 改变this指向
