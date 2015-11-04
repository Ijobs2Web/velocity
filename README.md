# velocity.js概述
velocity.js与jQuery的$.animate()API(应用程序接口，一些预定义的函数)一样是一种动画引擎。它可以与jQuery协同工作，也可以完全脱离jQuery来使用。对于动画的执行也是非常的迅速，性能远远优于jQuery的$.animate()方法。它的特性是集color animation,transforms, loops, easings,SVG support和scrolling于一体。总之，velocity.js是jQuery和css transforms的完美结合。

#velocity官方网址
http://www.julian.com/research/velocity/

#velocity.js的使用
下载velocity，引入到你的页面中，使用$.velocity()方法替换$.animate()方法，你会立即发现在浏览器和设备上velocity所带来的性能的提升，特别是在移动设备上带来的性能提升。

***将velocity.js（包括两个文件）引用的页面的方法：
   方法一：
   <script src='http://cdnjs.cloudflare.com/ajax/libs/jquery/2.1.3/jquery.min.js'></script>
   <script src='http://julian.com/research/velocity/build/jquery.velocity.min.js'></script>
   <script src='http://julian.com/research/velocity/build/velocity.ui.js'></script>
   方法二：
   下载后使用
   <script src="path/velocity.min.js"></script>
   <script src="path/velocity.ui.js"></script>
  
  #velocity.js的兼容性
  IE8+ \ Android2.3
  
#秘密武器
不能将Javascript和jQuery单纯的混为一谈。Javascript的动画实现非常的迅速，但jQuery中的$.animate()方法却不尽如人意。尽管velocity和jQuery协同工作，但是velocity使用自身的动画堆栈来提供其性能，其中也包括两个潜在的原则：1）同步DOM→渐变叠加最小化布局抖动 2）缓存值最小化DOM查询

#相关资源
推荐书籍链接http://amazon.com/Web-Animation-using-JavaScript-Develop/dp/0134096665
Demo链接https://www.youtube.com/watch?v=MDLiVB6g2NY&hd=1
        https://www.youtube.com/watch?v=CdwvR6a39Tg&hd=1
        http://codepen.io/collection/tIjGb/
        
#bugs解决方案
如果你在使用velocity.js的过程中遇到问题，您可以首先确认一下使用的是否是最新版本的velocity.js。并且可以在此（https://github.com/julianshapiro/velocity/issues/47）查看您遇到的问题是否已经解决。

#版本更新说明（三大更新）
1）自从velocity不再基于jQuery，jquery.velocity.min.js已经重命名为velocity.min.js。但如果你仍在和jquery一起使用velocity，那么你带代码无需做任何改变，velocity将像往常一样正常运作。
2）有关两个向后不兼容（backwards-incompatible）的改变：1、当访问元素传递到begin/complete/progress/promise回调函数时，必须遍历它们（例如：通过$.each()或.forEach()方法），因为在它们被传递之前被封装在一个数组之中。2、back, bounce和elastic easings被移除，使用spring physics来代替。
3）如果使用加载模块（eg： RequireJS 和 Browserify），要学习新的加载velocity的方法（方法见官网）

#velocity参数
$element.velocity({
    <!--参数一：css键值对（多个时用逗号分隔）-->
    width: "500px",
    property2: value2
}, {
    <!--参数二： Velocity's default options-->
    <!--Option defaults can be globally overriden by modifying $.Velocity.defaults.-->
    duration: 400,
    easing: "swing",
    queue: "",
    begin: undefined,
    progress: undefined,
    complete: undefined,
    display: undefined,
    visibility: undefined,
    loop: false,
    delay: false,
    mobileHA: true
});
***代替传递一个可选项对象（an options object)，velocity同时也支持逗号分隔（）语法，但仅仅是针对于 duration, easing和complete属性，具体语法形式如下： $element.velocity(propertyMap [, duration] [, easing] [, complete])
实例代码：
$element.velocity({ top: 50 }, 1000);
$element.velocity({ top: 50 }, 1000, "swing");
$element.velocity({ top: 50 }, "swing");
$element.velocity({ top: 50 }, 1000, function() { alert("Hi"); });

***propertyMap详细使用说明
1、自动添加css前缀功能（例如，transform becomes webkit-transform on WebKit browsers），无需自己添加
2、每个属性仅能对应一个单数值
{ padding: 1 }或者{ paddingLeft: 1, paddingRight: 1 }
但不支持以下写法：{ padding: "1 1 1 1" }
然而textShadow, boxShadow, clip, backgroundPosition和transformOrigin等属性也会打破以上的规则，可以对应混合数值

#有关easing
***velocity的easing的默认类型是swing

1)velocity支持的easing类型
/* Use one of the jQuery UI easings. */
$element.velocity({ width: 50 }, "easeInSine");
/* Use a custom bezier curve. */
$element.velocity({ width: 50 }, [ 0.17, 0.67, 0.83, 0.67 ]);
/* Use spring physics. */
$element.velocity({ width: 50 }, [ 250, 15 ]);
/* Use step easing. */
$element.velocity({ width: 50 }, [ 8 ]);

2）Per-Property Easing
也可以通过一个包含两项的数组来定义每一个的easing，该数组第一项是css属性对应的值，数组的第二项对应easing的类型。
$element.velocity({
    borderBottomWidth: [ "2px", "spring" ], // Uses "spring"
    width: [ "100px", [ 250, 15 ] ], // Uses custom spring physics
    height: "100px" // Defaults to easeInSine, the call's default easing
}, {
    easing: "easeInSine" // Default easing
});

3）自定义easing类型
$.Velocity.Easings.myCustomEasing = function (p, opts, tweenDelta) {
    return 0.5 - Math.cos( p * Math.PI ) / 2;
};
p:The call's completion percentage (decimal value).
opts (可选): The options object passed into the triggering Velocity call.
tweenDelta (可选): The difference between the animating property's ending value and its starting value.

#队列queue
可以通过将queue设置为false来迫使一个新的动画立即执行（默认情况下，动画会按照动画队列一次执行），而不是等到上一个动画完成才执行，从而实现同一个元素绑定的不同动画同步执行。
可见下列代码执行区别：
代码一：
$element.velocity({ width: "50px" }, { duration: 3000 });
$element.velocity({ height: "50px" }, { queue: false ，duration: 3000});
代码二：
$element.velocity({ width: "50px" }, { duration: 3000 });
$element.velocity({ height: "50px" }, { duration: 3000});

***自定义动画序列
用户可以通过对自定义动画序列命名的方式来调用该动画序列，调用方法如下：
1）$element.dequeue("queueName")
2）Velocity.Utilities.dequeue(element(s), "queueName"）----在不使用jQuery时，使用此方法
注意：loop选项和reverse命令不能使用于自定义和并行序列。

***velocity使用和jQuery相同的队列逻辑，而且其接口可$.animate()\$.fade()\$.delay()非常相似，想要学习更多velocity队列行为可查看一下链接http://stackoverflow.com/questions/1058158/can-somebody-explain-jquery-queue-to-me

#progress
$element.velocity({
    opacity: 0,
    tween: 1000 // Optional
}, {
    progress: function(elements, complete, remaining, start, tweenValue) {
        console.log((complete * 100) + "%");
        console.log(remaining + "ms remaining!");
        console.log("The current tween value is " + tweenValue)
    }
});
