
# 关于闭包

###  首先看下 sample1

> 闭包是运行时中的概念, 不能讲哪个函数是一个闭包, 而是哪个函数在运行时存在一个闭包. 有时候, 好几个函数都可以组成一个闭包 [来源](http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html#comment-222342)
> 
>     function ff() {
>         var local = 1;
>         this.add1 = function () {
>             return ++local;
>         };
>         this.add2 = function () {
>             return ++local;
>         }
>     }
>     
>     var f1 = new ff();
>     alert(f1.add1()); // 2
>     alert(f1.add2()); // 3
> 

// 个人感觉这种观点比较容易理解, 最重要的是 "闭包是运行时中的概念", 我们可以基于这个前提继续往下看
// 另外, 这里贴上 Mozilla 的开发者文档中对 "闭包" 的解释 (E 文, 感兴趣可以看一下): https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures

---

### 然后是 sample2

// 下面的demo用到了 JavaScript 的 "立即执行函数" 和 "闭包" 两个特性.

    var count = 100;
    while (!!count) {
        // 使用闭包的理由: 
        // 1.1 每次新的循环都会破坏上次循环中的 "上下文环境", 也就是 context. (count 被重新赋值)
        // 1.2 每次新的循环都会破坏上次循环中的 "外部引用". (count 被重新赋值)
        // 2. 当整个 while 循环执行完毕时, gc 会自动清理掉 "不再被引用的变量" 占掉的内存
        // 3. setTimeout 中 function 的执行是异步的
        //
        // 所以需要给每次循环加上闭包, 确保每次循环都会在内存中开辟出独立的上下文环境 (并且由于 count 被当作一个参数被传入),
        // 这样, 每次循环中给 setTimeout 的 function 执行时, 就都可以从自身的外部拿到正确的对应值了
        (function (ct) {
            var delay = 1000 * (101 - count);
            setTimeout(function () {
                debugger;
                console.log(ct)
            }, delay);
            count--;
        })(count);
    
        // 1 秒后 count == ct == 100
        // (function(){ debugger;console.log(ct); // 1 秒后在控制台输出 100 })()
        // 2 秒后 count == ct == 99
        // (function(){ debugger;console.log(ct); // 2 秒后在控制台输出 99 })()
        
    }

// PS: @manxisuo 解释了 "立即执行函数" 和 "闭包" 之间的区别和关联: http://segmentfault.com/q/1010000004140838

> Written with [StackEdit](https://stackedit.io/).