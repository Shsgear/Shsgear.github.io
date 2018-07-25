---
title: evil setInterval与优化
date: 2018-07-18 18:11:41
categories: 
            - javascript
            - 优化
tags: 
      - javascript
      - setInterval优化
      - setTimeout代替setInterval
      - 定时器
      - 闭包closure
---
# 邪恶的setInterval

1. 不关心回调是否还在执行
     有些时候我们执行完函数的时间要比间隔时间稍长。假如我们需要每5s从服务器发起请求，网络延迟、服务器不响应和其他可变因素可能阻止请求不能按时完成。 那么请求队列可能就并不是按照顺序来返回的

2. 忽略执行函数时的错误
     setInterval调用函数时若函数体内发生错误，不会停止而是继续运行错误的代码

3. 不灵活
    有时候我们可能需要可配置的执行次数。但是setInterval不提供

<!-- more -->
<p data-height="265" data-theme-id="0" data-slug-hash="bjePbq" data-default-tab="js,result" data-user="Shsgear" data-embed-version="2" data-pen-title="邪恶的setInterval" class="codepen">See the Pen <a href="https://codepen.io/Shsgear/pen/bjePbq/">邪恶的setInterval</a> by Shsgear (<a href="https://codepen.io/Shsgear">@Shsgear</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>


解决:
优化后的interval，采用setTimeout

<p data-height="265" data-theme-id="0" data-slug-hash="rrLXQW" data-default-tab="js,result" data-user="Shsgear" data-embed-version="2" data-pen-title="setInterval optimize" class="codepen">See the Pen <a href="https://codepen.io/Shsgear/pen/rrLXQW/">setInterval optimize</a> by Shsgear (<a href="https://codepen.io/Shsgear">@Shsgear</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

``` javascript
// setInterval优化

const interval = (func, wait, times) => {
  let intervalInner = function (w, t){
    return function() {
      if (typeof t === 'undefined' || t--> 0) {
        setTimeout(intervalInner, w);
        try {
          func.apply(null);
        } catch(err) {
          throw err.toString();
        }
      }
    }
  }(wait, times)
    setTimeout(intervalInner, wait);
}
```

此处是func无参数调用。假如func有参数，那么需要改进下函数

``` javascript
const interval = (func, funcArgs, wait, times) => {
  let intervalInner = function (w, t){
    return function() {
      if (typeof t === 'undefined' || t--> 0) {
        setTimeout(intervalInner, w);
        try {
          if (funcArgs !== undefined && funcArgs.length > 0) {
            func.apply(null, [...funcArgs]);
          } else {
            func.apply(null);
          }
        } catch(err) {
          t = 0;
          throw err.toString();
        }
      }
    }
  }(wait, times)
    setTimeout(intervalInner, wait);
}
```
